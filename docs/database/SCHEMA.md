# Database Schema
## TaskFlow - Minimal Kanban Board

**Version:** 1.0 MVP  
**Date:** January 14, 2026  
**Database:** PostgreSQL (Supabase)

---

## Overview

Single table design for MVP. Cards are stored with their column assignment and position for ordering.

---

## Entity Relationship Diagram

```
┌─────────────────────────────────────┐
│              cards                   │
├─────────────────────────────────────┤
│ id          UUID PRIMARY KEY        │
│ title       TEXT NOT NULL           │
│ column      TEXT NOT NULL           │
│ position    INTEGER NOT NULL        │
│ created_at  TIMESTAMPTZ            │
│ updated_at  TIMESTAMPTZ            │
└─────────────────────────────────────┘
```

---

## Table: cards

### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | `uuid` | PRIMARY KEY, DEFAULT uuid_generate_v4() | Unique identifier |
| `title` | `text` | NOT NULL | Card title/content |
| `column` | `text` | NOT NULL, CHECK (column IN ('todo', 'in_progress', 'done')) | Which column the card belongs to |
| `position` | `integer` | NOT NULL, DEFAULT 0 | Order within column (lower = higher) |
| `created_at` | `timestamptz` | DEFAULT now() | When card was created |
| `updated_at` | `timestamptz` | DEFAULT now() | When card was last modified |

### Indexes

| Name | Columns | Purpose |
|------|---------|---------|
| `cards_pkey` | `id` | Primary key lookup |
| `cards_column_position_idx` | `column, position` | Fast column queries with ordering |

---

## SQL Migration

```sql
-- Enable UUID extension
create extension if not exists "uuid-ossp";

-- Create cards table
create table public.cards (
  id uuid primary key default uuid_generate_v4(),
  title text not null,
  "column" text not null check ("column" in ('todo', 'in_progress', 'done')),
  position integer not null default 0,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

-- Create index for efficient column queries
create index cards_column_position_idx on public.cards ("column", position);

-- Updated_at trigger
create or replace function update_updated_at()
returns trigger as $$
begin
  new.updated_at = now();
  return new;
end;
$$ language plpgsql;

create trigger cards_updated_at
  before update on public.cards
  for each row
  execute function update_updated_at();

-- Grant access (no RLS for MVP - public access)
alter table public.cards enable row level security;

-- Allow all operations for anon users (MVP only!)
create policy "Allow all for anon" on public.cards
  for all
  using (true)
  with check (true);
```

---

## Row Level Security (RLS)

### MVP Policy (No Auth)

For MVP, we allow full public access:

```sql
-- Anyone can read all cards
create policy "Public read" on public.cards for select using (true);

-- Anyone can insert cards
create policy "Public insert" on public.cards for insert with check (true);

-- Anyone can update cards
create policy "Public update" on public.cards for update using (true);

-- Anyone can delete cards
create policy "Public delete" on public.cards for delete using (true);
```

> ⚠️ **Warning**: This is insecure and only suitable for personal/demo use.

### Post-MVP Policy (With Auth)

When authentication is added:

```sql
-- Users can only access their own cards
create policy "Users own cards" on public.cards
  for all
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);
```

---

## Common Queries

### Fetch All Cards (Grouped by Column)

```sql
select * from cards order by "column", position;
```

### Create Card

```sql
insert into cards (title, "column", position)
values ('New task', 'todo', 0);
```

### Update Card Title

```sql
update cards 
set title = 'Updated title'
where id = 'uuid-here';
```

### Move Card to Different Column

```sql
update cards 
set "column" = 'in_progress', position = 0
where id = 'uuid-here';
```

### Delete Card

```sql
delete from cards where id = 'uuid-here';
```

### Reorder Cards in Column

```sql
-- Update positions after drag
update cards set position = 1 where id = 'card-1';
update cards set position = 2 where id = 'card-2';
update cards set position = 3 where id = 'card-3';
```

---

## Supabase Client Usage

```typescript
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
);

// Fetch all cards
const { data: cards } = await supabase
  .from('cards')
  .select('*')
  .order('position');

// Create card
const { data: newCard } = await supabase
  .from('cards')
  .insert({ title: 'New task', column: 'todo', position: 0 })
  .select()
  .single();

// Update card
await supabase
  .from('cards')
  .update({ column: 'done' })
  .eq('id', cardId);

// Delete card
await supabase
  .from('cards')
  .delete()
  .eq('id', cardId);
```

---

## Type Definitions

```typescript
type ColumnType = 'todo' | 'in_progress' | 'done';

interface Card {
  id: string;
  title: string;
  column: ColumnType;
  position: number;
  created_at: string;
  updated_at: string;
}
```

---

## Seed Data (Optional)

```sql
insert into cards (title, "column", position) values
  ('Set up project', 'done', 0),
  ('Design database schema', 'done', 1),
  ('Build UI components', 'in_progress', 0),
  ('Implement drag and drop', 'todo', 0),
  ('Deploy to Netlify', 'todo', 1);
```

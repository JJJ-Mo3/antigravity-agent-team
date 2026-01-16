# Implementation Plan
## TaskFlow - Minimal Kanban Board

**Version:** 1.0 MVP  
**Date:** January 14, 2026  
**Total Slices:** 8  
**Estimated Time:** 3-4 days

---

## Overview

This plan breaks the TaskFlow MVP into 8 small, focused slices that can be implemented sequentially. Each slice is 2-4 hours of work.

---

## Slice Summary

| ID | Slice | Priority | Est. | Dependencies |
|----|-------|----------|------|--------------|
| S1 | Project Setup | P0 | 1h | None |
| S2 | Supabase Setup | P0 | 1h | S1 |
| S3 | Board Layout | P0 | 2h | S1 |
| S4 | Display Cards | P0 | 2h | S2, S3 |
| S5 | Create Cards | P0 | 2h | S4 |
| S6 | Edit & Delete Cards | P0 | 2h | S5 |
| S7 | Drag and Drop | P0 | 3h | S6 |
| S8 | Polish & Deploy | P0 | 2h | S7 |

**Total Estimated:** ~15 hours (2-3 days)

---

## Dependency Graph

```
S1 (Project Setup)
├── S2 (Supabase Setup)
│   └── S4 (Display Cards)
│       └── S5 (Create Cards)
│           └── S6 (Edit & Delete)
│               └── S7 (Drag and Drop)
│                   └── S8 (Polish & Deploy)
└── S3 (Board Layout)
    └── S4
```

---

## Detailed Slices

---

### SLICE-001: Project Setup

**Priority:** P0  
**Estimate:** 1 hour  
**Dependencies:** None

#### Description
Initialize Next.js project with TypeScript and Tailwind CSS. Set up project structure and basic configuration.

#### Tasks
1. Create Next.js project with App Router
2. Configure Tailwind CSS
3. Set up folder structure (components, lib, hooks)
4. Create basic layout with header
5. Add Inter font

#### Acceptance Criteria
- [ ] `npm run dev` starts successfully
- [ ] Tailwind CSS working (test with colored div)
- [ ] Folder structure matches architecture doc
- [ ] Header displays "TaskFlow"

#### Files Created
- `src/app/layout.tsx`
- `src/app/page.tsx`
- `src/app/globals.css`
- `tailwind.config.ts`
- `src/components/Header.tsx`

---

### SLICE-002: Supabase Setup

**Priority:** P0  
**Estimate:** 1 hour  
**Dependencies:** S1

#### Description
Create Supabase project, set up database schema, and configure client.

#### Tasks
1. Create Supabase project (manual if not exists)
2. Run migration SQL to create cards table
3. Create Supabase client helper
4. Create TypeScript types
5. Add environment variables

#### Acceptance Criteria
- [ ] Cards table exists in Supabase
- [ ] RLS policies allow public access
- [ ] Supabase client connects successfully
- [ ] Types defined for Card

#### Files Created
- `src/lib/supabase.ts`
- `src/lib/types.ts`
- `.env.local` (from `.env.example`)
- `.env.example`

---

### SLICE-003: Board Layout

**Priority:** P0  
**Estimate:** 2 hours  
**Dependencies:** S1

#### Description
Create the static board layout with three columns. No data yet - just the UI structure.

#### Tasks
1. Create Board component
2. Create Column component
3. Style columns with design specs
4. Add column headers with colors
5. Make layout responsive

#### Acceptance Criteria
- [ ] Three columns display side by side
- [ ] Column headers show correct titles
- [ ] Column backgrounds match design (colors)
- [ ] Layout scrolls horizontally on mobile
- [ ] Matches design spec visually

#### Files Created
- `src/components/Board.tsx`
- `src/components/Column.tsx`
- `src/components/ColumnHeader.tsx`

---

### SLICE-004: Display Cards

**Priority:** P0  
**Estimate:** 2 hours  
**Dependencies:** S2, S3

#### Description
Fetch cards from Supabase and display them in the correct columns.

#### Tasks
1. Create useCards hook for data fetching
2. Create Card component
3. Fetch cards on page load
4. Group cards by column
5. Display cards in correct columns
6. Add loading state

#### Acceptance Criteria
- [ ] Cards fetch from Supabase on load
- [ ] Cards appear in correct columns
- [ ] Cards ordered by position
- [ ] Loading indicator shows while fetching
- [ ] Empty columns show correctly

#### Files Created
- `src/hooks/useCards.ts`
- `src/components/Card.tsx`

#### Files Modified
- `src/components/Board.tsx`
- `src/components/Column.tsx`

---

### SLICE-005: Create Cards

**Priority:** P0  
**Estimate:** 2 hours  
**Dependencies:** S4

#### Description
Add ability to create new cards in any column.

#### Tasks
1. Create AddCard component
2. Toggle between button and input states
3. Handle form submission
4. Insert card into Supabase
5. Optimistically update UI
6. Handle errors

#### Acceptance Criteria
- [ ] "+ Add a card" button shows in each column
- [ ] Clicking shows input field
- [ ] Enter submits, Escape cancels
- [ ] New card appears immediately (optimistic)
- [ ] Card persists after refresh

#### Files Created
- `src/components/AddCard.tsx`

#### Files Modified
- `src/hooks/useCards.ts` (add createCard)
- `src/components/Column.tsx`

---

### SLICE-006: Edit & Delete Cards

**Priority:** P0  
**Estimate:** 2 hours  
**Dependencies:** S5

#### Description
Add ability to edit card titles and delete cards.

#### Tasks
1. Make card title editable on click
2. Save on Enter or blur
3. Add delete button (shows on hover)
4. Confirm delete (optional) or immediate
5. Update/delete in Supabase
6. Handle errors

#### Acceptance Criteria
- [ ] Click card title to edit inline
- [ ] Enter saves, Escape cancels
- [ ] Delete button appears on hover
- [ ] Delete removes card immediately
- [ ] Changes persist after refresh

#### Files Modified
- `src/components/Card.tsx`
- `src/hooks/useCards.ts` (add updateCard, deleteCard)

---

### SLICE-007: Drag and Drop

**Priority:** P0  
**Estimate:** 3 hours  
**Dependencies:** S6

#### Description
Implement drag and drop to move cards between columns and reorder within columns.

#### Tasks
1. Install and configure @dnd-kit
2. Make cards draggable
3. Make columns drop targets
4. Handle drop events
5. Update card column and position in Supabase
6. Animate card movement

#### Acceptance Criteria
- [ ] Cards can be dragged
- [ ] Visual feedback while dragging
- [ ] Drop in different column changes card.column
- [ ] Cards reorder within column
- [ ] Changes persist after refresh
- [ ] Smooth animations

#### Files Modified
- `src/components/Board.tsx` (add DndContext)
- `src/components/Column.tsx` (add droppable)
- `src/components/Card.tsx` (add draggable)
- `src/hooks/useCards.ts` (add moveCard)

---

### SLICE-008: Polish & Deploy

**Priority:** P0  
**Estimate:** 2 hours  
**Dependencies:** S7

#### Description
Final polish, testing, and deployment to Netlify.

#### Tasks
1. Review all interactions
2. Add subtle animations/transitions
3. Test on mobile
4. Fix any bugs found
5. Deploy to Netlify
6. Verify production works

#### Acceptance Criteria
- [ ] All features work smoothly
- [ ] No console errors
- [ ] Works on mobile (responsive)
- [ ] Deployed to Netlify
- [ ] Production database connected
- [ ] README updated with live URL

#### Files Modified
- Various (bug fixes)
- `README.md`

---

## Execution Order

```
Day 1 (Morning):  S1 → S2 → S3
Day 1 (Afternoon): S4 → S5
Day 2 (Morning):  S6 → S7
Day 2 (Afternoon): S8
```

Or if working full days:

```
Day 1: S1 → S2 → S3 → S4
Day 2: S5 → S6 → S7
Day 3: S8 + buffer
```

---

## Ready to Start!

Begin with: **SLICE-001: Project Setup**

Run `/run-developer` and specify "SLICE-001" to start implementation.

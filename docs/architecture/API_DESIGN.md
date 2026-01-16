# API Design (Mock Interface)

Since this is a client-side POC, this document defines the **TypeScript Interfaces** and **Service Methods** that mimic the backend behavior.

## Core Types

```typescript
type ID = string;

interface Board {
  id: ID;
  title: string;
  lists: List[];
}

interface List {
  id: ID;
  title: string;
  cards: Card[];
}

interface Card {
  id: ID;
  title: string;
  description?: string;
  labels: Label[];
}

interface Label {
  id: ID;
  text: string;
  color: string;
}
```

## Service Methods (`StorageService`)

These methods will interact with LocalStorage.

#### Board Operations
*   `getBoard(): Board | null`
    *   Retrieves the stored board. Returns default empty board if none exists.
*   `saveBoard(board: Board): void`
    *   Persists the entire board state.

#### Atomic Updates (Handled by Store, persisted via saveBoard)
*   `addList(title: string)`
*   `deleteList(listId: ID)`
*   `addCard(listId: ID, card: Partial<Card>)`
*   `moveCard(cardId: ID, sourceListId: ID, destListId: ID, index: number)`
*   `updateCard(cardId: ID, updates: Partial<Card>)`

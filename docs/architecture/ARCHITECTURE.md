# System Architecture: Lightweight Trello Clone

## Executive Summary
This project is a single-page application (SPA) built with **React** and **Vite**. It is a **Proof of Concept (POC)** designed to demonstrate core Kanban functionality. To minimize complexity and infrastructure overhead, it uses **LocalStorage** for data persistence instead of a traditional backend, and performs all logic client-side.

## System Overview

```mermaid
graph TD
    User[User] -->|Interacts with| UI[React UI]
    UI -->|Dispatches Actions| Store[State Store (Zustand)]
    Store -->|Persists Data| Storage[LocalStorage Service]
    Storage -->|Loads Data| Store
```

## Front-End Architecture
*   **Framework:** React 18+ (via Vite)
*   **Language:** TypeScript
*   **Styling:** Vanilla CSS (CSS Modules or Global CSS with Variables for theming)
*   **State Management:** **Zustand**
    *   Selected for its simplicity and minimal boilerplate compared to Redux.
    *   Directly handles the "Store" of Boards, Lists, and Cards.
*   **Drag & Drop:** **@hello-pangea/dnd** (Fork of react-beautiful-dnd)
    *   Industry standard for React accessible drag-and-drop lists.

## Data Layer (Mocked Backend)
Since there is no real backend, we will implement a `DataService` interface that abstracts the data operations.
*   **Storage Key:** `trello_clone_data_v1`
*   **Format:** JSON stringified object containing the entire board state.
*   **Pattern:** Repository pattern to allow easy swapping for a real backend later.

## Component Structure
*   `App`: Main container.
*   `Board`: The canvas containing lists.
*   `List`: Vertical column of cards.
*   `Card`: Individual task item.
*   `CardModal`: Detailed view for editing.

## Directory Structure
```
src/
├── assets/
├── components/
│   ├── Board/
│   ├── List/
│   ├── Card/
│   └── common/ (Button, Input, Modal)
├── hooks/        # Custom hooks (useDragDrop)
├── store/        # Zustand store
├── services/     # LocalStorage adapter
├── types/        # TypeScript interfaces
├── utils/        # Helper functions
└── App.tsx
```

## Security Design
*   **Auth:** None (Public access to local browser session).
*   **Data Isolation:** Data is isolated to the user's specific browser/device.
*   **XSS Protection:** React's default escaping.

## Performance Considerations
*   **Optimistic UI:** State updates immediately on drag; persistence happens asynchronously/debounced if needed (though sync is fine for LocalStorage).
*   **Rendering:** React.memo for Boards and Lists to prevent unnecessary re-renders during drag operations.

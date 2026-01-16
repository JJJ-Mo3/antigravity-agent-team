# Component Specifications

## 1. Board Canvas
*   **Structure:** Horizontal scrolling container.
*   **Behavior:** Auto-scrolls when dragging card to edge.
*   **Background:** Full viewport height.

## 2. List
*   **Width:** Fixed 272px (Standard implementation).
*   **Max-Height:** Viewport - margins. Internal scroll for cards.
*   **Elements:** Header (Title + Menu), Card Area (Droppable), Footer (Add Card button).

## 3. Card
*   **State: Default:** White bg, light shadow, pointer cursor.
*   **State: Hover:** Slight background darkening (`#F4F5F7`).
*   **State: Dragging:** Rotate 2-3 degrees, scale 1.02, large shadow.
*   **Content:**
    *   Labels (Thin colored bars on top).
    *   Title (Multi-line text).
    *   Badges (Icons for description present, etc.).

## 4. Add Card Input
*   **Behavior:** Replaces "Add Card" button with Textarea + "Add" button + "X" button.
*   **Textarea:** Auto-expanding height.

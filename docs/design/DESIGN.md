# Design System & UI/UX

## Design Philosophy
**"Premium Utility"**
A clean, distraction-free interface that feels robust and professional. Generous whitespace, subtle shadows for depth, and fluid animations for interactions.

## Color Palette
*   **Primary Logic:** We avoid a single "brand color" and instead use neutral scales with semantic colors for labels.
*   **Backgrounds:**
    *   `bg-canvas`: `#F4F5F7` (Light Gray - Classic Trello feel but more modern)
    *   `bg-surface`: `#FFFFFF` (White - Cards/Modals)
    *   `bg-elevation`: `#EBECF0` (List Backgrounds)
*   **Text:**
    *   `text-primary`: `#172B4D` (Dark Blue-Gray)
    *   `text-secondary`: `#5E6C84` (Medium Gray)
*   **Accents (Labels):**
    *   Red: `#EB5A46` / `#FFD2CC` (bg)
    *   Green: `#61BD4F` / `#D4F6D4` (bg)
    *   Blue: `#0079BF` / `#E6FCFF` (bg)
    *   Yellow: `#F2D600` / `#FFF9D2` (bg)

## Typography
*   **Font Family:** `Inter`, system-ui, sans-serif.
*   **Scale:**
    *   `H1` (Board Title): 24px, Bold
    *   `H2` (List Title): 16px, SemiBold
    *   `Body`: 14px, Regular
    *   `Small`: 12px, Medium (Labels/Meta)

## Spacing & Depth
*   **Grid:** 4px baseline.
*   **Radius:**
    *   Cards: `6px`
    *   Lists: `12px`
    *   Buttons: `4px`
*   **Shadows:**
    *   Card Default: `0 1px 2px rgba(9, 30, 66, 0.25)`
    *   Card Dragging: `0 10px 20px rgba(9, 30, 66, 0.25)` (Large lift effect)

## Interactions
*   **Hover:** All interactive elements must have a distinct hover state (usually darkening bg or slight lift).
*   **Transitions:** `0.2s ease-in-out` for general UI.
*   **Drag & Drop:** Immediate feedback. Placeholder shows where it will drop.

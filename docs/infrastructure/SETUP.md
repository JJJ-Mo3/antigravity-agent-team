# Project Setup & Development Guide

## Prerequisites
*   Node.js 18+
*   npm 9+

## Local Development
1.  Navigate to the project directory:
    ```bash
    cd trello-clone
    ```
2.  Install dependencies:
    ```bash
    npm install
    ```
3.  Start the development server:
    ```bash
    npm run dev
    ```
    Access the app at `http://localhost:5173`.

## Environment Variables
Since this is a client-side POC with mocked data, no environment variables are strictly required.
However, we follow the `.env` pattern for future compatibility.

Copy `.env.example` to `.env` (Create one if it doesn't exist).

## Build for Production
```bash
npm run build
```
Preview the build:
```bash
npm run preview
```

## Directory Structure
*   `src/components`: UI Components.
*   `src/store`: Zustand state management.
*   `src/utils`: Helper functions.
*   `src/types`: TypeScript definitions.

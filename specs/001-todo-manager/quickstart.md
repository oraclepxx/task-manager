# Quickstart: Simple Personal Todo Manager

**Branch**: `001-todo-manager` | **Date**: 2026-03-01

Use this document to set up the project from scratch and verify each user story
works end-to-end. Run these steps after implementation is complete.

---

## Prerequisites

- Node.js ≥ 18 (LTS recommended)
- npm ≥ 9 (bundled with Node.js 18+)
- A modern browser (Chrome 110+, Firefox 110+, Safari 16+)

---

## 1. Project Setup

```bash
# Scaffold a new React + TypeScript + Vite project
npm create vite@latest todo-manager -- --template react-ts
cd todo-manager

# Install dependencies
npm install

# Install test dependencies
npm install -D vitest @vitest/ui @testing-library/react @testing-library/user-event @testing-library/jest-dom jsdom
```

Add to `vite.config.ts`:

```ts
/// <reference types="vitest" />
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['./src/test-setup.ts'],
  },
})
```

Create `src/test-setup.ts`:

```ts
import '@testing-library/jest-dom'
```

---

## 2. Run the Dev Server

```bash
npm run dev
```

Open `http://localhost:5173` in your browser.
The app should load with an empty task list and a light background.

---

## 3. Run Tests

```bash
# Run all tests once
npm test

# Run in watch mode (for TDD)
npm run test -- --watch

# Run with UI (optional, browser-based test viewer)
npm run test -- --ui
```

All tests MUST pass before any user story is considered complete.

---

## 4. Validate User Story 1 — Create and Complete Tasks

1. Open the app at `http://localhost:5173`.
2. Type "Buy milk" in the task input. Set priority to "Medium". Press Enter.
   - Expected: task appears with "~Med" badge and unchecked checkbox.
3. Add "Call dentist" without selecting a priority.
   - Expected: task appears with "~Med" badge (default Medium).
4. Add "Walk the dog" with priority "High".
   - Expected: task appears with "↑High" badge (red-tinted).
5. Check the "Buy milk" checkbox.
   - Expected: title shows strikethrough; checkbox is checked.
6. Uncheck it.
   - Expected: title returns to normal; checkbox is unchecked.
7. Try submitting an empty title.
   - Expected: inline error "Task title cannot be empty." appears; no task added.
8. Refresh the page.
   - Expected: all three tasks are still present with their priority badges and completion states.

---

## 5. Validate User Story 2 — Delete and Edit Tasks

1. Click the edit control on "Buy milk". Change the title to "Buy oat milk". Confirm.
   - Expected: task title updates to "Buy oat milk".
2. Refresh the page.
   - Expected: "Buy oat milk" persists.
3. Click delete on "Call dentist".
   - Expected: task is immediately removed.
4. Delete all remaining tasks.
   - Expected: empty-state message "No tasks yet — add one above." is displayed.

---

## 6. Validate User Story 3 — Filter and View Tasks

1. Add three tasks: "Task A" (Low), "Task B" (Medium), "Task C" (High).
2. Mark "Task A" and "Task B" as complete.
3. Click the "Active" filter tab.
   - Expected: only "Task C" is shown. Filter bar shows "Active (1)".
4. Click the "Completed" filter tab.
   - Expected: "Task A" and "Task B" are shown. Filter bar shows "Completed (2)".
5. Click "All".
   - Expected: all three tasks shown. Filter bar shows "All (3)".
6. While on "Active", add "Task D" (no priority selected).
   - Expected: "Task D" appears in the Active view immediately with "~Med" badge.

---

## 7. Validate Accessibility

- Tab through all interactive elements. Every action (add, check, delete, filter) must
  be reachable via keyboard alone.
- Verify that priority badges show both a symbol prefix and a text label (not color only).
- Use browser DevTools to check that the page background is white/light-gray — no dark
  backgrounds in any state.

---

## 8. Light Theme Check

Open DevTools → Elements. Confirm:

- `body` background: `#ffffff` or `#f5f5f5`
- No `prefers-color-scheme: dark` media query overrides to a dark palette
- No class names matching `dark:*` patterns

---

## File Structure After Implementation

```text
todo-manager/
├── src/
│   ├── components/
│   │   ├── TaskInput/
│   │   │   ├── TaskInput.tsx
│   │   │   └── TaskInput.test.tsx
│   │   ├── TaskItem/
│   │   │   ├── TaskItem.tsx
│   │   │   └── TaskItem.test.tsx
│   │   ├── TaskList/
│   │   │   ├── TaskList.tsx
│   │   │   └── TaskList.test.tsx
│   │   └── FilterBar/
│   │       ├── FilterBar.tsx
│   │       └── FilterBar.test.tsx
│   ├── hooks/
│   │   ├── useTasks.ts
│   │   └── useTasks.test.ts
│   ├── services/
│   │   ├── storage.ts
│   │   └── storage.test.ts
│   ├── types/
│   │   └── task.ts
│   ├── styles/
│   │   └── main.css
│   ├── test-setup.ts
│   ├── App.tsx
│   └── main.tsx
├── index.html
├── vite.config.ts
├── tsconfig.json
└── package.json
```

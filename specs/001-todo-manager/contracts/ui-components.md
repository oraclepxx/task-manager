# UI Component Contracts: Simple Personal Todo Manager

**Branch**: `001-todo-manager` | **Date**: 2026-03-01
**Phase**: 1 — Design

These contracts define the public interface (props), behaviour, and accessibility
requirements for each React component. They are technology-agnostic at the value
level; TypeScript types are illustrative of shape, not implementation mandate.

---

## Component: `TaskInput`

**Purpose**: Capture a new task's title and priority and submit it.

### Props

| Prop       | Type                          | Required | Description                          |
|------------|-------------------------------|----------|--------------------------------------|
| `onAdd`    | `(title: string, priority: Priority) => void` | Yes | Called when user submits a valid task |

### Behaviour

- Renders a text field for the title and a priority selector (Low / Medium / High).
- Default selected priority: Medium.
- On submit (Enter key or button click):
  - If title is empty or whitespace-only: display inline error "Task title cannot be empty." Do NOT call `onAdd`.
  - If title is valid: call `onAdd(trimmedTitle, selectedPriority)`, then clear the input and reset priority to Medium.
- The submit action MUST be accessible via keyboard alone (Enter on input, Space/Enter on button).

### Accessibility

- Text input: `aria-label="New task title"` (or visible label with `for` association).
- Priority selector: `aria-label="Task priority"`.
- Submit button: visible label "Add task".
- Error message: `role="alert"` so screen readers announce it immediately.

---

## Component: `TaskItem`

**Purpose**: Render a single task with its checkbox, priority badge, title, and delete/edit controls.

### Props

| Prop         | Type                               | Required | Description                              |
|--------------|------------------------------------|----------|------------------------------------------|
| `task`       | `Task`                             | Yes      | The task to display                      |
| `onToggle`   | `(id: string) => void`             | Yes      | Called when checkbox is toggled          |
| `onDelete`   | `(id: string) => void`             | Yes      | Called when delete is triggered          |
| `onEdit`     | `(id: string, newTitle: string) => void` | Yes | Called when title edit is confirmed  |

### Behaviour

- Renders a checkbox (reflecting `task.completed`), a priority badge, the task title,
  an edit control, and a delete button.
- Completed tasks: title renders with strikethrough; checkbox is checked.
- Edit flow: clicking the title (or an edit icon) switches the title to an editable
  input. Confirming (Enter or blur) calls `onEdit(task.id, newTitle)` if the new
  title is non-empty. Pressing Escape cancels the edit.
- Delete: clicking the delete button calls `onDelete(task.id)`.

### Priority Badge

Renders as an inline badge displaying the priority text label and a differentiating
prefix symbol:

| Priority | Label  | Prefix | Background | Text      |
|----------|--------|--------|-----------|-----------|
| High     | "High" | ↑      | `#FDDEDE` | `#7B1D1D` |
| Medium   | "Med"  | ~      | `#FEF3C7` | `#78350F` |
| Low      | "Low"  | ↓      | `#DCFCE7` | `#14532D` |

Badge MUST include both the prefix symbol and the text label. Color is NOT the
sole differentiator (WCAG 1.4.1).

### Accessibility

- Checkbox: `aria-label="Mark '{task.title}' as complete"` (or `aria-label="Mark '{task.title}' as incomplete"` when checked).
- Delete button: `aria-label="Delete task '{task.title}'"`.
- Edit input (when active): `aria-label="Edit task title"`.
- Priority badge: `aria-label="Priority: {task.priority}"`.

---

## Component: `TaskList`

**Purpose**: Render the filtered list of tasks or an appropriate empty state.

### Props

| Prop         | Type                               | Required | Description                              |
|--------------|------------------------------------|----------|------------------------------------------|
| `tasks`      | `Task[]`                           | Yes      | Pre-filtered list of tasks to display    |
| `filter`     | `'all' \| 'active' \| 'completed'` | Yes      | Currently active filter (for empty-state wording) |
| `onToggle`   | `(id: string) => void`             | Yes      | Forwarded to `TaskItem`                  |
| `onDelete`   | `(id: string) => void`             | Yes      | Forwarded to `TaskItem`                  |
| `onEdit`     | `(id: string, newTitle: string) => void` | Yes | Forwarded to `TaskItem`             |

### Behaviour

- Renders `TaskItem` for each task in `tasks` (ordered as received — caller applies sort).
- If `tasks` is empty:
  - `filter === 'all'`: "No tasks yet — add one above."
  - `filter === 'active'`: "No active tasks."
  - `filter === 'completed'`: "No completed tasks yet."

### Accessibility

- Wraps the list in a `<ul>` with `aria-label="Task list"`.
- Each `TaskItem` renders as an `<li>`.

---

## Component: `FilterBar`

**Purpose**: Display filter tabs (All / Active / Completed) with task count per tab
and allow the user to switch the active filter.

### Props

| Prop            | Type                                           | Required | Description                        |
|-----------------|------------------------------------------------|----------|------------------------------------|
| `activeFilter`  | `'all' \| 'active' \| 'completed'`             | Yes      | Currently selected filter          |
| `counts`        | `{ all: number; active: number; completed: number }` | Yes | Task count for each filter   |
| `onFilterChange`| `(filter: 'all' \| 'active' \| 'completed') => void` | Yes | Called on tab selection       |

### Behaviour

- Renders three tab buttons: "All (N)", "Active (N)", "Completed (N)".
- Active tab is visually highlighted (underline or background — light-theme palette only).
- Clicking a tab calls `onFilterChange` with the corresponding filter ID.

### Accessibility

- Rendered as `<nav aria-label="Filter tasks">` containing a `<ul role="tablist">`.
- Each tab: `role="tab"`, `aria-selected="true|false"`.
- Active tab: `aria-current="true"`.

---

## Component: `App` (Root)

**Purpose**: Compose all components, own task state and filter state, coordinate
`storage` service reads/writes.

### State

| State variable  | Type                                           | Initial value              |
|-----------------|------------------------------------------------|----------------------------|
| `tasks`         | `Task[]`                                       | Loaded from localStorage   |
| `activeFilter`  | `'all' \| 'active' \| 'completed'`             | `'all'`                    |

### Responsibilities

- On mount: load tasks from `storage.loadTasks()`.
- On every mutation (add/toggle/delete/edit): call the appropriate `storage` method.
- Derive filtered task list and counts before passing to children.
- Render: `TaskInput` → `FilterBar` → `TaskList`.

---

## Service: `storage`

**Purpose**: Abstract localStorage reads and writes. The UI layer MUST NOT access
`localStorage` directly.

### Interface

```ts
interface StorageService {
  loadTasks(): Task[];          // Returns [] on missing or parse error
  saveTasks(tasks: Task[]): void; // Throws StorageError on write failure
}
```

### Error Handling

- `loadTasks`: catches `JSON.parse` errors and `SecurityError`; returns `[]` and logs
  the error to the console.
- `saveTasks`: if `localStorage.setItem` throws (e.g., quota exceeded), MUST surface
  the error to the caller so the UI can display a meaningful error message (not silently
  discard the write).

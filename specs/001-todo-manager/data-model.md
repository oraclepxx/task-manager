# Data Model: Simple Personal Todo Manager

**Branch**: `001-todo-manager` | **Date**: 2026-03-01
**Phase**: 1 — Design

---

## Entities

### Task

The single persistent entity in the application.

| Field       | Type                              | Constraints                          |
|-------------|-----------------------------------|--------------------------------------|
| `id`        | string (UUID v4)                  | System-generated; unique; immutable  |
| `title`     | string                            | Non-empty after trimming; max 500 chars |
| `priority`  | `'Low' \| 'Medium' \| 'High'`    | Default: `'Medium'`                  |
| `completed` | boolean                           | Default: `false`                     |
| `createdAt` | string (ISO 8601 timestamp)       | System-generated; immutable          |

**TypeScript type**:

```ts
type Priority = 'Low' | 'Medium' | 'High';

interface Task {
  id: string;           // UUID v4
  title: string;        // trimmed, non-empty
  priority: Priority;   // default 'Medium'
  completed: boolean;   // default false
  createdAt: string;    // ISO 8601, e.g. "2026-03-01T10:00:00.000Z"
}
```

---

## Validation Rules

Applied before any create or update operation:

| Rule              | Condition                                       | Error message                        |
|-------------------|-------------------------------------------------|--------------------------------------|
| Title required    | `title.trim().length === 0`                     | "Task title cannot be empty."        |
| Title max length  | `title.trim().length > 500`                     | "Task title is too long (max 500)."  |
| Priority valid    | value not in `['Low', 'Medium', 'High']`        | "Invalid priority value."            |
| ID immutable      | `id` MUST NOT be modified after creation        | (enforce structurally, not via error)|
| createdAt immutable | `createdAt` MUST NOT be modified after creation | (enforce structurally)             |

---

## State Transitions

```
Task states:

  active (completed: false)
       │
       │  user checks checkbox
       ▼
  completed (completed: true)
       │
       │  user unchecks checkbox
       ▼
  active (completed: false)

  [created] ──────────────────► [deleted]
    (any state)                  (removed from list)
```

---

## Persistence Schema

Tasks are stored in localStorage under a single key:

```
Key:   "tasks"
Value: JSON array of Task objects

Example:
[
  {
    "id": "a1b2c3d4-...",
    "title": "Buy milk",
    "priority": "Medium",
    "completed": false,
    "createdAt": "2026-03-01T10:00:00.000Z"
  }
]
```

**Write strategy**: Full array replacement on every mutation. On read failure or
parse error, the storage service MUST return an empty array and log the error —
never propagate a corrupt state to the UI.

---

## Default Sort Order

Tasks are displayed in ascending creation order (oldest first, i.e. sorted by
`createdAt` ascending). No drag-to-reorder in this version.

---

## Filter Definitions

| Filter ID   | Displayed tasks                    |
|-------------|------------------------------------|
| `'all'`     | All tasks regardless of status     |
| `'active'`  | Tasks where `completed === false`  |
| `'completed'` | Tasks where `completed === true` |

Default active filter: `'all'`.

# Implementation Plan: Simple Personal Todo Manager

**Branch**: `001-todo-manager` | **Date**: 2026-03-01 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-todo-manager/spec.md`

## Summary

A single-page React + TypeScript web application that lets one user manage personal
tasks locally in their browser. Tasks have a title, a completion toggle, and a
Low / Medium / High priority indicator. Data is persisted in localStorage. No backend,
no auth, no cloud sync. The UI is light-theme only.

## Technical Context

**Language/Version**: TypeScript 5.x (strict mode), Node.js 18+ (dev tooling only)
**Primary Dependencies**: React 18, Vite 5, Vitest, React Testing Library
**Storage**: localStorage (single key `"tasks"`, full-array JSON replacement)
**Testing**: Vitest + @testing-library/react + @testing-library/user-event
**Target Platform**: Modern web browser (Chrome 110+, Firefox 110+, Safari 16+); no server
**Project Type**: Single-page web application (client-only)
**Performance Goals**: Task list renders ≤ 200 tasks without visible jank; no network latency
**Constraints**: Light theme only (no dark-mode variants); WCAG 2.1 AA; keyboard navigable
**Scale/Scope**: Single user, single device; ~200 tasks max; 3 components + 1 hook + 1 service

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-checked post Phase 1 design. ✅ ALL PASS*

| Principle | Status | Evidence |
|-----------|--------|---------|
| I. Simplicity First | ✅ PASS | localStorage (no backend); plain CSS (no CSS framework); no speculative features |
| II. Light Theme UI | ✅ PASS | Plain CSS custom properties enforce light palette structurally; no `prefers-color-scheme: dark` overrides to dark palette; no `dark:` classes |
| III. Test-First | ✅ PASS | Vitest + RTL; task order in tasks.md writes tests before implementation for each component/service |
| IV. Data Integrity | ✅ PASS | `saveTasks` propagates write errors; `loadTasks` returns `[]` on parse error (no silent data loss); atomic full-array replacement |
| V. Accessibility | ✅ PASS | `aria-label` on all interactive elements; priority badge uses text label + symbol (not color alone); keyboard nav for all primary actions |

## Project Structure

### Documentation (this feature)

```text
specs/001-todo-manager/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/
│   └── ui-components.md # Phase 1 output
└── tasks.md             # Phase 2 output (/speckit.tasks — not yet created)
```

### Source Code (repository root)

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

**Structure Decision**: Single project (Option 1 variant). No backend; all code lives
under `todo-manager/src/`. Component-per-directory layout collocates tests with source.

## Complexity Tracking

> No Constitution Check violations — table left empty.

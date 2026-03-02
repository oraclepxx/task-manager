# Research: Simple Personal Todo Manager

**Branch**: `001-todo-manager` | **Date**: 2026-03-01
**Phase**: 0 — Unknowns & Best Practices

---

## Decision 1: Frontend Framework

**Decision**: React (with Vite)

**Rationale**: React's ecosystem depth provides the most mature accessibility tooling
(ARIA primitives, RTL) and the largest pool of community resources. For a personal
single-developer project, the overhead of React's mental model (`useState`, `useEffect`)
is trivial at this scale. The reactivity requirement (live filter counts, task state
updates) makes vanilla HTML unnecessarily complex to maintain.

**Alternatives considered**:
- Svelte: Simplest syntax, smallest bundle. Ruled out — ecosystem for accessible UI
  patterns is significantly thinner than React's.
- Vue: Excellent docs, gentle learning curve. Ruled out — React's accessible component
  library ecosystem is more mature.
- Vanilla HTML: Eliminates build step. Ruled out — DOM wiring for reactivity (filter
  state, live task count) increases accidental complexity.

---

## Decision 2: Build Tool

**Decision**: Vite

**Rationale**: Create React App was officially deprecated and sunsetted by the React
team in February 2025. Vite is the React team's current recommendation for new
client-only SPAs. Shares configuration with Vitest (single `vite.config.ts`), starts
in under 2 seconds, zero ejecting required.

**Alternatives considered**:
- Create React App: Officially deprecated. Not a valid option for new projects.
- Plain HTML: Incompatible with JSX without a build step.

---

## Decision 3: Language

**Decision**: TypeScript (strict mode)

**Rationale**: The task data model has a well-defined, fixed schema with string enums
(`priority: 'Low' | 'Medium' | 'High'`, `status: 'active' | 'completed'`). TypeScript
catches the category of bugs most common in enum-heavy CRUD models at near-zero cost
— Vite scaffolds TypeScript with no additional build configuration.

**Alternatives considered**:
- Plain JavaScript: Marginally simpler to start. Ruled out — the fixed schema with
  enums is exactly the use case TypeScript provides the most value for.

---

## Decision 4: Local Persistence

**Decision**: localStorage

**Rationale**: 200 tasks × ~5 fields (id, title, priority, status, createdAt) ≈ 20–40 KB
as JSON — well within the 5 MB cross-browser minimum. localStorage is synchronous,
requires no async wrappers, and the API is two lines: `JSON.stringify` to write,
`JSON.parse` to read. No complex queries (no full-text search, no joins) are needed.

**Alternatives considered**:
- IndexedDB: Required for files/blobs, large datasets, or complex queries. All three
  conditions are absent here. Its async, cursor-based API is over-engineering for
  this scale and violates Constitution Principle I.

---

## Decision 5: CSS Approach

**Decision**: Plain CSS with custom properties

**Rationale**: A single stylesheet with `:root` CSS custom properties
(`--color-surface`, `--color-text`, `--badge-high-bg`, etc.) gives complete, auditable
control over every color. Light-theme enforcement is structural — there are no
`dark:` utility classes to accidentally add. No build-time dependency, no vocabulary
overhead.

**Alternatives considered**:
- Tailwind CSS v3: Light-theme enforcement is passive (omit `dark:` prefixes). Adds
  utility vocabulary learning curve and clutters JSX with long class strings —
  violates Simplicity First for a project with a small, fixed styling surface.
- CSS Modules: Scoped class names are valuable in large trees; unnecessary collision
  risk mitigation for ~6 components.

---

## Decision 6: Test Framework

**Decision**: Vitest + React Testing Library

**Rationale**: Vitest shares Vite's config — single `vite.config.ts` for dev and test,
no separate Babel setup, no ESM compatibility shims. API is Jest-compatible
(`describe`, `it`, `expect`). Runs 5–10× faster than Jest for React component suites,
which is meaningful for a TDD workflow. RTL pairs identically with either runner.

**Alternatives considered**:
- Jest: Established standard. Ruled out — requires separate Babel/ts-jest config in
  a Vite project and has known ESM friction. No advantage over Vitest here.

---

## Decision 7: Priority Badge Design

**Decision**: Dark text on muted light-tinted backgrounds + mandatory text labels

**Rationale**: WCAG 2.1 AA requires ≥ 4.5:1 contrast for normal text. Saturated
primary colors fail with white text. The solution: dark foreground text on desaturated
pastel backgrounds, each with a mandatory text label (WCAG 1.4.1 — color must not
be the sole differentiator).

**Starting palette** (verify with WebAIM Contrast Checker before shipping):

| Priority | Background | Text       | ~Ratio | Extra differentiator |
|----------|-----------|------------|--------|----------------------|
| High     | `#FDDEDE` | `#7B1D1D`  | ~7.5:1 | "↑" or "!" prefix    |
| Medium   | `#FEF3C7` | `#78350F`  | ~7.2:1 | "~" prefix           |
| Low      | `#DCFCE7` | `#14532D`  | ~8.1:1 | "↓" prefix           |

All three must be verified against the page background (`#ffffff` or `#f5f5f5`).

---

## Summary

| Concern   | Decision                        |
|-----------|---------------------------------|
| Framework | React                           |
| Build     | Vite                            |
| Language  | TypeScript (strict)             |
| Storage   | localStorage                    |
| CSS       | Plain CSS + custom properties   |
| Testing   | Vitest + React Testing Library  |
| Badges    | Dark text on light tints + labels |

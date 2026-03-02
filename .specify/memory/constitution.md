<!--
SYNC IMPACT REPORT
==================
Version change: (unversioned template) → 1.0.0
Modified principles: N/A (initial fill — all principles are new)
Added sections:
  - Core Principles (5 principles)
  - UI & Design Standards
  - Development Workflow
  - Governance
Templates reviewed:
  - .specify/templates/plan-template.md        ✅ reviewed — Constitution Check section present; no updates required
  - .specify/templates/spec-template.md        ✅ reviewed — no principle-driven mandatory sections to add
  - .specify/templates/tasks-template.md       ✅ reviewed — task categories align with principles
  - .specify/templates/agent-file-template.md  ✅ reviewed — generic enough; no updates required
Deferred TODOs:
  - TODO(TECH_STACK): Language/framework not yet chosen — update Principle IV once decided
  - TODO(RATIFICATION_DATE): Project newly initialized; today's date used as ratification date
-->

# Task Manager Constitution

## Core Principles

### I. Simplicity First

Every feature MUST start from the simplest solution that satisfies the requirement.
No feature, abstraction, or dependency may be added speculatively for future needs.
Complexity MUST be explicitly justified and documented in the Complexity Tracking table
of the relevant plan.md before it is introduced.

**Rationale**: Task management tools fail users by accumulating unnecessary complexity.
Keeping scope tight ensures the tool remains fast and comprehensible.

### II. Light Theme UI (NON-NEGOTIABLE)

The application MUST use a light theme exclusively. Dark theme is prohibited.
All UI components — backgrounds, text, borders, icons, and interactive states — MUST
be styled for light backgrounds. No dark-mode CSS classes, media queries, or theme
toggles may be shipped.

**Rationale**: Explicit user requirement. Deviating requires a formal constitution
amendment, not an ad-hoc implementation decision.

### III. Test-First Development

Tests MUST be written and confirmed to fail before the corresponding implementation
is written (Red phase). Implementation MUST then make tests pass (Green phase).
Refactoring occurs only in the Green phase or after. No feature is considered complete
until its tests pass.

**Rationale**: Prevents shipping untested code and ensures requirements are captured
as executable specifications from the start.

### IV. Data Integrity

Task data MUST be reliably persisted and recoverable. Operations that mutate task
state (create, update, delete) MUST be atomic or provide clear rollback behavior.
No task data may be silently lost or corrupted by an error path.

**Rationale**: A task manager that loses data is worse than no task manager. Data
reliability is a fundamental contract with the user.

### V. Accessibility

All UI MUST meet WCAG 2.1 AA standards as a minimum. Interactive elements MUST have
accessible labels. Color MUST NOT be the sole means of conveying information.
Keyboard navigation MUST be supported for all primary workflows.

**Rationale**: Accessibility is a correctness requirement, not an enhancement.
Features that exclude users with disabilities are incomplete.

## UI & Design Standards

The light theme requirement (Principle II) establishes the following concrete rules:

- Background colors: white or light-gray palette only (e.g., `#ffffff`, `#f5f5f5`)
- Text: dark-on-light contrast ratio MUST be ≥ 4.5:1 (WCAG AA)
- Component library selection MUST default to light-theme defaults; no dark variants
- System `prefers-color-scheme: dark` media queries MUST be explicitly overridden
  to maintain light appearance

TODO(TECH_STACK): Once the UI framework/library is chosen, add framework-specific
style enforcement rules here (e.g., Tailwind config, CSS variable overrides).

## Development Workflow

- **Branching**: Feature branches named `###-feature-name`; no direct commits to `main`
- **Spec-first**: A `/speckit.specify` spec MUST exist and be approved before any
  implementation task is created
- **Plan-before-implement**: A plan.md MUST pass Constitution Check before coding begins
- **Commit discipline**: Each task (T###) SHOULD map to one logical commit; commit
  message MUST reference the task ID
- **Review gate**: All PRs MUST verify that Principle II (light theme) and
  Principle III (test-first) are satisfied before merge

## Governance

This constitution supersedes all other development guidelines. Any practice that
contradicts a principle stated here is non-compliant.

**Amendment procedure**:
1. Open a proposal describing the change and its rationale
2. Update this file with incremented version (follow semantic versioning below)
3. Run `/speckit.constitution` to propagate changes to dependent templates
4. Record the amendment in a git commit referencing the new version

**Versioning policy**:
- MAJOR: A principle is removed or its non-negotiable constraint is weakened
- MINOR: A new principle or section is added, or existing guidance is materially expanded
- PATCH: Wording clarifications, typo fixes, non-semantic refinements

**Compliance review**: Every plan.md Constitution Check section MUST be re-evaluated
against this file. Non-compliant complexity MUST be recorded in the Complexity
Tracking table with justification.

**Version**: 1.0.0 | **Ratified**: 2026-03-01 | **Last Amended**: 2026-03-01

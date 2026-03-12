# Task Generation — Principles and Parallel Analysis

Task generation translates `design.md` into actionable task files. Each task is a
self-contained unit of work that a developer (or agent) can pick up and complete
independently.

## Core Principles

### 1. Natural Language Descriptions
The **What** section describes functionality and workflows in plain language. Do NOT
specify file paths, function names, or class names in What — those belong in the How
and Files sections.

- **Good:** "Create the data access layer for storing and retrieving user preferences."
- **Bad:** "Create `user_preferences_crud.py` with `get_by_user_id()` and `update()` methods."

### 2. Task Progression
Tasks should be ordered so that each builds on completed predecessors. A common pattern
is bottom-up (data layer, then business logic, then interface), but the actual ordering
depends on the project's architecture. Choose the sequence that minimizes blocked work.

### 3. Flexible Sizing
Target 1-3 hours of focused work per sub-task:

| Size | Scope | Typical Files |
|------|-------|---------------|
| **S** | Single concern, one layer | 1-2 files |
| **M** | Feature slice across a few layers | 3-5 files |
| **L** | Cross-cutting or multi-component | 5+ files |

If a task exceeds **L**, split it. If every task is **S**, consider merging related ones
to reduce overhead.

### 4. Requirements Mapping
Every task should reference the requirement IDs it satisfies, and every requirement from
`requirements.md` should appear in at least one task — this is how you verify nothing
was missed. Include a coverage table at the end of the task set (see below).

### 5. Code-Only Focus
Tasks cover coding and testing work. Exclude non-code activities — tasks should describe
what to build and how to verify it, nothing else.

### 6. Parallel Analysis
Mark tasks that can run concurrently with `[P]` in the task list. Two tasks are parallel
only when they share no data dependencies, modify no common files, and do not depend on
each other's runtime output.

**Validation before marking [P]:** Trace data flow, file overlap, and schema overlap
between the two tasks. If any overlap exists, the tasks are sequential.

## Task File Format

Each task file uses the template at `${CLAUDE_PLUGIN_ROOT}/templates/task.md`:

```markdown
# Task <N>: <Title>

> Status: pending | in-progress | done | blocked
> Size: S | M | L
> Depends on: <task numbers or none>
> Requirements: <requirement IDs>

## What
<Natural language description — behavior, inputs, outputs, user-visible effects.>

## How
<Technical approach — patterns to follow, code to extend, interfaces to implement.>

## Files
<List of files to create or modify.>

## Acceptance
<Concrete, verifiable checks. Include "project check command passes clean" as a standard item.>

## Notes
<Filled during implementation. Edge cases, deviations, discoveries.>
```

## Requirements Coverage Table

After all tasks are defined, include a traceability table:

| Requirement | Task(s)    | Status  |
|-------------|------------|---------|
| 1.1         | Task 1     | covered |
| 1.2         | Task 1, 3  | covered |
| 2.1         | Task 2     | covered |
| 3.1         | —          | MISSING |

Any requirement with status **MISSING** is a generation failure. All requirements must
be covered before the task set is considered complete.

## Task Ordering Example

For a typical feature that adds a new entity with business logic and an interface:

1. **Task 1 [P]** — Data model and persistence layer
2. **Task 2 [P]** — Supporting service or utility (if independent)
3. **Task 3** — Business logic layer (depends on Task 1)
4. **Task 4** — Interface / API layer (depends on Task 3)
5. **Task 5** — Integration tests across layers (depends on Task 3, 4)

This is an example pattern, not a mandatory ordering. Adapt to the project's architecture
and the feature's dependency graph.

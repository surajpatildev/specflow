# Phase: Tasks

Decompose the approved design into ordered, implementable task files.

## Prerequisites

Check `spec.json` before proceeding:

- `requirements.generated` must be `true` AND `requirements.approved` must be `true`
- `design.generated` must be `true` AND `design.approved` must be `true`

If any approval is `false` and the user passed `-y`, set the missing approvals to `true` in spec.json and proceed. Otherwise, stop and tell the user what needs approval first.

## Phase-Specific Context

Load these in addition to shared context from SKILL.md:

- `.agents/specs/<feature>/requirements.md`, `design.md`, `research.md` (if exists), `gap-analysis.md` (if exists)
- `.agents/reference/tech-stack.md` (if exists) — for directory structure conventions
- `${CLAUDE_PLUGIN_ROOT}/skills/spec/rules/task-generation.md` — decomposition principles
- `${CLAUDE_PLUGIN_ROOT}/skills/spec/rules/self-review.md` — subagent review pattern
- `${CLAUDE_PLUGIN_ROOT}/templates/task.md` — task file template

## Procedure

### 1. Analyze the Design

Read the design document and identify:

- All new components to create
- All existing components to modify
- The dependency graph between components
- The natural implementation boundaries

### 2. Determine Task Order

Order tasks so each builds on completed predecessors. Dependencies must be explicit.

For projects with layered architectures (e.g., model -> service -> route), a bottom-up order is often natural:

1. Data models / schemas
2. Business logic / services
3. API layer / routes / UI
4. Integration and wiring
5. Tests and validation

This is a guideline, not a rule. Use whatever order makes sense for the project's architecture (per tech-stack.md if available, otherwise infer from the codebase).

### 3. Size Tasks

Each task should be completable in a single implementation session:

| Size | Files Changed | Description |
|------|--------------|-------------|
| **S** | 1-2 files | Single component, straightforward |
| **M** | 3-5 files | Multiple components, some coordination |
| **L** | 5+ files | Cross-cutting — consider splitting |

If a task is size L, look for a natural split point. Tasks should cross as few layer boundaries as possible.

### 4. Write Task Files

Create numbered files in `.agents/specs/<feature>/tasks/`:

```
tasks/
  01-verb-noun.md
  02-verb-noun.md
  03-verb-noun.md
  ...
```

Naming convention: `NN-verb-noun.md` (e.g., `01-create-data-models.md`, `02-add-service-logic.md`).

Each task file follows the template at `${CLAUDE_PLUGIN_ROOT}/templates/task.md`. Key sections:

- **Title** — Short, action-oriented (e.g., "Create user notification model")
- **What** — Capabilities in natural language, no code details
- **How** — Implementation approach, referencing existing patterns by name
- **Files** — Explicit paths with create/modify annotations
- **Acceptance** — Observable, verifiable criteria (include "check command passes" as a standard item)
- **Notes** — Empty; filled during implementation

### 5. Validate Coverage

Map every AC to at least one task. Present the traceability and confirm 100% coverage. If any AC is missing, add or expand tasks.

### 6. Update spec.md

Add the task index table to `spec.md` with columns: #, Task, Size, Depends, Requirements, Status (all `pending`). Match the format in the spec.md template.

### 7. Self-Review

Before presenting to the user, dispatch a review subagent following the pattern in `${CLAUDE_PLUGIN_ROOT}/skills/spec/rules/self-review.md`. Pass:

- **Artifact:** All task files in `.agents/specs/<feature>/tasks/`
- **Inputs:** `.agents/specs/<feature>/requirements.md`, `.agents/specs/<feature>/design.md`
- **Phase:** `tasks`

Fix any issues the reviewer finds (broken dependencies, missing AC coverage, oversized tasks, inconsistent naming). Then proceed.

### 8. Update spec.json

Set:
- `phase`: `"tasks-generated"`
- `tasks.generated`: `true`
- `updated_at`: today's date

Do NOT set `tasks.approved` — that requires user review.

### 9. Present for Review

Show the task list and quality checklist: every AC mapped, valid dependency DAG (no cycles), all tasks S or M size, file paths match project structure, implementation steps self-contained, minimal layer-boundary crossings.

Ask the user to review. On approval, set `tasks.approved: true` in spec.json. On change requests, revise and re-present.

## Context Management Warning

After task generation, warn:

```
Context may be heavy after this phase. Start a fresh conversation
before running /spec impl <feature> to ensure clean implementation.
```

## Next Step

After approval, suggest:

```
Tasks approved. Ready for implementation.
Start a fresh conversation, then run: /spec impl <feature>
```

## Rules

- Tasks are individual files, not checkboxes in spec.md (spec.md has the index, tasks/ has the detail)
- Never combine unrelated changes in a single task
- Every task must have at least one testable acceptance criterion
- Task files are living documents — the impl phase updates them with notes and status
- Suggest `feat(<feature-slug>): task N - <description>` as the commit convention (conventional commits format)

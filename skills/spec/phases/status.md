# Phase: Status

Report the current state of one or all feature specs. This phase is read-only — it never modifies files.

## Phase-Specific Context

If a specific feature is named, load:
- `.agents/specs/<feature>/spec.json`
- All task files in `.agents/specs/<feature>/tasks/`

If no feature is named, scan `.agents/specs/` for all spec directories.

## Procedure

### 1. Find Specs

**Specific feature**: Look for `.agents/specs/<feature>/spec.json`. If it doesn't exist, report that no spec was found and suggest `/spec init`.

**All specs (no argument)**: List all directories under `.agents/specs/` that contain a `spec.json`. If none exist, report that and suggest `/spec init`.

### 2. Read State

For each spec, read:

- **spec.json** — phase, approval flags, description, dates
- **Task files** — count by status (pending, in-progress, done, blocked), read Notes sections for recent activity

Also check global state:
- Current git branch
- Recent commits on the current branch (last 5)

### 3. Generate Report

Present a structured status report:

```
## Spec: <feature-slug>

Description: <description>
Phase: <phase>
Created: <date>  |  Updated: <date>

### Approvals

| Stage | Generated | Approved |
|-------|-----------|----------|
| Requirements | yes/no | yes/no |
| Design | yes/no | yes/no |
| Tasks | yes/no | yes/no |

### Tasks

| # | Task | Size | Depends | Requirements | Status |
|---|------|------|---------|-------------|--------|
| 01 | <title> | S | — | 1.1 | done |
| 02 | <title> | M | 01 | 1.2, 2.1 | in-progress |
| 03 | <title> | M | 01, 02 | 2.2 | pending |

Summary: N done, M in-progress, P pending (of T total)

### Next Eligible Task

Task NN: <title>
  Dependencies: all met
  Size: S/M
```

If reporting on multiple specs, show a summary table first:

```
| Feature | Phase | Tasks | Next Action |
|---------|-------|-------|------------|
| user-notifications | tasks-generated | 0/5 done | Review and approve tasks |
| csv-export | design-generated | — | Review and approve design |
```

### 4. Identify Patterns

Look for potential issues:
- Tasks stuck in `in-progress` (check git log for last related commit)
- Missing approvals that are blocking progress
- Specs that were initialized but never advanced

Report any findings.

### 5. Suggest Next Actions

Based on the current state, suggest the appropriate next command:

| State | Suggestion |
|-------|-----------|
| `initialized` | `/spec requirements <feature>` |
| `requirements-generated`, not approved | Review and approve requirements |
| `requirements-generated`, approved | `/spec gap <feature>` or `/spec design <feature>` |
| `design-generated`, not approved | Review and approve design |
| `design-generated`, approved | `/spec tasks <feature>` |
| `tasks-generated`, not approved | Review and approve tasks |
| `tasks-generated`, approved | `/spec impl <feature>` |
| All tasks done | Feature complete — consider cleanup |
| `in-progress` tasks exist | `/spec impl <feature>` to continue |

### 6. Branch Context

If a branch is set in spec.json, or the current git branch appears related to the feature:

```
Branch: <branch-name>
  Commits since main: N
  Latest: <commit message>
```

## Rules

- Status is strictly read-only — never modify any files
- Always show the next actionable step
- If spec.json is malformed or missing fields, report the issue and suggest manual inspection
- Show task dependencies so the user can understand what's blocking
- When reporting on all specs, sort by most recently updated

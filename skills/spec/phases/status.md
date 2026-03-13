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

The report should feel like a dashboard — scannable at a glance, with visual cues that draw attention to what matters. Use rendered markdown (not code blocks) so the user gets proper formatting.

#### Multi-spec overview (when no feature is specified)

If reporting on multiple specs, lead with a summary table sorted by most recently updated:

```markdown
## All Specs

| Feature | Phase | Progress | Next Action |
|---------|-------|----------|-------------|
| **user-notifications** | `tasks-generated` | 2/5 done | `/spec impl user-notifications` |
| **csv-export** | `design-generated` | — | Review and approve design |
| **auth-refresh** | `initialized` | — | `/spec requirements auth-refresh` |
```

Then show the detailed report for each spec below.

#### Single-spec report

Use this structure. Render as real markdown — not inside code fences.

**Header block** — feature name as heading, key metadata on one line:

```markdown
## user-notifications

> Online notification system for user activity alerts

**Phase:** `tasks-generated`  \|  **Created:** 2026-02-10  \|  **Updated:** 2026-03-12
```

**Approval pipeline** — use checkmarks and crosses for instant scanning:

```markdown
### Approvals

| Stage | Generated | Approved |
|-------|:---------:|:--------:|
| Requirements | ✅ | ✅ |
| Design | ✅ | ✅ |
| Tasks | ✅ | ❌ |
```

**Task breakdown** — status indicators make each row scannable without reading the status column:

```markdown
### Tasks

| # | Task | Size | Depends | Reqs | Status |
|--:|------|:----:|---------|------|--------|
| 01 | Create notification model | S | — | 1.1 | ✅ done |
| 02 | Add WebSocket channel | M | 01 | 1.2, 2.1 | 🔄 in-progress |
| 03 | Build notification feed UI | M | 01, 02 | 2.2 | ⏳ pending |
| 04 | Implement read/unread toggle | S | 03 | 2.3 | ⏳ pending |
| 05 | Add email fallback | M | 01 | 3.1 | 🚫 blocked |

**Progress: 1/5 done** · 1 in-progress · 2 pending · 1 blocked
```

**Next eligible task** — highlight what the user can act on right now:

```markdown
### Next Up

> **Task 03 — Build notification feed UI**
> Size: M · Dependencies: all met
```

If no task is eligible (all remaining tasks are blocked), say so and explain what's blocking.

### 4. Identify Patterns

Scan for issues that might not be obvious from the raw data:
- Tasks stuck in `in-progress` — check git log for last related commit and report staleness
- Missing approvals blocking forward progress
- Specs initialized but never advanced past `initialized`

Report findings as a warnings section when issues exist:

```markdown
### Warnings

- Task 02 has been `in-progress` with no related commits for 5 days
- Design approval is missing — blocking task generation
```

Omit this section entirely if there are no issues. Don't report "no warnings found."

### 5. Suggest Next Actions

End the report with a clear, actionable next step based on current state:

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

Present the suggestion as a direct recommendation, not just a table lookup:

```markdown
### Suggested Next Step

Tasks are approved with 1 task in-progress. Continue implementation:
`/spec impl user-notifications`
```

### 6. Branch Context

If a branch is set in spec.json, or the current git branch appears related to the feature, append branch info:

```markdown
### Branch

**`feat/user-notifications`** · 12 commits ahead of main
Latest: `feat(notifications): task 02 - add WebSocket channel`
```

## Rules

- Status is strictly read-only — never modify any files
- Always show the next actionable step
- If spec.json is malformed or missing fields, report the issue and suggest manual inspection
- Show task dependencies so the user can understand what's blocking
- When reporting on all specs, sort by most recently updated
- Render the report as real markdown — never wrap the entire output in a code fence
- Omit sections that have no content (e.g., skip Warnings if there are none, skip Branch if no branch context exists)

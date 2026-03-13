# Phase: Implementation

Execute a task from the approved task list by writing code, verifying it, and documenting completion.

## Prerequisites

Check `spec.json` before proceeding:

- `tasks.approved` must be `true`

There is no `-y` override for this gate. Tasks must be explicitly approved before implementation begins. If not approved, stop and tell the user.

## Phase-Specific Context

Load these in addition to shared context from SKILL.md:

- `.agents/specs/<feature>/spec.json`, `design.md`, `research.md` (if exists), `gap-analysis.md` (if exists)
- The specific task file(s) being implemented
- The project's `CLAUDE.md` or `AGENTS.md` (if exists) and `.agents/reference/tech-stack.md` (if exists)

## Procedure

### 1. Select Task

If the user specified task number(s), use those. Otherwise, find the next eligible task:

- Read all task files in `.agents/specs/<feature>/tasks/`
- Find the first task with status `pending` whose dependencies are all `done`
- If no task is eligible (all blocked or all done), report the situation and stop

If the user specified multiple task numbers, validate that their dependency order is respected and implement them sequentially.

### 2. Claim and Implement

Read the task file, then set its status to `in-progress`. If this is the first task being implemented (phase is still `tasks-generated`), update spec.json: set `phase` to `"in-progress"`. Follow the task's How section step by step:

- Implement each numbered step in the How section in order
- Match the key interfaces defined in How — these were approved by the user during task review
- Create or modify the files listed in the Files section
- Follow the project's architecture patterns (per tech-stack.md and CLAUDE.md/AGENTS.md if available, otherwise infer from existing code)
- Read existing files before modifying them to match surrounding code style
- Handle error cases as specified in the design
- Write the tests listed in the Tests section, verifying each acceptance criterion

### 3. Verify

Run the project's verification commands (from tech-stack.md, CLAUDE.md, or discover from package.json / Makefile / pyproject.toml):

- **Check command** — lint, format, typecheck. Fix issues before proceeding.
- **Tests** — run tests for modified modules; run broader suites if cross-cutting. All must pass.
- **Migration** — if data models changed and the project uses migrations, run the migration command.

### 4. Acceptance and Self-Check

Walk through the task's Acceptance criteria. For each item, note evidence (e.g., "check command passed", "N tests green"). Also verify:

- Implementation matches the interfaces defined in the task's How section
- All tests from the Tests section are written and passing
- Code follows CLAUDE.md / AGENTS.md conventions (if they exist) and matches surrounding code style
- No hardcoded values, debug code, commented-out code, or orphan TODOs
- Error handling matches project patterns

If any item fails, fix and re-verify.

### 5. Document Completion

**Update the task file**:
- Set status to `done`
- Add implementation notes (deviations from plan, discoveries, decisions made) to the Notes section

**Update spec.md**:
- Update the task's Status in the task index table from `pending` to `done`

### 6. Commit

Stage and commit the changes using conventional commit format:

```
feat(<feature-slug>): task N - <short description>
```

This follows the conventional commits convention. Adapt to the project's commit style if one is established.

### 7. Report

```
Task N complete: <title>
  Files changed: <count>
  Tests: <pass/fail summary>
  Next eligible: Task M — <title>
  Remaining: X tasks

If context is getting heavy, start fresh and run /spec impl <feature> to continue.
```

## Common Mistakes

| Mistake | Prevention |
|---------|-----------|
| Skipping verification | Always run the project's check command before marking done |
| Modifying files outside task scope | Only touch files listed in the task. If you discover needed changes, note them for a future task |
| Not reading existing code first | Always read a file before modifying it to match existing patterns |
| Leaving tests broken | Run the full test suite, not just new tests. Fix any regressions |
| Not following project conventions | Check CLAUDE.md/AGENTS.md if they exist. Match surrounding code style |
| Implementing ahead of the current task | Only implement what the current task specifies. Later tasks may depend on doing things in order |

## Context Management

After completing a task, if the conversation has been long (many tool calls, large files read):

```
Task N complete. X remaining.
If context is getting heavy, start fresh and run /spec impl <feature> to continue.
```

## Next Step

If there are remaining tasks:

```
Next: /spec impl <feature>
```

If all tasks are done, update spec.json: set `phase` to `"complete"` and `updated_at` to today's date. Then report:

```
All tasks complete! Run /spec status <feature> for a final summary.
```

If `.agents/reference/` exists, suggest syncing steering docs since the codebase has changed:

```
Project context may have drifted — run /steering sync to update steering docs.
```

## Rules

- Never implement without approved tasks — no `-y` override
- One task at a time unless the user explicitly requests multiple
- Always verify before marking done — the check command and tests must pass
- Never modify files outside the task's scope without noting it
- Update the task file before committing
- If a task reveals that the design needs changes, stop and discuss with the user before continuing

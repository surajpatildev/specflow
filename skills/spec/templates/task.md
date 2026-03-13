# Task {{N}}: {{SHORT_NAME}}

> Status: pending | in-progress | done | blocked
> Size: S | M | L
> Depends on: {{TASK_NUMBERS_OR_NONE}}
> Requirements: {{REQUIREMENT_IDS}}

## What
<!-- Describe capabilities and business logic in natural language.
     Do NOT include file paths, function names, or class names here. -->
{{WHAT_TO_BUILD}}

## How
<!-- Step-by-step implementation approach. Specific enough that a reviewer
     can verify the plan is sound before code is written. Reference existing
     patterns and pull key interfaces from design.md. -->

1. {{CONCRETE_STEP}}

**Key interfaces** (from design.md):
<!-- Function signatures, types, or API contracts this task introduces.
     Skip for S-size tasks with no new interfaces. -->

## Files
<!-- Every file this task touches, with a brief note on what changes. -->
- `path/to/file` — create — {{BRIEF_DESCRIPTION}}
- `path/to/existing` — modify — {{WHAT_CHANGES}}

## Tests
<!-- Specific test scenarios that verify this task's acceptance criteria.
     Name each test and describe the expected behavior. -->
- **{{test_name}}**: {{what_it_verifies}}

## Acceptance
- Project check command passes clean
- All tests in Tests section pass
- {{OBSERVABLE_BEHAVIOR_1}}

## Notes
<!-- Filled during implementation. Deviations, discoveries, gotchas. -->

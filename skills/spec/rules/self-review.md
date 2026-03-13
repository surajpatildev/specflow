# Self-Review via Subagent

After generating a phase artifact (requirements, design, or tasks), dispatch a review subagent before presenting to the user. The reviewer catches inconsistencies, gaps, and quality issues while keeping the review isolated from the generation context.

## Why Subagent Review

Generating a complex artifact consumes significant context. By dispatching a fresh subagent to review, you get an independent perspective that isn't anchored to the assumptions made during generation. The reviewer sees only the artifact and its inputs, not the reasoning that produced it.

## Review Pattern

### 1. Dispatch the Reviewer

After writing the artifact to disk, dispatch a subagent with the **Agent tool** using the prompt template below. Pass only the artifact and its inputs as context. Never pass your session history.

```
Review the following spec artifact for quality and consistency.

**Artifact:** <path to the generated file>
**Inputs:** <paths to files the artifact was derived from>
**Phase:** <requirements | design | tasks>

Read the artifact and all input files, then check against the criteria below.

## Review Criteria

### All Phases
- No TODOs, placeholders, or "TBD" sections
- No internal contradictions (e.g., a statement in one section conflicts with another)
- Consistent terminology throughout (same concept should use the same name)
- Sections are proportionally detailed (no section noticeably thinner than others)

### Requirements Phase
- Every criterion uses valid EARS syntax (shall + one of: ubiquitous, event, state, unwanted, optional)
- Every criterion is testable with a concrete scenario
- Single behavior per criterion (one "shall" statement)
- No ambiguous qualifiers (appropriate, efficient, user-friendly, etc.)
- Hierarchical numbering is consistent (1.1, 1.2, 2.1, not mixed formats)
- Out of Scope section exists and addresses likely follow-up questions

### Design Phase
- Every acceptance criterion from requirements.md is traced to a design component
- Architecture references the project's actual patterns (not hypothetical)
- Data models include all fields, types, and constraints
- Error handling covers every unwanted-behavior requirement
- Components have clear boundaries and interfaces
- No requirement was invented that doesn't exist in requirements.md

### Tasks Phase
- Dependency DAG is valid (no cycles, no missing references)
- Every acceptance criterion is mapped to at least one task
- All tasks are size S or M (no L tasks without justification)
- File paths match the project's actual directory structure
- Each task's implementation steps are self-contained (a fresh agent could execute without asking clarifying questions)
- Task numbering and naming follow NN-verb-noun.md convention

## Output Format

**Status:** Approved | Issues Found

**Issues (if any):**
- [Section/Item]: [specific problem] - [why it matters]

**Recommendations (non-blocking):**
- [suggestions for improvement that don't block approval]
```

### 2. Process the Review

Read the subagent's response:

- **If Approved:** Proceed to the presentation step.
- **If Issues Found:** Fix each issue in the artifact. If an issue is a false positive (the reviewer misunderstood something), skip it. Do not re-dispatch unless there were significant structural fixes (e.g., missing traceability, contradictions). One review pass is usually sufficient.

### 3. Limits

- **Max 3 review iterations** per phase. If the reviewer still finds issues after 3 passes, present the artifact to the user and note the remaining concerns. The user's review is the final authority.
- **Don't over-optimize.** The goal is catching real problems (inconsistencies, gaps, contradictions), not polishing prose. If the reviewer only returns recommendations with no issues, that counts as Approved.

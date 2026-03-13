# Self-Review via Subagent

After generating a phase artifact (requirements, design, or tasks), dispatch a review subagent before presenting to the user. The reviewer catches inconsistencies, gaps, and quality issues while keeping the review isolated from the generation context.

## Why Subagent Review

Generating a complex artifact consumes significant context. By dispatching a fresh subagent to review, you get an independent perspective that isn't anchored to the assumptions made during generation. The reviewer sees only the artifact and its inputs, not the reasoning that produced it.

## Review Pattern

### 1. Dispatch the Reviewer

After writing the artifact to disk, dispatch a subagent to perform the review. Fill in the placeholders in the template below with actual file paths and pass it as the subagent's prompt. Never pass your session history — the reviewer should start fresh with only the artifact and inputs.

**Example (requirements phase):**
```
Artifact: .agents/specs/user-notifications/requirements.md
Inputs: .agents/specs/user-notifications/spec.json
Phase: requirements
```

**Prompt template:**

```
Review the following spec artifact for quality and consistency.

**Artifact:** <actual path(s) to the generated file(s)>
**Inputs:** <actual paths to files the artifact was derived from>
**Phase:** <requirements | design | tasks>

Read all listed files using the Read tool, then check against the criteria below.

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
- Testing strategy covers unit, integration, and e2e layers
- Open Questions section exists (may be empty if all resolved)
- System Flows section names actual components

### Tasks Phase
- Dependency DAG is valid (no cycles, no missing references)
- Every acceptance criterion is mapped to at least one task
- All tasks are size S or M (no L tasks without justification)
- File paths match the project's actual directory structure
- Each task's implementation steps are self-contained (a fresh agent could execute without asking clarifying questions)
- Task numbering and naming follow NN-verb-noun.md convention
- Requirements coverage table exists with no gaps

## Output Format

**Status:** Approved | Issues Found

**Issues (if any):**
- [Section/Item]: [specific problem] - [why it matters]

**Recommendations (non-blocking):**
- [suggestions for improvement that don't block approval]
```

For the **tasks phase**, the Artifact field should list the directory path (e.g., `.agents/specs/<feature>/tasks/`) and instruct the reviewer to read all `.md` files in it, since multiple task files need to be checked together for dependency and coverage validation.

### 2. Process the Review

Read the subagent's response:

- **If Approved:** Proceed to the presentation step.
- **If Issues Found:** Fix each issue in the artifact. If an issue is a false positive (the reviewer misunderstood something), skip it.
- **Recommendations only (no issues):** Treat as Approved. Act on recommendations at your discretion, but they do not block proceeding.

After fixing issues, re-dispatch only if the fixes were structural (e.g., added missing traceability, resolved contradictions between sections). Cosmetic or prose fixes do not warrant another pass. Limit to **2 additional passes** (3 total). After the third pass, present the artifact to the user and note any remaining concerns. The user's review is the final authority.

### 3. Principles

- **Catch real problems, not polish prose.** The goal is inconsistencies, gaps, and contradictions.
- **One pass is usually enough.** Most artifacts pass or need only minor fixes.
- **The user is the final reviewer.** The subagent review improves quality before the user sees it, but the user's approval is what advances the phase.

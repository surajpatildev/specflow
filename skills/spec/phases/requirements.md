# Phase: Requirements

Generate EARS-format requirements from the feature description and codebase research.

## Phase-Specific Context

Load these files in addition to the shared context from SKILL.md:

- `.agents/specs/<feature>/requirements.md` — the stub from init
- `rules/ears-format.md` — EARS syntax reference
- `rules/discovery.md` — tool guidance for external documentation research
- `rules/self-review.md` — subagent review pattern
- `templates/requirements.md` — output template

## Procedure

### 1. Understand the Feature

Read the description from `spec.json` and the stub in `requirements.md`. Get a baseline understanding of what the user wants before diving into research.

### 2. Clarify Scope with the User

Before diving into deep research, engage the user to understand their vision. Use an interactive question tool (such as AskUserQuestion) if available. Ask one question at a time, following the interaction patterns in SKILL.md's User Interaction section.

Start with the big picture, then drill into specifics:

- **What problem does this solve?** Understand the motivation beyond the feature description.
- **Who are the users/actors?** Confirm the personas and their needs.
- **What's the scope?** What's definitely in, what's definitely out, what's unclear.
- **Key behaviors?** The most important things the feature must do.
- **Any constraints?** Performance, security, compatibility, existing patterns to follow.

You've already read the spec.json description and any stub — use that context to make your questions specific, not generic. But don't wait until you've explored the entire codebase to start asking. The user's answers will focus your research on what actually matters.

### 3. Research

Survey the project and external sources, now informed by the user's answers about scope and priorities.

**Codebase:**
- Search for related models, services, routes, schemas, and configuration
- Read existing code in areas the feature will extend or interact with
- Check for documentation (README, inline docs, ADRs) that clarifies domain concepts
- Review the product context (`.agents/reference/product.md`, if exists) to identify relevant user types
- Check `.agents/reference/tech-stack.md` (if exists) for architectural patterns that constrain design

**External — when the feature depends on libraries, APIs, or external services:**
- Look up current documentation to verify capabilities before writing requirements that depend on them — requirements that assume a library can do something it can't lead to designs that fail at implementation
- Use documentation lookup tools (such as context7), web fetch tools (such as WebFetch), or web search tools (such as WebSearch) to confirm APIs, features, and integration patterns
- See `rules/discovery.md` — External Research for tool priority and source quality guidance

For complex features where the required libraries or services are clear from the description, consider dispatching a subagent to research external documentation while you survey the codebase. When the targets aren't clear upfront, survey the codebase first to identify what to research externally.

### 4. Confirm Findings and Fill Gaps

Present what you learned from research and confirm key decisions with the user. Use an interactive question tool (such as AskUserQuestion) if available.

For each area where research revealed multiple valid approaches or raised new questions:
- Present what you found (the evidence)
- Propose 2-3 options with tradeoffs
- Mark your recommendation

Before writing requirements, summarize your understanding of the feature scope: "Here's what I plan to cover — does this match your expectations?" This catches misalignment before you invest in the full document. If research surfaced new personas or edge cases the user didn't mention, confirm those too.

### 5. Write Requirements

Generate the full `requirements.md` using the template. Structure:

**Objective** — User story format:
```
As a <persona>, I want <capability>, so that <benefit>.
```

**Acceptance Criteria** — EARS format. Use the appropriate pattern for each criterion:

| Pattern | Template | Use When |
|---------|----------|----------|
| Ubiquitous | The system shall `<action>` | Always true, no trigger |
| Event-driven | When `<event>`, the system shall `<action>` | Triggered by an event |
| State-driven | While `<state>`, the system shall `<action>` | Active during a state |
| Unwanted behavior | If `<condition>`, then the system shall `<action>` | Error/edge case handling |
| Optional | Where `<feature enabled>`, the system shall `<action>` | Configurable behavior |

Quality rules for each criterion:
- **Testable**: Can be verified with a concrete test
- **Single behavior**: One shall per criterion
- **Specific**: No ambiguous words (appropriate, efficient, user-friendly)
- **Independent**: Does not depend on implementation details

Number criteria hierarchically under each requirement (1.1, 1.2, 2.1, 2.2, etc.) for traceability through design and tasks.

**Out of Scope** — Explicitly list related capabilities that are NOT part of this feature. This prevents scope creep during design and implementation.

### 6. Self-Review

Before presenting to the user, dispatch a review subagent following the pattern in `rules/self-review.md`. Pass:

- **Artifact:** `.agents/specs/<feature>/requirements.md`
- **Inputs:** `.agents/specs/<feature>/spec.json`
- **Phase:** `requirements`

Fix any issues the reviewer finds (inconsistent EARS patterns, untestable criteria, ambiguous language, numbering gaps). Then proceed.

### 7. Update spec.json

Set:
- `phase`: `"requirements-generated"`
- `requirements.generated`: `true`
- `updated_at`: today's date

Do NOT set `requirements.approved` — that requires user review.

### 8. Present for Review

Show the requirements and a quality checklist:

```
Requirements Quality Checklist:
[ ] Every criterion uses EARS syntax
[ ] Every criterion is testable with a concrete scenario
[ ] Single behavior per criterion (one "shall")
[ ] No ambiguous qualifiers (appropriate, efficient, etc.)
[ ] Out of Scope section addresses likely follow-up questions
[ ] Personas match product.md user types (if product.md exists)
```

Ask the user to review. If they approve:
- Set `requirements.approved: true` in spec.json

If they request changes, revise and re-present.

## Next Step

After approval, suggest:

```
Requirements approved. Next options:
  /spec gap <feature>    — analyze existing code before designing (recommended for brownfield)
  /spec design <feature> — go directly to technical design
```

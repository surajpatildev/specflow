# Phase: Requirements

Generate EARS-format requirements from the feature description and codebase research.

## Phase-Specific Context

Load these files in addition to the shared context from SKILL.md:

- `.agents/specs/<feature>/requirements.md` — the stub from init
- `rules/ears-format.md` — EARS syntax reference
- `rules/self-review.md` — subagent review pattern
- `templates/requirements.md` — output template

## Procedure

### 1. Understand the Feature

Read the description from `spec.json` and the stub in `requirements.md`. Get a baseline understanding of what the user wants before diving into research.

### 2. Research the Codebase

Survey the project to understand what exists and what the feature touches. Use all available tools — search the codebase, read existing code, check documentation, explore the web for relevant library docs or patterns:

- Search for related models, services, routes, schemas, and configuration
- Read existing code in areas the feature will extend or interact with
- Check for documentation (README, inline docs, ADRs) that clarifies domain concepts
- Review the product context (`.agents/reference/product.md`, if exists) to identify relevant user types
- Check `.agents/reference/tech-stack.md` (if exists) for architectural patterns that constrain design

Map the personas who will interact with this feature (end users, admins, system actors, etc.).

### 3. Clarify with the User

After research, you'll have a much clearer picture of the feature and a clearer picture of what you *don't* know. Use an interactive question tool (such as AskUserQuestion) if available to present decisions that would materially affect scope or behavior and that you couldn't determine from the codebase. Follow the interaction patterns in SKILL.md's User Interaction section.

Focus on what the feature should do, not how to implement it. For each decision, present 2-3 concrete options grounded in what you found in the code, with your recommendation and the tradeoffs. Group related scope questions together when they're about the same topic.

Before moving on, make sure you understand:
- What the feature should do and what it explicitly should not
- Who the users/actors are and what they need
- Key behavioral choices where multiple valid approaches exist
- Edge cases or error scenarios that affect acceptance criteria

If research answered everything clearly, skip this step and proceed.

### 4. Write Requirements

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

### 5. Self-Review

Before presenting to the user, dispatch a review subagent following the pattern in `rules/self-review.md`. Pass:

- **Artifact:** `.agents/specs/<feature>/requirements.md`
- **Inputs:** `.agents/specs/<feature>/spec.json`
- **Phase:** `requirements`

Fix any issues the reviewer finds (inconsistent EARS patterns, untestable criteria, ambiguous language, numbering gaps). Then proceed.

### 6. Update spec.json

Set:
- `phase`: `"requirements-generated"`
- `requirements.generated`: `true`
- `updated_at`: today's date

Do NOT set `requirements.approved` — that requires user review.

### 7. Present for Review

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

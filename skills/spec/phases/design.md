# Phase: Design

Produce a technical design document grounded in approved requirements and codebase research.

## Prerequisites

Check `spec.json` before proceeding:

- `requirements.generated` must be `true`
- `requirements.approved` must be `true`

If `requirements.approved` is `false` and the user passed `-y`, set it to `true` in spec.json and proceed. Otherwise, stop and tell the user to review and approve requirements first.

## Phase-Specific Context

Load these files in addition to the shared context from SKILL.md:

- `.agents/specs/<feature>/requirements.md` — approved requirements
- `.agents/specs/<feature>/gap-analysis.md` (if exists) — from /spec gap
- `rules/discovery.md` — research process
- `rules/design-principles.md` — quality standards
- `rules/design-review.md` — review criteria for the presentation step
- `rules/self-review.md` — subagent review pattern
- `templates/design.md` — output template
- `templates/research.md` — research output template

## Procedure

### 1. Classify Feature Type

Determine the scope to calibrate discovery depth:

| Type | Signal | Discovery Level |
|------|--------|----------------|
| **New Feature** | New domain concept, no existing code | Full |
| **Extension** | Adds capabilities to existing module | Light |
| **Simple Addition** | Small, well-defined change (new field, flag) | Minimal |
| **Complex Integration** | Crosses multiple modules or services | Full |

### 2. Execute Discovery

Follow `rules/discovery.md` based on the classification. This includes both codebase analysis and external documentation research — see the External Research and Parallel Research sections in `rules/discovery.md` for tool guidance and subagent patterns.

**Full Discovery** (New Feature / Complex Integration):
- Map all related modules, services, and data flows
- Read key files end-to-end to understand patterns
- Trace request flows through the system
- Identify all integration points
- Verify library/API capabilities against current documentation
- Document architectural constraints (from tech-stack.md if available, otherwise infer from the codebase)

**Light Discovery** (Extension):
- Read the target module and its direct dependencies
- Understand the existing patterns in that area
- Identify the extension points
- Verify any new library features or APIs against current documentation

**Minimal Discovery** (Simple Addition):
- Read the specific file(s) that will change
- Confirm the change fits existing patterns

### 3. Resolve Ambiguities

Discovery often reveals decisions that affect architecture or data modeling where the answer isn't clear from the requirements or codebase. Use an interactive question tool (such as AskUserQuestion) if available to present these decisions before proceeding. Follow the interaction patterns in SKILL.md's User Interaction section.

For each decision, present what you found during discovery, your recommended approach, and the alternatives with their tradeoffs. Design decisions made without user input are expensive to change once implementation starts.

Common design decisions that need user input:
- Data model choices (normalize vs. denormalize, separate table vs. embedded)
- Integration approach (sync vs. async, polling vs. webhooks)
- Authorization model (role-based, resource-based, attribute-based)
- Caching and performance tradeoffs
- Third-party service selection when multiple options exist

If discovery answered everything clearly, skip this step.

### 4. Persist Research

Write findings to `.agents/specs/<feature>/research.md` using the template at `templates/research.md`. This preserves context for the tasks and impl phases even if the conversation is cleared.

### 5. Generate Design Document

Write `.agents/specs/<feature>/design.md` using the template. Include these sections:

**Overview** — What is being built and why. One paragraph grounding the design in the requirements.

**Goals and Non-Goals** — What this design achieves and what it explicitly does not. Non-goals prevent scope creep.

**Architecture** — How the feature fits into the existing system. Reference the project's architecture (from tech-stack.md if available, otherwise describe what you find in the codebase). Include a diagram (ASCII or Mermaid) if the feature involves multiple components.

**System Flows** — Step-by-step flows for the primary use cases. Show the path from trigger to completion, naming the actual components involved.

**Requirements Traceability** — Map every acceptance criterion (1.1, 1.2, 2.1, etc.) to the design components that satisfy it. Every criterion must be covered.

**Components and Interfaces** — New or modified components with their responsibilities and public interfaces. Include function signatures or type definitions where they clarify the design.

**Data Models** — New or modified data structures. Show fields, types, relationships, and constraints. Note migration needs if applicable.

**Error Handling** — How each failure mode is handled. Map to the unwanted-behavior requirements.

**Testing Strategy** — What to test and how. Organize by layer:
- Unit tests for business logic
- Integration tests for component interactions
- End-to-end tests for critical flows

**Open Questions** — Decisions that need user input. If any open question would block task decomposition, ask the user now rather than leaving it unresolved.

### 6. Self-Review

Before presenting to the user, dispatch a review subagent following the pattern in `rules/self-review.md`. Pass:

- **Artifact:** `.agents/specs/<feature>/design.md`
- **Inputs:** `.agents/specs/<feature>/requirements.md`, `.agents/specs/<feature>/research.md` (if exists), `.agents/specs/<feature>/gap-analysis.md` (if exists)
- **Phase:** `design`

Fix any issues the reviewer finds (untraced requirements, internal contradictions, incomplete sections, invented requirements). Then proceed.

### 7. Update spec.json

Set:
- `phase`: `"design-generated"`
- `design.generated`: `true`
- `updated_at`: today's date

Do NOT set `design.approved` — that requires user review.

### 8. Present for Review

Show the design and a quality checklist:

```
Design Quality Checklist:
[ ] Every acceptance criterion has a traceable design component
[ ] Architecture aligns with project patterns (per tech-stack.md or codebase)
[ ] Data models include all fields, types, and constraints
[ ] Error handling covers all unwanted-behavior requirements
[ ] Testing strategy covers unit, integration, and e2e
[ ] No unresolved open questions that block task decomposition
[ ] Components have clear boundaries and interfaces
```

Ask the user to review. If they approve:
- Set `design.approved: true` in spec.json

If they request changes, revise and re-present.

## Context Management Warning

After design generation, warn:

```
Context may be heavy after this phase. Consider starting a fresh conversation
before running /spec tasks <feature> to avoid degraded output quality.
```

## Next Step

After approval, suggest:

```
Design approved. Next: /spec tasks <feature>
Consider starting a fresh conversation first if context is heavy.
```

## Rules

- Never invent requirements — the design must trace back to approved acceptance criteria
- Use the project's actual architecture, not a hypothetical one
- If gap-analysis.md exists, incorporate its findings into the design
- Keep the design technology-aware but not implementation-level — save code details for task files
- Prefer extending existing patterns over introducing new ones

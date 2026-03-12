# Phase: Gap Analysis

Survey the existing codebase to map what exists, what's missing, and how the new feature should integrate. This phase is optional but recommended for brownfield features that extend or interact with existing code.

## Phase-Specific Context

Load these files in addition to the shared context from SKILL.md:

- `.agents/specs/<feature>/requirements.md` — approved requirements
- `.agents/specs/<feature>/spec.json` — current state
- `.agents/reference/tech-stack.md` (if exists) — architecture and directory structure

## Procedure

### 1. Survey Existing Assets

Search the codebase systematically across these categories:

| Category | What to Look For |
|----------|-----------------|
| **Models / Data** | Database models, schemas, type definitions, migrations related to the feature domain |
| **Business Logic** | Services, use cases, domain logic, utility functions that handle related behavior |
| **API / Routes** | Endpoints, controllers, handlers, GraphQL resolvers in the feature's domain |
| **Background Jobs** | Workers, queues, scheduled tasks, event handlers that touch related data |
| **Configuration** | Environment variables, feature flags, settings relevant to the feature |
| **Tests** | Existing test coverage for related modules — patterns, fixtures, factories |

For each asset found, note:
- File path and purpose
- Whether it can be reused, extended, or must be replaced
- Conventions it follows (naming, patterns, structure)

### 2. Map Requirements to Assets

Create a gap table that maps each acceptance criterion to existing code:

```markdown
| Requirement | Existing Asset | Gap | Effort |
|-------------|---------------|-----|--------|
| 1.1: ... | `path/to/file.ext` — partial match | Need to add X | S |
| 1.2: ... | None | New component needed | M |
| 2.1: ... | `path/to/other.ext` — full match | Wire up only | S |
```

Effort estimates: **S** (minor change), **M** (moderate work), **L** (significant new code).

### 3. Identify Conventions

Document the patterns the existing codebase follows that the new feature should match:

- Naming conventions (files, functions, variables, routes)
- Code organization patterns (where new files should go)
- Error handling approach
- Testing patterns (test structure, assertion style, fixture patterns)
- Any architectural patterns visible in related modules (e.g., repository pattern, service layer, event-driven)

### 4. Map Integration Surfaces

Identify where the new feature connects to existing code:

- **Data dependencies**: Existing models/tables the feature reads or writes
- **Service dependencies**: Existing services the feature calls
- **API surface**: Existing endpoints the feature extends or new ones it adds alongside
- **Event surfaces**: Events the feature produces or consumes
- **Shared utilities**: Helpers, middleware, decorators the feature should use

### 5. Present Approach Options

Propose 2-3 approaches based on the gap analysis (fewer is fine if one option clearly dominates):

**Option A — Extend Existing**: Build on top of existing modules. Best when gaps are small and existing code is well-structured.

**Option B — Create New**: Build the feature as a standalone module. Best when existing code is unrelated or poorly structured.

**Option C — Hybrid**: Reuse some existing components while creating new ones for gaps. Best when some parts align and others don't.

For each option, note: effort estimate, risk level, and tradeoffs.

### 6. Discuss with the User

Present your approach options and ask the user which direction they prefer. This choice shapes the entire design phase, so it's worth getting right before writing the analysis. Share your recommendation and reasoning, but let the user make the call — they have context about team preferences, timelines, and risk appetite that the codebase alone can't tell you.

If one option clearly dominates (e.g., the feature is greenfield with nothing to extend), state your recommendation and proceed unless the user objects.

### 7. Write gap-analysis.md

Write the full analysis to `.agents/specs/<feature>/gap-analysis.md` using the template at `${CLAUDE_PLUGIN_ROOT}/templates/gap-analysis.md`. Include the user's chosen approach in the Recommendation section.

### 8. Update spec.json

Set:
- `updated_at`: today's date

Do NOT change the `phase` — it stays at `requirements-generated`. The gap analysis is informational; it feeds into design but doesn't advance the phase state machine.

## Next Step

Suggest:

```
Gap analysis complete. Review the findings in gap-analysis.md, then run:
  /spec design <feature>
```

## Rules

- Gap analysis is read-only research — never modify project source files
- Use the project's actual directory structure (from tech-stack.md if available, otherwise explore the codebase)
- If no relevant existing code is found, say so clearly — the feature is greenfield
- Keep the analysis factual — report what exists, don't pre-design the solution

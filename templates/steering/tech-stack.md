# Tech Stack: {{PROJECT_NAME}}

## Core Stack

<!-- The major technology choices. Include only what shapes how features
     are designed and built — not every transitive dependency. -->

| Layer | Technology | Purpose |
|-------|-----------|---------|
| {{LAYER}} | {{TECH}} | {{PURPOSE}} |

## Architecture

<!-- How the codebase is organized at a high level. Name the actual
     directories and their roles. This helps the spec workflow place
     new components correctly during design and task generation. -->

{{ARCHITECTURE_DESCRIPTION}}

### Directory Structure

<!-- Show the actual top-level layout. The spec workflow references this
     to determine where new files go. -->

```
{{DIRECTORY_TREE}}
```

## Key Decisions

<!-- Architectural decisions that constrain how features are built.
     Not every decision — just the ones the spec workflow needs to
     know about to produce designs that fit the system.
     Examples: "Server components for data fetching",
     "Event sourcing for audit trail", "Edge middleware for auth" -->

- **{{DECISION}}** — {{RATIONALE}}

## Commands

<!-- Commands used during spec implementation. The impl phase references
     these to verify work and run quality checks. -->

| Action | Command | Notes |
|--------|---------|-------|
| Check | `{{COMMAND}}` | Lint + format + typecheck — run after every significant edit |
| Test | `{{COMMAND}}` | Run after implementing a task |
| Dev | `{{COMMAND}}` | Local development server |
| Migrate | `{{COMMAND}}` | After model/schema changes (if applicable) |

## Conventions

<!-- Project-specific patterns that affect how specs, designs, and tasks
     should be written. Don't duplicate CLAUDE.md — only include patterns
     that the spec workflow needs to produce good output.
     Examples: "Bottom-up implementation order: models → services → routes",
     "All API responses wrapped in { data, error, meta }" -->

- {{CONVENTION}}

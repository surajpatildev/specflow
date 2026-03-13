---
name: spec
description: Spec-driven development workflow for planning and implementing features. Use when starting any new feature, extending existing functionality, discussing requirements, designing systems, decomposing work into tasks, or implementing from a plan. Also use when the user asks about feature specs, project status, or wants structured planning. Triggers on /spec, "plan this feature", "write requirements", "design this", "break into tasks", "implement the spec", "what's the status", or any request for structured feature planning before writing code.
---

# Spec

Structured planning and implementation workflow for features that benefit from deliberate decomposition (4+ files or unclear scope). This skill is a router — it parses the subcommand and loads the appropriate phase file.

## Subcommands

```
/spec init "Build user notification system"      → phases/init.md
/spec requirements user-notifications            → phases/requirements.md
/spec design user-notifications [-y]             → phases/design.md
/spec tasks user-notifications [-y]              → phases/tasks.md
/spec gap user-notifications                     → phases/gap.md
/spec status [user-notifications]                → phases/status.md
/spec impl user-notifications [task-numbers]     → phases/impl.md
/spec (no args)                                  → phases/status.md (auto-detect)
```

## Routing

Parse the first argument to determine the subcommand:

| Argument | Phase file | Description |
|----------|-----------|-------------|
| `init` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/init.md` | Initialize a new spec from a description |
| `requirements` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/requirements.md` | Generate EARS-format requirements |
| `design` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/design.md` | Discovery + technical design |
| `tasks` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/tasks.md` | Decompose design into implementation tasks |
| `gap` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/gap.md` | Brownfield gap analysis |
| `status` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/status.md` | Show spec progress and next steps |
| `impl` | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/impl.md` | Implement next eligible task |
| (no args) | `${CLAUDE_PLUGIN_ROOT}/skills/spec/phases/status.md` | Auto-detect active spec, show status |

## Shared Context (Load Before Every Phase)

Before executing any phase, load this shared context (skip files that don't exist):

1. Project's `CLAUDE.md` or `AGENTS.md` (if exists) — coding standards and conventions
2. `.agents/reference/product.md` (if exists) — product context (created by `/steering`)
3. `.agents/reference/tech-stack.md` (if exists) — tech stack, commands, architecture (created by `/steering`)

If a spec already exists for the feature:
4. `.agents/specs/<feature>/spec.json` — current phase and approval state

Each phase file lists additional phase-specific files to load (rules, templates, spec documents). Load those in addition to the shared context, not instead of it.

## Approval Gating

Phases gate on prior approvals tracked in `spec.json`:

| Phase | Requires | Override |
|-------|----------|----------|
| `design` | `requirements.approved == true` | `-y` auto-approves |
| `tasks` | `requirements.approved == true` AND `design.approved == true` | `-y` auto-approves all |
| `impl` | `tasks.approved == true` | No override — tasks must be approved |

If a prerequisite isn't met, stop and tell the user what needs approval first. The `-y` flag sets prerequisite approvals to `true` in `spec.json` and proceeds.

## Phase Execution

1. Parse the subcommand and feature name from arguments
2. Load shared context files listed above
3. Check approval gates (if applicable)
4. Read and follow the instructions in the phase file
5. After the phase completes, suggest next steps (see chaining below)

## Chaining

After each phase completes, suggest the natural next step:

| Completed phase | Suggest next |
|-----------------|-------------|
| `init` | `/spec requirements <feature>` |
| `requirements` | `/spec gap <feature>` (brownfield) or `/spec design <feature>` |
| `gap` | `/spec design <feature>` |
| `design` | `/spec tasks <feature>` |
| `tasks` | `/spec impl <feature>` (after clearing context) |
| `impl` (task done) | `/spec impl <feature>` (next task) |

## Context Management

After heavy phases (`design`, `tasks`), warn:

```
Context may be heavy after this phase. Consider starting a fresh conversation
before running the next phase to avoid degraded output quality.
```

After `impl` completes a task, if many tools were invoked:

```
Task N complete. X remaining.
If context is getting heavy, start fresh and run `/spec impl <feature>` to continue.
```

## User Interaction

Getting user alignment right is critical to producing useful specs. These patterns apply to all phases that ask the user questions (requirements, design, gap).

### Use AskUserQuestion for Decisions

When you need user input on decisions that affect scope, behavior, or architecture, use the **AskUserQuestion tool** to present structured choices. This gives the user a clear, interactive way to respond rather than having to parse a wall of text.

- Present **2-3 concrete options** per question, each with a short label and a sentence explaining the tradeoff or impact
- Mark your **recommended option** and explain why you recommend it
- Group related questions when they're about the same topic, but don't batch unrelated decisions together
- If the user's answer doesn't fit any option, they can respond freely

### Investigate Before Asking

Every question should reflect what you actually found in the codebase, docs, or web research. Questions grounded in evidence ("I see you're using the repository pattern in `src/repositories/`. Should the new service follow the same pattern?") are far more useful than generic ones ("How should I structure the data layer?"). If the answer is already in the code, don't ask.

### What to Ask About

Ask about things that **materially affect the result** and **can't be determined from evidence**:
- Scope boundaries (what's in, what's out)
- Behavioral choices where multiple valid approaches exist
- Architecture decisions with meaningful tradeoffs
- Domain concepts that aren't documented in the codebase

Don't ask about things you can derive from the codebase, steering docs, or standard patterns. Don't ask implementation-level questions (save those for design/tasks). If research answered everything clearly, skip asking and proceed.

## Rules

- Every spec lives in `.agents/specs/<feature-name>/` as a directory
- Feature names are kebab-case slugs derived from the description
- `spec.json` is the single source of truth for phase state and approvals
- Tasks are individual files in `tasks/`, not checkboxes in spec.md
- Even trivial features benefit from `/spec init` — use a brief description and skip to `/spec impl`
- Specs are living documents — update them as implementation reveals new requirements
- Never skip the routing step — always load the correct phase file

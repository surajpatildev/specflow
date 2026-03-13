# SpecFlow

A Claude Code plugin that brings spec-driven development to AI-assisted coding. Instead of jumping straight into code, SpecFlow guides you through a structured process: understand the problem, define requirements, design the solution, decompose into tasks, then implement. The AI does the heavy lifting at every step.

Works with any project. Any language. Any framework.

---

## Why SpecFlow

AI coding assistants work best with clear direction. Without structure, features drift, requirements get missed, and implementation touches files it shouldn't. SpecFlow gives your AI agent a **repeatable planning workflow** that produces better code.

| Feature | |
|:--|:--|
| **Research-first** | Explores your codebase and the web before asking questions. Grounded in what it found, not generic checklists. |
| **Persistent state** | Specs survive context compaction and session restarts. Pick up exactly where you left off. |
| **Approval gates** | Requirements need sign-off before design. Design before tasks. Nothing ships without your OK. |
| **Full traceability** | Every task traces to a design component, which traces to a requirement. Nothing gets lost. |
| **Context-aware** | Learns your architecture and conventions through steering docs. Applies them consistently. |

---

## Install

### Claude Code

```
/plugin marketplace add surajpatildev/specflow
/plugin install specflow@specflow
```

### Any Agent (Codex, OpenCode, Gemini CLI, Cursor, etc.)

```
npx skills add surajpatildev/specflow
```

This installs SpecFlow's skills into your project's agent skills directory, which is automatically discovered by any agent that supports the [Agent Skills](https://agentskills.io) standard.

> **Note:** Hooks (session-start context injection, pre-compact warnings) are only available via the Claude Code plugin installation. Other agents work fine without them — use `/spec status` to recover context at the start of a session.

---

## Quick Start

**1. Set up project context** *(optional but recommended)*

```
/steering
```

Scans your codebase and asks you to confirm what it found: product purpose, tech stack, architecture, key commands. This context gets loaded into every spec session.

**2. Start a feature**

```
/spec init "Add webhook notifications for order status changes"
```

**3. Walk through the phases**

```
/spec requirements webhook-notifications
/spec design webhook-notifications
/spec tasks webhook-notifications
/spec impl webhook-notifications
```

Each phase builds on the last. SpecFlow suggests the next step after each one completes.

---

## Workflows

SpecFlow adapts to the size of the job. Three ways to use it:

### Full Spec *// Complex features, unclear scope*

```
    ┌─────────────────────────────────────────────────────────────┐
    │                                                             │
    │   /steering ·············· Set up project context (once)    │
    │       │                                                     │
    │       ▼                                                     │
    │   /spec init ············ Describe the feature              │
    │       │                                                     │
    │       ▼                                                     │
    │   /spec requirements ···· Research → clarify → write        │
    │       │                         ✓ you approve               │
    │       ▼                                                     │
    │   /spec gap ············· Analyze existing code (optional)  │
    │       │                                                     │
    │       ▼                                                     │
    │   /spec design ·········· Discovery → technical design      │
    │       │                         ✓ you approve               │
    │       ▼                                                     │
    │   /spec tasks ··········· Decompose into task files         │
    │       │                         ✓ you approve               │
    │       ▼                                                     │
    │   /spec impl ············ Execute one task at a time        │
    │       │                     code → test → verify → commit   │
    │       ▼                                                     │
    │     Done                                                    │
    │                                                             │
    └─────────────────────────────────────────────────────────────┘
```

You review at every gate. Catch problems early when they're cheap to fix.

### Lightweight *// Small features, clear picture*

```
    /spec init "Add created_at timestamp to user profiles"
         │
         ▼
    /spec requirements user-timestamps -y     ─┐
    /spec design user-timestamps -y            ├── -y auto-approves
    /spec tasks user-timestamps -y            ─┘
         │
         ▼
    /spec impl user-timestamps
```

The **`-y`** flag auto-approves previous phases. SpecFlow still does the research and planning, you just don't stop to review each step. All the verification discipline of `impl` still applies: checks run, tests pass, commits follow conventions.

### Quick Implementation *// Bug fixes, small changes*

For changes where you know exactly what needs to happen. SpecFlow creates a minimal spec, and the implementation phase gives you the same verification discipline as a full spec:

```
/spec init "Fix timezone bug in date parser"
/spec tasks fix-timezone-bug -y
/spec impl fix-timezone-bug
```

The `-y` flag lets you skip straight to tasks without stopping for approvals. The `impl` phase still runs your project's check command, runs tests, and commits with conventional format. Same rigor as a full spec, without the ceremony.

> **Tip:** Even for quick work, steering docs help. If you've run `/steering` once, Claude already knows your project's architecture, conventions, and commands. That context improves every implementation.

---

## Commands

### `/spec`

| Command | What it does |
|---------|-------------|
| **`/spec init "<description>"`** | Create a new feature spec from a description |
| **`/spec requirements <feature>`** | Research the codebase, then write EARS-format requirements |
| **`/spec gap <feature>`** | Analyze existing code to find what can be reused |
| **`/spec design <feature>`** | Deep discovery + technical design document |
| **`/spec tasks <feature>`** | Decompose design into ordered, sized task files |
| **`/spec impl <feature>`** | Implement the next eligible task |
| **`/spec status [<feature>]`** | Show progress, approvals, and next steps |

> Add **`-y`** to `requirements`, `design`, or `tasks` to auto-approve prior phases.

### `/steering`

| Command | What it does |
|---------|-------------|
| **`/steering`** | Bootstrap core docs (product + tech stack) or show status |
| **`/steering <topic>`** | Create a reference doc for any topic (testing, API, security, etc.) |
| **`/steering sync`** | Update all steering docs to match current codebase |

---

## What Gets Created

```
your-project/
└── .agents/
    ├── reference/                        ← Project knowledge (from /steering)
    │   ├── product.md                       Product overview, personas, domains
    │   └── tech-stack.md                    Stack, architecture, commands
    │
    └── specs/                            ← Feature specs (from /spec)
        └── webhook-notifications/
            ├── spec.json                    Phase state + approval tracking
            ├── spec.md                      Feature overview + task index
            ├── requirements.md              EARS-format acceptance criteria
            ├── research.md                  Discovery findings
            ├── design.md                    Technical design + data models
            └── tasks/
                ├── 01-create-models.md      ┐
                ├── 02-add-service.md        ├─ Individual task files
                └── 03-wire-routes.md        ┘
```

**Commit your `.agents/` directory.** These files serve as living documentation that helps your team and future AI sessions understand why things were built the way they were.

---

## Under the Hood

### State Machine

Every spec tracks progress through `spec.json`:

```
  initialized
       │
       ▼
  requirements-generated ──── approved? ────►  design-generated
                                                     │
                                                approved?
                                                     │
                                                     ▼
                                              tasks-generated
                                                     │
                                                approved?
                                                     │
                                                     ▼
                                               in-progress ──── all done? ──── complete
```

### Session Resilience

Conversations get long, context gets compacted, sessions restart. SpecFlow handles all of it because every piece of state lives in files, not conversation memory:

| Mechanism | What it does |
|-----------|-------------|
| **Session start hook** | Detects active specs, injects context so Claude knows where you left off |
| **Pre-compact hook** | Preserves spec state when context is compressed |
| **Task files** | Each task is standalone with everything needed to implement it |

Start a fresh conversation and run `/spec impl webhook-notifications`. Claude reads the spec files and picks up the next task automatically.

### Research-First Interaction

When SpecFlow needs to write requirements or design a feature, it first:

1. Searches your codebase for related models, services, routes, and patterns
2. Reads existing code to understand conventions
3. Checks steering docs for architectural constraints
4. Explores the web for relevant library docs or patterns

*Then* it asks you questions, grounded in what it found. "I see you're using the repository pattern in `src/repositories/`. Should the new webhook service follow the same pattern?" is far more useful than "How should I structure the data layer?"

### EARS Requirements

SpecFlow uses [EARS (Easy Approach to Requirements Syntax)](https://alistairmavin.com/ears/) for acceptance criteria. Five patterns that produce unambiguous, testable requirements:

```
  ┌────────────────────┬──────────────────────────────────────────────────┐
  │ Pattern            │ Template                                        │
  ├────────────────────┼──────────────────────────────────────────────────┤
  │ Ubiquitous         │ The system shall <action>                       │
  │ Event-driven       │ When <event>, the system shall <action>         │
  │ State-driven       │ While <state>, the system shall <action>        │
  │ Unwanted behavior  │ If <condition>, the system shall <action>       │
  │ Optional           │ Where <feature>, the system shall <action>      │
  └────────────────────┴──────────────────────────────────────────────────┘
```

EARS requirements translate directly into test cases, making verification straightforward during implementation.

---

## Tips

- **Start with `/steering`.** Five minutes of setup gives every future spec better context.
- **Use `/spec status`.** Quick snapshot of where things stand across all features.
- **Fresh conversations for heavy phases.** After design or task generation, start a new session. SpecFlow picks up where you left off.
- **The `-y` flag is your friend.** Skip reviews when you know what you want.

---

## License

MIT

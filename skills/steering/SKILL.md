---
name: steering
description: Build and maintain project knowledge for spec-driven development. Use when setting up specflow in a new project, creating any reference document (product overview, tech stack, testing conventions, API standards, security practices, deployment workflows, or any project-specific topic), updating existing docs, or when the user runs /steering. Triggers on "set up steering", "initialize project context", "create a reference doc for X", "add testing docs", "document our API conventions", or any request to capture project knowledge in .agents/reference/ for AI agents.
---

# Steering

Build and maintain the project knowledge base that powers spec-driven development. Steering docs live in `.agents/reference/` and get loaded by the spec workflow to ground requirements, designs, and tasks in the project's reality.

All file paths in this skill are relative to this skill's root directory (the directory containing this SKILL.md).

The two core docs — `product.md` and `tech-stack.md` — are the minimum. Beyond those, any topic that shapes how features are designed and built deserves its own reference doc: testing conventions, API standards, security practices, deployment workflows, database patterns, or anything specific to the project.

## Mode Detection

Parse the input to determine what the user wants:

| Input | Mode |
|-------|------|
| No args, `.agents/reference/` doesn't exist | **Bootstrap** — generate core docs |
| No args, `.agents/reference/` exists | **Status** — report what exists, suggest what's missing |
| `sync` or "update steering" | **Sync** — detect drift across all existing docs |
| `<topic>` (e.g., "testing", "security", "workflows") | **Generate** — create or update a reference doc for that topic |

---

## Bootstrap Mode

Run when the project has no steering docs yet. Produces the two core documents every project needs, then suggests additional topics based on what the codebase reveals.

### Step 1: Scan the Codebase

Read these files to build a picture of the project. Skip any that don't exist.

**Project identity:**
- `README.md` — product description, features, getting started
- `CLAUDE.md` / `AGENTS.md` — existing AI agent context (extract rather than duplicate)
- `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod` / `Gemfile` / `build.gradle` / `pom.xml` — name, description, dependencies
- `.env.example` / `.env.local` — environment structure (never read actual `.env` files)

**Architecture signals:**
- Top-level directory listing (`ls` the root and key subdirectories)
- Framework config: `tsconfig.json`, `vite.config.*`, `next.config.*`, `nuxt.config.*`, `angular.json`, `app.json` (Expo)
- Infrastructure: `Dockerfile`, `docker-compose.yml`, `Makefile`, `Justfile`, `Taskfile.yml`
- Database: `alembic.ini`, `prisma/schema.prisma`, `drizzle.config.*`, `knexfile.*`, `cube.js`
- CI: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`

**Code patterns (sample, don't read everything):**
- 2-3 representative source files from the main source directory
- Test directory structure and 1 example test file
- API route/handler directory if it exists

**Existing context (if partially set up):**
- `.agents/reference/*` — any existing steering docs
- `docs/` — project documentation

### Step 2: Derive Product Context

From the scan, extract:
- **What the product does** — purpose, core value proposition
- **Who uses it** — user types or personas
- **Key domains** — major functional areas
- **Design principles** — recurring patterns or values visible in the code

Present findings to the user in a structured summary:

```
Here's what I found about the product. Correct anything wrong
and fill in what I couldn't determine from code:

Product: [name]
Purpose: [what it does]
Users: [who uses it]
Domains: [list]
Principles: [list]

What am I missing? Any corrections?
```

Wait for user input before writing. The user knows the product better than the code reveals — the scan gives a starting point, not the final answer.

### Step 3: Derive Tech Context

From the scan, extract:
- **Language and runtime**
- **Framework and key libraries**
- **Database and data layer**
- **Architecture pattern** — layered, MVC, microservices, monolith, serverless
- **Build/check/test commands**
- **Directory structure** — layout with roles

Present findings to the user:

```
Here's the tech stack I found. Correct anything and add what's missing:

Stack: [language] + [framework] + [database]
Architecture: [pattern]
Key commands:
  Check: [lint + format + typecheck command]
  Test: [test command]
  Dev: [local dev command]

Directory layout:
  src/     — [role]
  tests/   — [role]
  ...

Any corrections or additions?
```

Wait for user input before writing.

### Step 4: Write Core Docs

Create the directory structure:

```bash
mkdir -p .agents/reference
mkdir -p .agents/specs
```

Generate the two core files using `templates/product.md` and `templates/tech-stack.md` as structural guides. Read each template first, then fill every section with findings + user corrections.

### Step 5: Suggest Additional Topics

After writing core docs, look at what the codebase revealed during the scan and suggest additional reference docs that would be valuable. Only suggest topics where the scan found substantial, documentable patterns.

```
Core steering docs created:
  .agents/reference/product.md    — [N] lines
  .agents/reference/tech-stack.md — [N] lines
  .agents/specs/                  — ready for specs

Based on what I found in the codebase, these additional reference docs
could help the spec workflow produce better output:

  /steering <topic>  — [why it would be useful for this project]
  /steering <topic>  — [why it would be useful for this project]

These are optional — the spec workflow works with just the core docs.

Next: /spec init "<feature description>" to start your first spec.
```

Only suggest topics where the scan found real signals — established test suites, CI pipelines, database patterns, API conventions, etc. Don't suggest docs for things the project doesn't have yet.

---

## Generate Mode

Create a reference doc for any topic. This is the heart of steering's extensibility — the project knowledge base grows as the project's needs become clear.

### Step 1: Identify the Topic

Parse the topic from the argument. The topic becomes both the filename (`<topic>.md`) and the focus of the scan. Common topics include testing, API standards, security, deployment, database, error handling, authentication, workflows — but any topic is valid.

### Step 2: Targeted Scan

Scan the parts of the codebase relevant to the topic. The scan should be focused — don't re-read the entire project, just the areas that matter.

Think about what code, config, and documentation would contain conventions and patterns for the topic. For example:
- A doc about testing would look at test directories, test config, CI test steps, test fixtures, and example test files to extract the project's testing patterns
- A doc about deployment would look at CI/CD configs, Dockerfiles, infrastructure-as-code, environment configs, and deployment scripts
- A doc about database conventions would look at schema files, migrations, ORM config, model definitions, and query patterns

Read the relevant files, extract the conventions and patterns that are actually practiced (not aspirational), and note what's consistent versus inconsistent.

If the scan reveals very little about the topic — no established patterns, no config, no relevant code — tell the user rather than generating a thin doc:

```
I didn't find much about [topic] in the codebase — no established
patterns or conventions to document yet.

If you'd like, I can create a doc capturing your intended approach
for [topic], but steering docs work best when grounded in existing code.
```

### Step 3: Present and Validate

Show the user what was found:

```
Here's what I found about [topic] in the codebase:

[Organized findings — conventions, patterns, tools, commands]

Anything to correct or add? This will become .agents/reference/<topic>.md
and the spec workflow will use it when designing and implementing features
that touch [topic].
```

Wait for user input. The user may have context about why certain patterns exist, planned changes, or conventions that aren't visible in code yet.

### Step 4: Write the Doc

Save to `.agents/reference/<topic>.md`. The document should follow a consistent structure:

```markdown
# <Topic>: <Project Name>

## Overview
[One paragraph: what role does this topic play in the project and
why does it matter for spec authoring]

## [Topic-specific sections]
[Conventions, patterns, tools, commands — whatever is relevant.
Organize by what's most useful for someone writing specs and
implementing features. Use tables for structured information,
prose for context and rationale.]

## Key Decisions
[Decisions about this topic that constrain how features are built.
Only include decisions that the spec workflow needs to know about.]
```

Adapt the sections to the topic — a testing doc has different sections than a security doc. The structure should serve the content, not the other way around.

Report after writing:

```
Created .agents/reference/<topic>.md — [N] lines

The spec workflow will now load this when working on features
that involve [topic].
```

---

## Sync Mode

Update all existing steering docs to match the current state of the codebase.

### Step 1: Inventory

Read all files in `.agents/reference/` and list what exists.

### Step 2: Detect Drift

For each existing doc, scan the relevant parts of the codebase and compare:

- New patterns or conventions that aren't documented?
- Documented patterns that no longer match the code?
- New tools, dependencies, or commands?
- Stale references to removed code?

### Step 3: Propose and Apply

Present a summary of changes per file:

```
Changes detected:

<filename>.md:
  + New: [addition]
  ~ Changed: [what's different]
  - Stale: [no longer accurate]

Apply updates? (I'll show changes before writing.)
```

Apply only what the user approves. Update in place — don't rewrite from scratch unless the doc has drifted significantly.

---

## Status Mode

When `.agents/reference/` exists and no action was requested, report what's there and what might be missing.

```
Steering docs:
  product.md    — [N] lines, last modified [date]
  tech-stack.md — [N] lines, last modified [date]
  testing.md    — [N] lines, last modified [date]

To update: /steering sync
To add:    /steering <topic>
```

If the scan reveals topics that would be valuable but don't have docs yet, suggest them — but only based on real codebase signals, not generic recommendations.

---

## Quality Standards

Steering docs power every spec session. They get loaded into context alongside spec documents, so quality matters.

**Accurate over comprehensive** — only document what's confirmed in the codebase. A wrong convention will propagate into every spec.

**Concise** — target 50-150 lines per file. These are reference material, not textbooks. If a doc is growing past 200 lines, consider whether some content belongs in CLAUDE.md or a more specific reference doc.

**Specific** — use actual names, actual paths, actual commands. "Next.js 14 with App Router + Prisma + PostgreSQL" beats "Modern web framework with database."

**Grounded in code** — every convention documented should be traceable to actual code in the project. If a pattern appears in the codebase, document it. If it's aspirational, leave it out — steering docs describe what is, not what should be.

**Complementary** — if CLAUDE.md or AGENTS.md already documents something, don't duplicate it. Steering docs capture project knowledge specifically structured for the spec workflow.

## What Steering Docs Are Not

Steering docs serve one audience: the spec workflow. They're not general-purpose documentation.

- Not a README — that's for humans onboarding to the project
- Not a style guide — that lives in CLAUDE.md or linting config
- Not comprehensive documentation — that lives in docs/
- Not aspirational — they describe what the project does today, not what it should do

Each doc answers a specific question the spec workflow would otherwise have to guess at: What does this product do? How is it built? How do we test? What are the API conventions? If a doc doesn't help the spec workflow produce better specs, it doesn't belong here.

# Discovery — Research and Analysis Process

Discovery produces `research.md` — a document capturing everything learned before design
begins. The depth of research scales with feature complexity. Not every feature needs a
full investigation; simple additions can follow the minimal path.

## Complexity Tiers

### Full Discovery (Complex / Novel Features)

Use when: new architectural patterns, unfamiliar integrations, significant data model
changes, or features that span multiple services.

**Six steps, in order:**

1. **Requirements Analysis** — Parse every requirement from `requirements.md`. Identify ambiguities, implicit dependencies, and unstated constraints. List open questions.
2. **Existing Implementation Analysis** — Read the relevant parts of the codebase. Map current architecture: which modules, services, and data models are involved. Note existing patterns the new feature must follow.
3. **Technology Research** — Verify that libraries, frameworks, or APIs the feature depends on work as expected in the project's current versions. Look up official documentation for the specific APIs you plan to use — don't rely on prior knowledge alone, since APIs change between versions. Evaluate options against project constraints (per `tech-stack.md` if available). See the External Research section for tool guidance.
4. **External Dependencies** — Identify third-party APIs, services, or data sources. Document their contracts, rate limits, failure modes, and versioning policies.
5. **Architecture Pattern Analysis** — Determine which architectural patterns apply (event-driven, request-response, batch, etc.). Validate that the chosen approach fits the project's existing conventions.
6. **Risk Assessment** — List technical risks, unknowns, and mitigation strategies. Flag anything that could block implementation or require a spike.

### Light Discovery (Extensions / Moderate Features)

Use when: adding to an existing pattern, moderate scope, familiar technology, contained
within one or two modules.

**Four steps:**

1. **Extension Point Analysis** — Find the exact locations in the codebase where the feature hooks in. Identify the interfaces, base classes, or patterns to follow.
2. **Dependency Check** — Confirm all required libraries and services are already available. Note any new dependencies and their compatibility.
3. **Tech Verification** — Look up documentation for any library features or API endpoints the feature will use, especially ones the project hasn't used before. Confirm they exist in the project's current version and work as expected. See the External Research section for tool guidance.
4. **Integration Risk** — Identify anything that could break existing functionality. Note required changes to shared code, schemas, or contracts.

### Minimal Discovery (Simple / Repetitive Changes)

Use when: the feature follows an established pattern exactly and touches 1-2 files.

**One step:**

1. **Pattern Check** — Find one similar, already-implemented example in the codebase. Confirm the new feature can follow the same pattern without deviation. If it cannot, escalate to Light Discovery.

## Escalation Criteria

Upgrade from **Minimal to Light** when:
- The pattern match is not exact (new fields, different validation, extra side effects).
- A new dependency or external call is required.

Upgrade from **Light to Full** when:
- Architecture questions arise that the existing codebase does not answer.
- Multiple services or data stores are affected.
- The team has no prior experience with a required technology.
- Risk assessment reveals potential data integrity or security concerns.

## External Research

Training data can be months behind the latest library releases. A design built on a deprecated API, a removed feature, or an outdated pattern wastes implementation time. Verifying technical assumptions against current documentation takes minutes and prevents hours of rework.

### When to Research

Research externally when the feature involves:
- Library or framework APIs you'll call directly — verify they exist and work as expected in the project's version
- Third-party service integrations — confirm current API contracts, rate limits, authentication flows
- Patterns where best practices evolve quickly — auth, real-time, file handling, mobile APIs, AI/ML integrations
- Any technology the project hasn't used before

Skip external research when the feature is purely internal logic with no library or service dependencies.

### Tool Priority

Use the most authoritative source available, working down this list:

| Priority | Tool | Best For |
|----------|------|----------|
| 1st | Documentation lookup tools (such as context7) | Library APIs, version-specific features, configuration options |
| 2nd | Web fetch tools (such as WebFetch) | Official documentation pages, API references, migration guides, changelogs |
| 3rd | Web search tools (such as WebSearch) | Recent ecosystem changes, community-reported issues, alternative approaches |

If a tool is not available in your environment, move to the next one. The key is verifying against current sources rather than relying solely on prior knowledge.

### Source Quality

Prioritize sources in this order:

1. **Official documentation** — Always the primary source.
2. **Recent blog posts / tutorials** (< 2 years old) — Practical patterns and real-world usage.
3. **Community forums** — Edge cases and known issues.
4. **Open-source reference implementations** — How others solved similar problems.

**Avoid:** outdated tutorials, AI-generated summaries without verification, single-source conclusions.

### Parallel Research with Subagents

For Full Discovery, codebase analysis and external documentation research can run in parallel. Dispatch subagents to investigate different areas simultaneously — this is faster and gives each researcher a fresh, focused context.

A typical split:
- **Codebase subagent** — explores the project's existing code, maps modules, traces data flows, identifies extension points
- **Documentation subagent** — looks up library docs, verifies API capabilities, checks current best practices using the tools above

Each subagent should return a focused report. Merge their findings into `research.md` before proceeding to design.

For Light or Minimal Discovery, subagents are usually unnecessary — the research scope is small enough to handle inline.

## Output: research.md Structure

```
# Research: <Feature Name>

## Discovery Tier
Full / Light / Minimal (with justification)

## Requirements Analysis
- Parsed requirements with IDs
- Open questions and clarifications

## Codebase Analysis
- Relevant modules and their roles
- Existing patterns to follow
- Extension points identified

## Technology Research (if applicable)
- Options evaluated
- Recommendation with rationale

## External Dependencies (if applicable)
- Service/API contracts
- Failure modes and fallbacks

## Risks and Mitigations
- Risk description → Mitigation strategy
- Unknowns that need spikes

## Decisions
- Key choices made during research
- Trade-offs and reasoning
```

Sections not applicable to the chosen tier may be omitted. The document should capture
enough context that a different developer could pick up the design phase without
re-doing the research.

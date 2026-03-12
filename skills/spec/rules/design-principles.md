# Design Principles — Technical Quality Standards

These principles govern what belongs in `design.md` and how design decisions should be
made. Design defines interfaces, contracts, and boundaries — not algorithm internals or
implementation minutiae.

## Core Principles

### 1. Type Safety
Use type annotations and strong typing as supported by the project's language. Define
clear input/output types for every interface boundary. Avoid `any`, untyped dictionaries,
or stringly-typed parameters at public interfaces.

### 2. Design vs Implementation
Design documents specify **what** and **why**, not **how** at the code level.

- **In scope:** API contracts, data models, component boundaries, error strategies, sequence flows.
- **Out of scope:** Algorithm implementations, loop structures, variable names, internal helper functions.

If a section reads like pseudocode, it has gone too far.

### 3. Visual Communication
Use Mermaid diagrams when they add clarity:

- **Include diagrams** for multi-component flows, state machines, and data pipelines.
- **Skip diagrams** for simple CRUD operations or single-service changes.

A diagram should communicate something that prose cannot express as clearly. Do not add
diagrams for decoration.

### 4. Component Design
- **Single Responsibility:** Each component does one thing well. If a component description
  requires "and", consider splitting it.
- **Dependency Direction:** Dependencies flow inward — higher-level modules depend on
  abstractions, not on lower-level implementation details.
- **Explicit Boundaries:** Define what each component owns (data, behavior, lifecycle) and
  what it delegates.

### 5. Data Modeling
Follow a three-stage progression:

1. **Domain Model** — Business entities and their relationships, free of storage concerns.
2. **Logical Model** — Structure the data for the chosen storage technology (tables, documents, graphs).
3. **Physical Model** — Indexes, partitioning, constraints — only when performance or integrity demands it.

Start from the domain. Let storage decisions follow, not lead.

### 6. Error Handling Strategy
- **Fail fast at boundaries:** Validate inputs at the edge of each component. Reject bad
  data before it propagates.
- **Domain-specific exceptions:** Define error types that carry meaning ("InsufficientBalance",
  not "ValueError").
- **Graceful degradation for external failures:** When third-party services fail, degrade
  functionality rather than crashing. Document fallback behavior in the design.

### 7. Requirements Traceability
Every design section must reference the requirement IDs it satisfies (e.g., "Implements 1.1, 2.3").
Use plain hierarchical numbers as defined in `requirements.md`. A design element that does
not trace to a requirement is either missing a requirement or is unnecessary scope.

### 8. Size Limit
Design documents should stay **under 1000 lines**. If a design exceeds this:

- Split into sub-designs per bounded context or module.
- Use a top-level design that references sub-documents.
- Each sub-design must be self-contained enough to implement independently.

## Design Quality Checklist

Before finalizing `design.md`, verify:

- [ ] Every requirement ID from `requirements.md` is referenced at least once.
- [ ] All component interfaces have typed inputs and outputs.
- [ ] Error handling strategy is defined for every external boundary.
- [ ] No implementation details have leaked into the design (no function bodies, no algorithms).
- [ ] Diagrams are present for multi-component flows and absent for trivial ones.
- [ ] The document is under 1000 lines.
- [ ] Naming follows the project's existing conventions (per `tech-stack.md` or codebase patterns).

# Design Review — Quality Assurance Process

Design review validates `design.md` before implementation begins. The goal is quality
over perfection — catch critical structural problems, not nitpick style preferences.

## Philosophy

- **Max 3 critical issues.** More than three means the design needs a rethink, not a patch.
  Focus on the highest-impact problems.
- **Interactive dialogue.** Review is a conversation. Ask clarifying questions before
  declaring something wrong.
- **GO / NO-GO decision.** Every review ends with a clear verdict. NO-GO requires specific,
  actionable fixes — never a vague "needs more work."
- **Respect the design boundary.** Review the design, not the future implementation.
  Do not critique code that has not been written.

## Review Criteria

Evaluate `design.md` against four standards:

### 1. Architecture Alignment
- **Layer compliance:** Does the design respect the project's architectural layers? Are
  responsibilities placed in the correct modules?
- **Pattern consistency:** Does it follow established patterns in the codebase, or does it
  introduce a new pattern? If new, is the deviation justified?
- **Dependency direction:** Do dependencies flow inward (toward abstractions), not outward
  (toward implementation details)?

### 2. Design Consistency
- **Naming conventions:** Do new components, fields, and endpoints follow the project's
  existing naming patterns?
- **Error handling:** Is the error strategy consistent with how the rest of the project
  handles failures?
- **Data modeling:** Do new models follow the project's conventions for field types,
  relationships, and constraints?
- **API design:** Are new endpoints or interfaces consistent in style, versioning, and
  response shape with existing ones?

### 3. Extensibility and Maintainability
- **Separation of concerns:** Can each component be understood, tested, and modified
  independently?
- **Testability:** Can the design be tested without elaborate setup or mocking of internals?
  Are boundaries clear enough for integration tests?
- **Complexity budget:** Is the design as simple as it can be while meeting requirements?
  Flag over-engineering and unnecessary abstractions.

### 4. Type Safety and Interface Design
- **Typed boundaries:** Are all inputs and outputs of public interfaces explicitly typed?
- **Input validation:** Does the design specify validation rules at entry points?
- **Clear contracts:** Can a developer implement a component using only the interface
  description, without reading the rest of the design?

## Review Process

1. **Load context.** Read `requirements.md`, `research.md` (if present), and `design.md`
   together. Understand the full picture before judging any part.
2. **Analyze.** Walk through the design against each of the four criteria above. Note
   observations, both positive and negative.
3. **Identify critical issues.** Select the 1-3 most impactful problems. A critical issue
   is one that would cause rework, incorrect behavior, or architectural drift if built as-is.
4. **Recognize strengths.** Call out what the design does well. This calibrates the review
   and reinforces good patterns.
5. **Decide GO / NO-GO.**
   - **GO:** No critical issues, or only minor suggestions. Implementation can proceed.
   - **GO with notes:** Minor issues noted but not blocking. Address during implementation.
   - **NO-GO:** 1-3 critical issues that must be resolved before implementation starts.

## Critical Issue Format

Each critical issue follows this structure:

```
### Issue: <Brief Title>

**Concern:** What is wrong or risky in the current design.
**Impact:** What goes wrong if this is built as-is.
**Suggestion:** A concrete alternative or fix.
**Traceability:** Which requirement IDs are affected (e.g., 1.2, 3.1).
**Evidence:** Reference the specific section of design.md.
```

## Common Pitfalls — What NOT to Review

- **Implementation details.** Do not critique algorithm choices, variable names, or code
  structure that the design does not specify.
- **Bikeshedding.** Do not debate naming preferences or formatting when the existing choice
  is functional and consistent.
- **Scope creep.** Do not request features or capabilities beyond what `requirements.md`
  defines. If requirements are missing, flag that separately.
- **Premature optimization.** Do not demand performance tuning in the design unless
  requirements specify performance targets.
- **Style over substance.** A design that is clear, correct, and complete is ready — even
  if you would have structured the document differently.

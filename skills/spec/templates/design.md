# Design: {{FEATURE_NAME}}

> Spec: ../spec.md
> Requirements: ../requirements.md
> Research: ../research.md
> Status: draft | approved
> Created: {{DATE}}

<!-- Authoritative technical design. Keep under 1000 lines. Skip sections that don't apply. -->

---

## Overview
<!-- 2-3 paragraphs: what, who, impact. -->
{{OVERVIEW}}

---

## Goals and Non-Goals

### Goals
- {{GOAL}}

### Non-Goals
- {{NON_GOAL}} — {{WHY_EXCLUDED}}

---

## Architecture

### Existing Code Analysis
<!-- What exists today and what changes. Skip for greenfield. -->
| Component | Current State | Proposed Change |
|-----------|--------------|-----------------|
| {{COMPONENT}} | {{CURRENT}} | {{PROPOSED}} |

### Technology Stack
<!-- Only layers impacted by this feature. -->
| Layer | Choice | Role |
|-------|--------|------|
| {{LAYER}} | {{TECH}} | {{ROLE}} |

---

## System Flows
<!-- Mermaid diagrams for key interactions. Skip for simple features. -->
{{FLOWS_OR_SKIP}}

---

## Requirements Traceability
| Requirement | Design Component | Notes |
|-------------|-----------------|-------|
| 1 | {{COMPONENT}} | {{NOTES}} |

---

## Components and Interfaces
### {{ComponentName}}
**Purpose:** {{WHAT_IT_DOES}}
**Requirements:** {{REQUIREMENT_IDS}}
**Interface:** (signatures, contracts)

---

## Data Models
### {{ModelName}}
| Field | Type | Constraints | Notes |
|-------|------|-------------|-------|
| {{FIELD}} | {{TYPE}} | {{CONSTRAINTS}} | {{NOTES}} |

---

## Error Handling
| Scenario | Error Type | Response | Recovery |
|----------|-----------|----------|----------|
| {{SCENARIO}} | {{EXCEPTION}} | {{CODE}} | {{STRATEGY}} |

---

## Testing Strategy
| Layer | What to Test | How |
|-------|-------------|-----|
| {{LAYER}} | {{WHAT}} | {{TOOL}} |

---

## Open Questions
- {{UNRESOLVED_ITEM}}

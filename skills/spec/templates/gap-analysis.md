# Gap Analysis: {{FEATURE_NAME}}

> Spec: ../spec.md
> Requirements: ../requirements.md
> Status: in-progress | complete
> Created: {{DATE}}

## Summary
<!-- One paragraph: what was surveyed, key findings, recommended approach. -->
{{SUMMARY}}

---

## Existing Assets
<!-- One subsection per category. Omit categories with no relevant assets. -->

### Models / Data
| Asset | Path | Reusable? | Notes |
|-------|------|-----------|-------|
| {{ASSET}} | `{{PATH}}` | reuse / extend / replace | {{NOTES}} |

### Business Logic
| Asset | Path | Reusable? | Notes |
|-------|------|-----------|-------|
| {{ASSET}} | `{{PATH}}` | reuse / extend / replace | {{NOTES}} |

### API / Routes
| Asset | Path | Reusable? | Notes |
|-------|------|-----------|-------|
| {{ASSET}} | `{{PATH}}` | reuse / extend / replace | {{NOTES}} |

### Background Jobs
| Asset | Path | Reusable? | Notes |
|-------|------|-----------|-------|
| {{ASSET}} | `{{PATH}}` | reuse / extend / replace | {{NOTES}} |

### Configuration
| Asset | Path | Reusable? | Notes |
|-------|------|-----------|-------|
| {{ASSET}} | `{{PATH}}` | reuse / extend / replace | {{NOTES}} |

### Tests
| Asset | Path | Reusable? | Notes |
|-------|------|-----------|-------|
| {{ASSET}} | `{{PATH}}` | reuse / extend / replace | {{NOTES}} |

---

## Requirements Mapping

| Requirement | Existing Asset | Gap | Effort |
|-------------|---------------|-----|--------|
| {{REQ_ID}}: {{BRIEF}} | `{{PATH}}` — {{MATCH_TYPE}} | {{WHAT_IS_MISSING}} | S / M / L |

<!-- Effort: S = minor change, M = moderate work, L = significant new code. -->

---

## Conventions Observed
<!-- Patterns the existing codebase follows that the new feature should match. -->

- **Naming:** {{NAMING_CONVENTIONS}}
- **Code organization:** {{WHERE_NEW_FILES_GO}}
- **Error handling:** {{ERROR_PATTERNS}}
- **Testing:** {{TEST_STRUCTURE_AND_PATTERNS}}
- **Architecture:** {{ARCHITECTURAL_PATTERNS}}

---

## Integration Surfaces
<!-- Where the new feature connects to existing code. -->

- **Data dependencies:** {{EXISTING_MODELS_TABLES}}
- **Service dependencies:** {{EXISTING_SERVICES_CALLED}}
- **API surface:** {{EXISTING_ENDPOINTS_EXTENDED}}
- **Event surfaces:** {{EVENTS_PRODUCED_OR_CONSUMED}}
- **Shared utilities:** {{HELPERS_MIDDLEWARE_DECORATORS}}

---

## Approach Options

### Option A: {{LABEL}}
<!-- e.g., "Extend Existing" -->
{{DESCRIPTION}}
- **Effort:** {{ESTIMATE}}
- **Risk:** {{LEVEL}}
- **Tradeoffs:** {{TRADEOFFS}}

### Option B: {{LABEL}}
<!-- e.g., "Create New" -->
{{DESCRIPTION}}
- **Effort:** {{ESTIMATE}}
- **Risk:** {{LEVEL}}
- **Tradeoffs:** {{TRADEOFFS}}

### Option C: {{LABEL}}
<!-- e.g., "Hybrid" — omit if not applicable -->
{{DESCRIPTION}}
- **Effort:** {{ESTIMATE}}
- **Risk:** {{LEVEL}}
- **Tradeoffs:** {{TRADEOFFS}}

---

## Recommendation

**Selected:** Option {{LETTER}} — {{LABEL}}

**Rationale:** {{WHY_THIS_APPROACH}}

<!-- Include the user's preference if discussed during the phase. -->

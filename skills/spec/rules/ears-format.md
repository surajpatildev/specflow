# EARS Format — Acceptance Criteria Syntax

EARS (Easy Approach to Requirements Syntax) provides five structured patterns for writing
unambiguous, testable acceptance criteria. Every requirement in `requirements.md` should use
one of these patterns — they produce consistently testable, clear criteria.

## The Five Patterns

### 1. Ubiquitous — Unconditional Constraints
**Template:** `The <component> shall <behavior>.`
Always active, no trigger or condition required.

> *Example:* The AuthService shall hash passwords using bcrypt with a minimum cost factor of 12.

### 2. Event-Driven — Reactive Behaviors
**Template:** `When <trigger>, the <component> shall <behavior>.`
Fires once in response to a discrete event.

> *Example:* When a user submits a registration form, the AccountService shall create a new account and send a confirmation email.

### 3. State-Driven — Ongoing Conditions
**Template:** `While <state>, the <component> shall <behavior>.`
Active continuously as long as the state holds.

> *Example:* While the system is in maintenance mode, the API Gateway shall return 503 responses to all non-admin requests.

### 4. Unwanted Behavior — Error / Edge-Case Handling
**Template:** `If <condition>, the <component> shall <behavior>.`
Guards against failure states and exceptional conditions.

> *Example:* If the payment gateway returns a timeout, the OrderService shall retry the request up to 3 times with exponential backoff.

### 5. Optional — Configurable Features
**Template:** `Where <feature is supported>, the <component> shall <behavior>.`
Applies only when a capability or configuration is present.

> *Example:* Where two-factor authentication is enabled, the LoginService shall require a TOTP code after password verification.

## Quality Rules

Every requirement should be:

- **Testable** — Can be verified with a concrete pass/fail check.
- **Singular** — Expresses exactly one behavior. Split compound requirements.
- **Specific** — Uses measurable values, not vague terms ("fast", "secure", "user-friendly").
- **Unambiguous** — Only one reasonable interpretation exists.

## Writing Guidelines

1. **Use concrete component names.** Write "the NotificationService shall..." not "the system shall...". Name the specific service, module, or component responsible.
2. **Number hierarchically.** Group related requirements: 1.1, 1.2, 2.1, 2.2.
3. **One behavior per requirement.** If you write "and" or "or", consider splitting.
4. **Include boundary values.** "within 200ms", "up to 5 retries", "maximum 100 items".

## Common Mistakes

| Mistake | Bad Example | Fix |
|---|---|---|
| Vague behavior | "The system shall be fast" | "The SearchService shall return results within 200ms for queries under 1000 records" |
| Missing component | "Shall validate input" | "The API Gateway shall validate input against the request schema" |
| Compound requirement | "The service shall create the record and notify the user" | Split into two requirements: one for creation, one for notification |
| Implementation detail | "The service shall use a LEFT JOIN to fetch records" | "The ReportService shall include records that have no associated metadata" |
| Wrong pattern | "When the system is running, it shall log errors" (ubiquitous, not event) | "The LoggingService shall write all errors to the configured log sink" |

## Choosing the Right Pattern

- No condition at all? **Ubiquitous.**
- Something happens once (click, message, cron tick)? **Event-driven.**
- A mode or state persists over time? **State-driven.**
- Handling an error or edge case? **Unwanted behavior.**
- Depends on a feature flag or optional capability? **Optional.**

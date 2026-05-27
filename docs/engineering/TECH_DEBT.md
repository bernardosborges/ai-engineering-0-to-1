# Technical debt register

Tracks technical decisions deferred during development with explicit context, deferral reason, trigger to revisit, and effort estimate. Each entry is actionable: when its trigger fires, the entry becomes a planned task.

This register exists to make **deliberate deferrals visible**. Items in this file are not bugs and not unfinished work — they are informed decisions to do something later, recorded so the rationale survives team rotation and so technical due diligence can confirm the team thought about each item.

---

## Format

Each entry follows the same structure:

- **Context** — what currently exists in the codebase
- **Deferral reason** — why we did not address it during the originating task
- **Trigger to revisit** — concrete condition that promotes this to a planned task
- **Estimated effort** — rough size, in task-units
- **References** — relevant ADRs, tasks, or docs

Entries are numbered sequentially as `TD-NNN` and grouped by priority.

---

## Priority levels

- **High priority** — items blocking production or with a deadline. Must be addressed before going live or before a defined event.
- **Medium priority** — items affecting near-term feature work. Will likely be picked up during the planned roadmap.
- **Low priority** — items not blocking anything in the foreseeable future. Logged so the rationale survives.

Items move between priorities as the project evolves. Re-prioritizing is a normal part of reviewing this register.

---

## High priority — must be addressed before production

<!-- BOOTSTRAP:TD_HIGH_PRIORITY:START -->
> 📝 Populated as the project surfaces high-priority technical debt during development.
>
> Entries here block production or have a hard deadline. Typical examples:
>
> - Encryption at rest for sensitive fields when the project handles PII
> - Secrets management beyond environment variables before first production deploy
> - Missing observability for critical operations before going live
> - Compliance gaps that require resolution before processing real user data
<!-- BOOTSTRAP:TD_HIGH_PRIORITY:END -->

---

## Medium priority — affects feature work in foreseeable phases

<!-- BOOTSTRAP:TD_MEDIUM_PRIORITY:START -->
> 📝 Populated as the project consciously defers decisions during development.
>
> Entries here affect work in the near-term roadmap. Typical examples:
>
> - Premature abstractions deferred until a second consumer exists
> - Schema decisions deferred until a real use case emerges
> - Refactors deferred until pain is concrete
> - Performance optimizations deferred until measured bottlenecks appear
<!-- BOOTSTRAP:TD_MEDIUM_PRIORITY:END -->

---

## Low priority — not blocking anything in the foreseeable future

<!-- BOOTSTRAP:TD_LOW_PRIORITY:START -->
> 📝 Populated as the project notices minor improvements worth recording.
>
> Entries here are logged so the rationale survives but are not actively planned. Typical examples:
>
> - Cosmetic inconsistencies that work but violate a convention
> - Cleanup jobs for tables/files that grow slowly
> - Multi-region deployment before SLA commitments
> - Cost optimizations that only matter at scale not yet reached
<!-- BOOTSTRAP:TD_LOW_PRIORITY:END -->

---

## Items resolved

When an item is resolved by a task, move it here with the resolving task ID and date. **Do not delete entries** — the deferred-then-resolved trail is itself documentation.

<!-- BOOTSTRAP:TD_RESOLVED:START -->
> 📝 Populated as TD items are resolved by future tasks.
>
> Each resolved entry adds a `**Resolved in:** TSK-NNN (YYYY-MM-DD)` field at the top and keeps all the original sections (Context, Trigger, Effort, References) so future readers see what was promised vs delivered.
<!-- BOOTSTRAP:TD_RESOLVED:END -->

---

## How to add an entry

When a task consciously defers something:

1. Add a new entry to this file in the appropriate priority section.
2. Use the next sequential `TD-NNN` number.
3. Include all five required fields: Context, Deferral reason, Trigger, Effort, References.
4. Reference the entry from the task description that introduced the deferral.
5. Reference the entry from any ADR that authorized the deferral.

When a task resolves an entry:

1. Move the entry from its priority section to "Items resolved".
2. Add `**Resolved in:** TSK-NNN (YYYY-MM-DD)` at the top of the entry.
3. Keep the original Context, Trigger, and Effort sections.

When re-prioritizing an entry:

1. Move it to the appropriate section.
2. Add a one-line note explaining the change: `**Re-prioritized YYYY-MM-DD:** <reason>`.
3. Do not rewrite the original Context or Trigger.

---

## Entry template

Copy this block when adding a new entry. Replace placeholders, then delete this template-of-the-template line.

```
### TD-NNN — Short imperative title

**Context.** What currently exists in the codebase. Be specific: which file, which entity, what behavior. One paragraph.

**Deferral reason.** Why this was not addressed during the originating task. Cost vs benefit, scope discipline, lack of information, dependency on something not yet built. One paragraph.

**Trigger to revisit.** Concrete condition(s) that promote this to a planned task. Use measurable thresholds when possible (volume, latency, error count) or named events (pre-production checklist, first production deploy, specific feature shipping).

**Estimated effort.** 1 task, ~200 lines. Or 2-3 tasks if larger. Include the main steps the work will involve.

**References.**
- ADR NNNN (if relevant)
- TSK-NNN (originating task)
- TSK-NNN (other related tasks)
- Other docs that mention this debt
```

<!--
PYTHON_REFERENCE_EXAMPLE — Real TD entries

The author's project has the following high-priority entries. Use as reference
for the level of detail, tone, and concreteness expected in a well-written
TD entry.

### TD-001 — Encryption at rest for sensitive personal data fields

**Context.** Personal data fields (tax_id, government_id, telegram_user_id when populated) are currently stored as plaintext in Postgres, access-controlled at the application layer. The privacy register classifies tax_id as "Sensitive, plaintext, access-controlled".

**Deferral reason.** MVP scope. Encryption at rest requires choosing between database-level encryption (encrypted volume on RDS), column-level encryption (pgcrypto), or application-level encryption (encrypt before insert). The decision depends on production deployment target and key management infrastructure, neither of which is settled at MVP.

**Trigger to revisit.** Before the first production user record is written. Specifically: as part of the pre-production checklist in docs/ROADMAP.md. Cannot defer past first real customer onboarding.

**Estimated effort.** 1 PR, ~200-400 lines:
- ADR documenting encryption strategy (volume vs column-level vs application-level)
- Migration adding encryption to selected columns
- Repository layer changes if application-level encryption is chosen
- Key management documentation
- Test coverage for encrypted-data flows

**References.**
- docs/engineering/SECURITY.md §10 (privacy data register)
- ADR 0015 (soft delete and anonymization policy)
- TSK-031 (introduced the deferred plaintext storage)

---

### TD-005 — DomainError base class

**Context.** Domain exceptions (e.g., OrderValidationError, AddressValidationError, InvalidEmailError) extend `Exception` directly. There is no shared base class for "any domain error".

**Deferral reason.** Introducing a base class with one or two consumers is premature abstraction. The current 5+ exceptions cross only a few aggregates and do not require unified handling at any layer.

**Trigger to revisit.** Either:
- 4+ exceptions cross multiple aggregates AND a use case or middleware needs to catch "any domain error" without listing each class
- A test pattern emerges where multiple exception types must be handled with shared logic
- API error response mapping requires categorizing exceptions by layer

**Estimated effort.** 1 small task:
- Introduce DomainError(Exception) in app/domain/exceptions/
- Update existing domain exceptions to extend it
- Document the convention in docs/engineering/CODE_STYLE.md

**References.**
- ADR 0015 (defers the abstraction)
- TSK-030 (initial domain exception layout)
- TSK-031 (added value object exceptions)
-->

# Docs Index

Navigation guide for this project's documentation. Organized in three layers:

- **By area** — the complete catalog of every document, grouped by area (Product / Engineering / Legal). Use this when you need a full map of what exists.
- **By task** — the most common scenarios with the docs to read in each. Use this when you're about to do something specific.
- **By topic** — quick lookup of where a topic is documented.

All paths are relative to the repo root. When referencing docs in prompts to Claude Code, prefix with `@` (e.g., `@docs/engineering/ARCHITECTURE.md`) so they load into context.

---

## By area

Three areas, each with an overview document at the root of `docs/` and a folder of detailed documents:

### Product

Overview: **[PRODUCT.md](PRODUCT.md)** — what the project does, for whom, and how. Includes how PDRs (Product Decision Records) work.

**Discovery documents (`docs/product/pd-*.md`)** — one per discovery dimension; created during BS-01:

<!-- BOOTSTRAP:PD_DOCS:START -->
> 📝 Populated during BS-01 (Product discovery) as each dimension's document is created.
>
> Expected entries (created in order):
>
> - **[product/pd-motivation.md](product/pd-motivation.md)** — why this system, why now, founder-market fit
> - **[product/pd-audience.md](product/pd-audience.md)** — who needs this, evidence of need, first concrete users
> - **[product/pd-scope.md](product/pd-scope.md)** — what the system does and what it explicitly doesn't
> - **[product/pd-business-model.md](product/pd-business-model.md)** — how the product makes money and operates in production
> - **[product/pd-risks.md](product/pd-risks.md)** — weak points, uncertainties, external risks, pre-mortem, success criteria
> - **[product/pd-workflows.md](product/pd-workflows.md)** — high-level narrative of how each actor interacts with the system
> - **[product/pd-mvp.md](product/pd-mvp.md)** — smallest viable version, validation hypotheses, distribution to first 100 users
<!-- BOOTSTRAP:PD_DOCS:END -->

**Feature documents (`docs/product/ft-*.md`)** — one per significant feature or transversal model; created during BS-02:

<!-- BOOTSTRAP:FT_DOCS:START -->
> 📝 Populated during BS-02 (Product document) as features get their own specs.
>
> Format for each entry: `**[product/ft-<feature>.md](product/ft-<feature>.md)** — one-line summary.`
<!-- BOOTSTRAP:FT_DOCS:END -->

**Product Decision Records (`docs/product/pdr/`)** — durable records of product decisions made over time:

- [product/pdr/TEMPLATE.md](product/pdr/TEMPLATE.md) — template for new PDRs

<!-- BOOTSTRAP:PDR_LIST:START -->
> 📝 Populated as PDRs are written over the project's life.
>
> Format for each entry: `**[product/pdr/NNNN-kebab-case-title.md](product/pdr/NNNN-kebab-case-title.md)** — one-line summary.`
<!-- BOOTSTRAP:PDR_LIST:END -->

**Templates and references:**

- [product/TEMPLATE.md](product/TEMPLATE.md) — structure for any `pd-*.md` or `ft-*.md`

---

### Engineering

Overview: **[ENGINEERING.md](ENGINEERING.md)** — architecture, conventions, processes, decision records. Includes how ADRs work.

**Conventions and policy (`docs/engineering/`):**

- **[engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md)** — layered architecture, components, flows, cross-cutting decisions
- **[engineering/STRUCTURE.md](engineering/STRUCTURE.md)** — folder and file layout
- **[engineering/TECH_STACK.md](engineering/TECH_STACK.md)** — dependencies, versions, what's explicitly not used
- **[engineering/CODE_STYLE.md](engineering/CODE_STYLE.md)** — how to write code in this project (includes database queries and migrations)
- **[engineering/LANGUAGE.md](engineering/LANGUAGE.md)** — language policy for code, comments, commits, user-facing content
- **[engineering/SECURITY.md](engineering/SECURITY.md)** — auth, secrets, input, output, privacy, AI-specific, incident response
- **[engineering/TESTING.md](engineering/TESTING.md)** — pyramid, markers, LLM testing, factories, coverage targets
- **[engineering/GIT.md](engineering/GIT.md)** — trunk-based, Conventional Commits, PR flow
- **[engineering/TECH_DEBT.md](engineering/TECH_DEBT.md)** — register of deferred technical decisions

**Architecture Decision Records (`docs/engineering/adr/`)** — durable records of architectural decisions:

- [engineering/adr/TEMPLATE.md](engineering/adr/TEMPLATE.md) — template for new ADRs

<!-- BOOTSTRAP:ADR_LIST:START -->
> 📝 Populated as ADRs are written. The bootstrap workflow may seed the first few during BS-03 (Architecture), BS-04 (Tools), and BS-05 (Conventions).
>
> Format for each entry: `**[engineering/adr/NNNN-kebab-case-title.md](engineering/adr/NNNN-kebab-case-title.md)** — one-line summary.`
<!-- BOOTSTRAP:ADR_LIST:END -->

---

### Roadmap

Overview: **[ROADMAP.md](ROADMAP.md)** — phase overview, conventions for tasks (TSK/BF/SA/decomposition), pre-production checklist. Includes how planning events (replanning, phase creation, convention changes) are handled.

**Per-phase task files (`docs/roadmap/`):**

- **[roadmap/phase-1-foundation.md](roadmap/phase-1-foundation.md)** — repository foundation (TSK-001..TSK-012)
- **[roadmap/phase-2-infrastructure.md](roadmap/phase-2-infrastructure.md)** — application infrastructure (TSK-013..TSK-022)

<!-- BOOTSTRAP:PHASE_FILES:START -->
> 📝 Populated during BS-07 (Roadmap) as phases beyond 1-2 are planned.
>
> Format for each entry: `**[roadmap/phase-N-<name>.md](roadmap/phase-N-<name>.md)** — one-line summary (TSK-NNN..TSK-MMM).`
<!-- BOOTSTRAP:PHASE_FILES:END -->

**Planning event prompts (`.ai/prompts/planning/`):**

Replanning events, phase creations, and convention changes are recorded as prompts under `.ai/prompts/planning/YYYY-MM-DD-<slug>.md`. They are produced by `.ai/workflows/ROADMAP-PLANNER.md` and applied by `.ai/workflows/ROADMAP-EDITOR.md`. Each prompt is listed in the ROADMAP.md "Planning events" section.

**Roadmap workflows:**

- [.ai/workflows/ROADMAP-PLANNER.md](../.ai/workflows/ROADMAP-PLANNER.md) — planning conversation for roadmap structural changes (Claude desktop)
- [.ai/workflows/ROADMAP-EDITOR.md](../.ai/workflows/ROADMAP-EDITOR.md) — applies a planning prompt to the roadmap (Claude Code)

---

### Legal

Overview: **[LEGAL.md](LEGAL.md)** — what this area covers, how the policy register works, and how Legal Decision Records (LDRs) are used.

- **[legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md)** — pre-legal register of considerations as they emerge (LP-NN entries)

**Legal Decision Records (`docs/legal/ldr/`)** — durable records of legal positions formed over time:

- [legal/ldr/TEMPLATE.md](legal/ldr/TEMPLATE.md) — template for new LDRs

<!-- BOOTSTRAP:LDR_LIST:START -->
> 📝 Populated as LDRs are written over the project's life, typically in response to counsel input, regulatory changes, or new jurisdictions.
>
> Format for each entry: `**[legal/ldr/NNNN-kebab-case-title.md](legal/ldr/NNNN-kebab-case-title.md)** — one-line summary.`
<!-- BOOTSTRAP:LDR_LIST:END -->

<!-- BOOTSTRAP:LEGAL_DOCS:START -->
> 📝 Populated as additional legal documents are added (e.g., specific compliance registers, region-specific notes, retention schedules).
>
> Format for each entry: `**[legal/<doc-name>.md](legal/<doc-name>.md)** — one-line summary.`
<!-- BOOTSTRAP:LEGAL_DOCS:END -->

---

## By task

### When planning what to build next

1. [ROADMAP.md](ROADMAP.md) — find the current phase and next task in the queue.
2. Check the task's `Depends on` to confirm prerequisites are done.
3. Use the Planner (`@.ai/workflows/TASK-PLANNER.md`) to discuss scope before generating the prompt.

### When starting on the project (new contributor)

Read in this order:

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — what the system does, how the pieces fit
2. [engineering/STRUCTURE.md](engineering/STRUCTURE.md) — where code lives in the repo
3. [engineering/TECH_STACK.md](engineering/TECH_STACK.md) — what we use and why
4. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — how to write code that fits
5. [engineering/GIT.md](engineering/GIT.md) — how to commit, branch, and open PRs
6. [ROADMAP.md](ROADMAP.md) — current build plan and what's next

That's the minimum context to start contributing.

### When adding a new feature

1. [ROADMAP.md](ROADMAP.md) — confirm the feature fits the current phase
2. [PRODUCT.md](PRODUCT.md) and the relevant `product/ft-*.md` for the feature
3. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — where the feature fits in the component map and flows
4. [engineering/STRUCTURE.md](engineering/STRUCTURE.md) — which folder each piece goes in
5. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — naming, typing, async patterns, error handling
6. [engineering/TESTING.md](engineering/TESTING.md) — what tests to write, where they live
7. [engineering/SECURITY.md](engineering/SECURITY.md) — security checklist before opening PR
8. [engineering/GIT.md](engineering/GIT.md) — commit format, branch name, PR size

### When understanding what a feature should do

1. [PRODUCT.md](PRODUCT.md) — overview and document map
2. The relevant `product/ft-*.md` for the feature
3. The relevant `product/pd-*.md` if you need product context (motivation, audience, scope)
4. Cross-reference with [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) for technical constraints

### When adding a new endpoint or external boundary

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — authentication and authorization rules
2. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — boundary conventions
3. [engineering/SECURITY.md](engineering/SECURITY.md) — input validation, response schemas, authorization in the use case layer
4. [engineering/TESTING.md](engineering/TESTING.md) — authorization test required for protected boundaries
5. [engineering/LANGUAGE.md](engineering/LANGUAGE.md) — endpoint paths and field names follow the language policy

### When adding a new use case

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — layering rules and use case responsibilities
2. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — naming patterns
3. [engineering/SECURITY.md](engineering/SECURITY.md) — authorization check in the use case, not just in the router
4. [engineering/TESTING.md](engineering/TESTING.md) — unit test with fakes; authorization test required

### When integrating with an LLM

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — all LLM access through a port, adapter in the infrastructure layer
2. [engineering/SECURITY.md](engineering/SECURITY.md) — prompt injection, data exfiltration, tool calling safety, PII in prompts
3. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — prompt files location and conventions
4. [engineering/TESTING.md](engineering/TESTING.md) — fake LLM adapter in unit tests; real API only behind explicit marker

### When writing a prompt for the application's LLM features

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — prompt files location, how they're loaded
2. [engineering/SECURITY.md](engineering/SECURITY.md) — delimit user input; validate output schema before triggering actions
3. [engineering/LANGUAGE.md](engineering/LANGUAGE.md) — system prompts language policy; explicit instruction if output must be in a specific language

### When touching the database

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — database is the source of truth; derived stores are rebuildable
2. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — session management, parametrized queries, Unit of Work pattern
3. [engineering/SECURITY.md](engineering/SECURITY.md) — parametrized queries only; raw SQL with string interpolation is banned
4. [engineering/STRUCTURE.md](engineering/STRUCTURE.md) — where repositories and mappers live

### When creating or changing a migration

1. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — the "Database and migrations" section, end to end
2. Check migration type: **schema** (DDL) or **data** (DML). Never mix in one file
3. If autogeneration is available in your tool: treat it as a starting point, not the final answer
4. If the change is destructive (drop, truncate, column removal): separate migration, explicit PR approval, backup evidence in PR description
5. If the change is a rename: rewrite any autogenerated `DROP+CREATE` as `ALTER ... RENAME` to preserve data
6. If the change is breaking in production: follow the expand/contract pattern (three deploys)
7. [engineering/GIT.md](engineering/GIT.md) — migrations are typically excluded from the PR size budget; one migration per PR as the default

### When modeling new data

1. [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) — what lives where (primary vs derived vs ephemeral)
2. [engineering/STRUCTURE.md](engineering/STRUCTURE.md) — folder layout for entities, value objects, ports, services
3. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — entity and value object patterns, ID generation, domain exceptions
4. [engineering/SECURITY.md](engineering/SECURITY.md) — if personal data, document legal basis and retention

### When adding a dependency

1. [engineering/TECH_STACK.md](engineering/TECH_STACK.md) — declare it in the right group
2. [engineering/TECH_STACK.md](engineering/TECH_STACK.md) — check the "Explicitly NOT used" list before adding
3. [engineering/SECURITY.md](engineering/SECURITY.md) — vulnerability scan must pass
4. Consider whether the decision deserves an ADR

### When making a significant technical decision

1. [engineering/adr/TEMPLATE.md](engineering/adr/TEMPLATE.md) — copy as the next `NNNN-kebab-case-title.md` in `engineering/adr/`
2. Fill all sections (Context, Decision, Consequences, Alternatives rejected)
3. Reference the ADR from the most affected doc (ARCHITECTURE, TECH_STACK, CODE_STYLE)
4. After the ADR is accepted, run [@.ai/workflows/ADR-PROPAGATOR.md](../.ai/workflows/ADR-PROPAGATOR.md) to propagate consequences into other docs

### When making a significant product decision

1. [product/pdr/TEMPLATE.md](product/pdr/TEMPLATE.md) — copy as the next `NNNN-kebab-case-title.md` in `product/pdr/`
2. Fill all sections following the template
3. Reference the PDR from the most affected `pd-*.md` or `ft-*.md`
4. After the PDR is accepted, run [@.ai/workflows/PDR-PROPAGATOR.md](../.ai/workflows/PDR-PROPAGATOR.md) to propagate consequences across product docs

### When forming or changing a legal position

1. [LEGAL.md](LEGAL.md) — read how POLICY_CONSIDERATIONS and LDRs relate
2. Identify which LP entry(ies) in [legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md) this affects, or note that you are forming a new position
3. [legal/ldr/TEMPLATE.md](legal/ldr/TEMPLATE.md) — copy as the next `NNNN-kebab-case-title.md` in `legal/ldr/`
4. Fill all sections, marking each Decision part as `[Confirmed by counsel]`, `[Preliminary]`, or `[Pending further review]`
5. After the LDR is accepted, run [@.ai/workflows/LDR-PROPAGATOR.md](../.ai/workflows/LDR-PROPAGATOR.md) to propagate consequences across affected docs (POLICY_CONSIDERATIONS.md, SECURITY.md, product docs, etc.)

### When changing the roadmap structure (decomposition, insertion, renumbering, new phase, convention change)

1. In Claude desktop, invoke [@.ai/workflows/ROADMAP-PLANNER.md](../.ai/workflows/ROADMAP-PLANNER.md) and conduct the planning conversation. The Planner produces a detailed planning prompt
2. Save the prompt at `.ai/prompts/planning/YYYY-MM-DD-<slug>.md` with a descriptive slug (e.g., `decompose-tsk-010`, `create-phase-4`, `insert-billing-task`)
3. In a new Claude Code session, invoke [@.ai/workflows/ROADMAP-EDITOR.md](../.ai/workflows/ROADMAP-EDITOR.md). It picks up the most recent prompt, presents an execution plan, and applies the changes after your confirmation
4. Review the resulting PR and merge

For the policy on what gets frozen in merged content vs what gets updated, see the "Replanning event" section in [ROADMAP.md](ROADMAP.md) and the "Policy: stale references in merged content" section in [.ai/workflows/ROADMAP-PLANNER.md](../.ai/workflows/ROADMAP-PLANNER.md).

### When handling user input

1. [engineering/SECURITY.md](engineering/SECURITY.md) — validation schema with size limits
2. [engineering/SECURITY.md](engineering/SECURITY.md) — if input reaches an LLM, wrap in delimited sections, never as instruction
3. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — value objects for structured inputs
4. [engineering/LANGUAGE.md](engineering/LANGUAGE.md) — error message language policy

### When handling a secret or sensitive data

1. [engineering/SECURITY.md](engineering/SECURITY.md) — never commit; use protected types; rotation rules
2. [engineering/SECURITY.md](engineering/SECURITY.md) — never in responses
3. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — access config via typed entry point, never environment variables directly
4. [engineering/TESTING.md](engineering/TESTING.md) — no secrets in fixtures

### When writing tests

1. [engineering/TESTING.md](engineering/TESTING.md) — the whole doc
2. [engineering/STRUCTURE.md](engineering/STRUCTURE.md) — tests mirror the application structure
3. [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) — same language rules apply to tests

### When committing

1. [engineering/GIT.md](engineering/GIT.md) — Conventional Commits format, character limit, language, mood
2. [engineering/LANGUAGE.md](engineering/LANGUAGE.md) — commit message language

### When deferring a technical decision

1. [engineering/TECH_DEBT.md](engineering/TECH_DEBT.md) — add an entry with context, deferral reason, trigger to revisit, effort estimate
2. Reference the entry in the PR description that introduced the deferral
3. If an ADR authorized the deferral, cross-reference both

### When identifying a legal or policy concern (without yet forming a position)

1. [LEGAL.md](LEGAL.md) — read how the register works
2. [legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md) — add a new LP-NN entry with status `open`, filling all six fields (Situation, Implication, Preliminary position, Affects, Origin, Status)
3. If you are also forming a concrete position on this concern, write an LDR — see "When forming or changing a legal position"

### When opening a PR

1. [engineering/GIT.md](engineering/GIT.md) — PR title follows Conventional Commits; size cap; branch rebased
2. [engineering/SECURITY.md](engineering/SECURITY.md) — security checklist
3. [.github/PULL_REQUEST_TEMPLATE.md](../.github/PULL_REQUEST_TEMPLATE.md) — fill out the PR template inline
4. [ROADMAP.md](ROADMAP.md) — update the "Status" and "PR link" of the corresponding task entry

### When a PR is merged

1. [ROADMAP.md](ROADMAP.md) — mark the entry as ✅ Complete
2. Fill the "Learnings" subsection: what was done, what surprised you, what you learned
3. If the phase is complete, fill the phase retrospective

### When reviewing a PR

1. Does it match the architecture (layers respected)? → [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md)
2. Does it follow code style? → [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md)
3. Are tests adequate (authorization test for new use cases/endpoints)? → [engineering/TESTING.md](engineering/TESTING.md)
4. If migration: autogenerate reviewed? destructive ops flagged? → [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md)
5. Any secrets, missing validation, unsanitized output? → [engineering/SECURITY.md](engineering/SECURITY.md)
6. Any new dependency justified? → [engineering/TECH_STACK.md](engineering/TECH_STACK.md)

<!-- BOOTSTRAP:TASK_ENTRIES_EXTENSIONS:START -->
> 📝 Extended during BS-06 (Docs map) and as the project develops domain-specific tasks.
>
> Additional "By task" entries are added here as the project encounters recurring scenarios specific to its domain — e.g., "When curating the catalog", "When designing an LLM-driven interaction", "When handling a specific compliance event". The pattern is the same: short title, ordered list of docs to consult.
<!-- BOOTSTRAP:TASK_ENTRIES_EXTENSIONS:END -->

---

## By topic

| Topic | Primary doc | Secondary |
|---|---|---|
| What the product is | [PRODUCT.md](PRODUCT.md) | `product/pd-*.md` |
| Product motivation | [product/pd-motivation.md](product/pd-motivation.md) | — |
| Product audience | [product/pd-audience.md](product/pd-audience.md) | — |
| Product scope | [product/pd-scope.md](product/pd-scope.md) | — |
| Business model | [product/pd-business-model.md](product/pd-business-model.md) | — |
| Product risks and validation | [product/pd-risks.md](product/pd-risks.md) | [product/pd-mvp.md](product/pd-mvp.md) |
| User workflows (high level) | [product/pd-workflows.md](product/pd-workflows.md) | `product/ft-*.md` |
| MVP scope | [product/pd-mvp.md](product/pd-mvp.md) | [ROADMAP.md](ROADMAP.md) |
| Specific feature behavior | `product/ft-<feature>.md` | [PRODUCT.md](PRODUCT.md) |
| Product decisions (durable record) | [product/pdr/](product/pdr/) | [PRODUCT.md](PRODUCT.md) |
| Component map | [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) | [engineering/STRUCTURE.md](engineering/STRUCTURE.md) |
| Folder layout | [engineering/STRUCTURE.md](engineering/STRUCTURE.md) | — |
| Domain exceptions | [engineering/STRUCTURE.md](engineering/STRUCTURE.md) | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) |
| Domain services | [engineering/STRUCTURE.md](engineering/STRUCTURE.md) | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) |
| Repository pattern | [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) | [engineering/STRUCTURE.md](engineering/STRUCTURE.md) |
| Build order and phases | [ROADMAP.md](ROADMAP.md) | `roadmap/phase-*.md` |
| Dependencies and versions | [engineering/TECH_STACK.md](engineering/TECH_STACK.md) | — |
| Code style rules | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) | — |
| Naming (classes, files, etc) | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) | [engineering/LANGUAGE.md](engineering/LANGUAGE.md) |
| Async patterns | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) | [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) |
| Database queries and sessions | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) | [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) |
| Database migrations | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) | — |
| Error handling | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) | [engineering/SECURITY.md](engineering/SECURITY.md) |
| Authentication | [engineering/SECURITY.md](engineering/SECURITY.md) | [engineering/ARCHITECTURE.md](engineering/ARCHITECTURE.md) |
| Authorization | [engineering/SECURITY.md](engineering/SECURITY.md) | [engineering/TESTING.md](engineering/TESTING.md) |
| Secrets | [engineering/SECURITY.md](engineering/SECURITY.md) | [engineering/CODE_STYLE.md](engineering/CODE_STYLE.md) |
| Input validation | [engineering/SECURITY.md](engineering/SECURITY.md) | — |
| Rate limiting | [engineering/SECURITY.md](engineering/SECURITY.md) | — |
| Privacy and compliance | [engineering/SECURITY.md](engineering/SECURITY.md) | [LEGAL.md](LEGAL.md) |
| Audit logging | [engineering/SECURITY.md](engineering/SECURITY.md) | — |
| AI / LLM security | [engineering/SECURITY.md](engineering/SECURITY.md) | — |
| Testing strategy | [engineering/TESTING.md](engineering/TESTING.md) | — |
| LLM testing | [engineering/TESTING.md](engineering/TESTING.md) | — |
| Factories vs fixtures | [engineering/TESTING.md](engineering/TESTING.md) | — |
| Git workflow | [engineering/GIT.md](engineering/GIT.md) | — |
| Commits | [engineering/GIT.md](engineering/GIT.md) | [engineering/LANGUAGE.md](engineering/LANGUAGE.md) |
| PRs | [engineering/GIT.md](engineering/GIT.md) | — |
| Language policy | [engineering/LANGUAGE.md](engineering/LANGUAGE.md) | — |
| Architecture decisions (records) | [engineering/adr/](engineering/adr/) | [ENGINEERING.md](ENGINEERING.md) |
| Product decisions (records) | [product/pdr/](product/pdr/) | [PRODUCT.md](PRODUCT.md) |
| Technical debt | [engineering/TECH_DEBT.md](engineering/TECH_DEBT.md) | — |
| Legal and policy concerns | [LEGAL.md](LEGAL.md) | [legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md) |
| Legal considerations register | [legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md) | [LEGAL.md](LEGAL.md) |
| Legal decisions (durable record) | [legal/ldr/](legal/ldr/) | [LEGAL.md](LEGAL.md) |
| Roadmap planning workflow | [.ai/workflows/ROADMAP-PLANNER.md](../.ai/workflows/ROADMAP-PLANNER.md) | [ROADMAP.md](ROADMAP.md) |
| Roadmap apply workflow | [.ai/workflows/ROADMAP-EDITOR.md](../.ai/workflows/ROADMAP-EDITOR.md) | [ROADMAP.md](ROADMAP.md) |
| Planning event prompts | [.ai/prompts/planning/](../.ai/prompts/planning/) | [ROADMAP.md](ROADMAP.md) |

<!-- BOOTSTRAP:TOPIC_ENTRIES_EXTENSIONS:START -->
> 📝 Extended during BS-06 (Docs map) and grows as the project encounters topics worth indexing.
>
> Additional "By topic" rows are added here for project-specific topics: domain entities, product features, ADRs, runbooks, etc. The pattern is the same: short topic name, primary doc, secondary doc (if any).
<!-- BOOTSTRAP:TOPIC_ENTRIES_EXTENSIONS:END -->

---

## Future documents (not yet created)

Created as needed, not speculatively. Common candidates:

- `engineering/DATA_MODEL.md` — entities, relationships, aggregates. Write when modeling real entities in code.
- `engineering/API_CONVENTIONS.md` — REST patterns: versioning, error format, pagination, idempotency. Write on first endpoint.
- `engineering/AI_INTEGRATION.md` — prompting strategy, model choice per task, tool calling, cost tracking. Write on first real LLM integration.
- `engineering/OBSERVABILITY.md` — instrumentation specifics, dashboards, alerts. Write when instrumenting for production.
- `engineering/DEPLOYMENT.md` — containers, CI/CD, environments, rollback. Write near first deploy.
- `engineering/RELEASE.md` — release process, versioning in practice, changelog. Write on first release.
- `engineering/runbooks/INCIDENT_RESPONSE.md` — incident procedure. Required before production.

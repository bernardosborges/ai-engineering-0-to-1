# Architecture

Structural view of the system: components, flows, boundaries, cross-cutting decisions. Detailed schemas, endpoints, code conventions, and deploy topology live in dedicated docs.

---

## 1. What this project is

<!-- BOOTSTRAP:WHAT_THE_PROJECT_IS:START -->
> 📝 Filled by bootstrap prompt `BS-01-product-discovery.md` after the project domain is understood.
>
> This section will contain:
> - A one-paragraph description of what the system does.
> - The core asset (the most valuable data or capability the system creates).
> - A table of actors with their role, access channels, and the phase in which they enter the system.
> - Any v1 constraints that scope down the ambition (e.g., single-tenant, one channel, free tier only).
<!-- BOOTSTRAP:WHAT_THE_PROJECT_IS:END -->

---

## 2. High-level component view

<!-- BOOTSTRAP:COMPONENT_VIEW:START -->
> 📝 Filled by bootstrap prompt `BS-03-architecture.md` after the architecture is decided.
>
> This section will contain:
> - A Mermaid diagram showing the major components and how they connect (entry points, services, queues, workers, databases, external integrations).
> - A "Component responsibilities" subsection listing each component, its location in the code, and what it owns.
<!-- BOOTSTRAP:COMPONENT_VIEW:END -->

---

## 3. Main flows

<!-- BOOTSTRAP:MAIN_FLOWS:START -->
> 📝 Filled by bootstrap prompt `BS-02-product-document.md` and refined during `BS-03-architecture.md`.
>
> This section will document the critical user and system flows step by step. Examples of flows worth documenting:
> - User onboarding
> - Core user action (the main thing the product does)
> - Async workflows triggered by user actions
> - Recurring jobs or background processes
>
> Each flow uses a numbered list, identifies which components participate, and calls out where decisions are made.
<!-- BOOTSTRAP:MAIN_FLOWS:END -->

---

## 4. Internal layers

The system follows **explicit layering with one-directional dependencies**. Inner layers know nothing about outer layers. Business logic does not depend on frameworks, databases, or external services.

The dependency rule is enforced automatically in CI by a layering tool (e.g., `import-linter` for Python, `dependency-cruiser` for TypeScript). Layer violations break the build.

**Layers, from inside out:**

- **`domain/`** — entities, value objects, enums, exceptions, repository ports, domain services. No frameworks, no I/O. The business rules live here.
- **`application/`** — use cases coordinating one or more domain operations behind a transaction boundary. Service-client ports (LLM, storage, messaging, etc.) consumed by use cases live here.
- **`infrastructure/`** — concrete adapters: database repositories, external service clients, message queue workers, template renderers. Everything that talks to the outside world lives here.
- **`api/`** — HTTP routers and entry points. Request/response schemas, dependency injection wiring. The web layer.

### Port placement: domain vs application

Ports (interface contracts) live in two locations depending on what they abstract:

- **`domain/ports/`** — contracts for domain aggregates and domain-owned concerns. Examples: entity repositories, domain event publishers. These ports name business concepts; their methods describe business operations.
- **`application/ports/`** — contracts for service-clients and infrastructure plumbing that the application layer talks to. Examples: LLM clients, messaging clients, storage clients, idempotency stores. These ports name technical capabilities; their methods describe technical operations.

When in doubt, ask: does this port name a domain aggregate? If yes → `domain/ports/`. If no → `application/ports/`.

Concrete adapters for both groups live under `infrastructure/`. The dependency rule is unchanged: domain may not import from application, neither imports from infrastructure.

### Repository pattern

Repository interfaces live in `domain/ports/`. Concrete implementations live in `infrastructure/db/repositories/`. Repositories are CRUD-only: they expose persistence operations on a single aggregate and never orchestrate across aggregates. They never own the transaction boundary.

A repository receives the active database session through its constructor and uses it for both reads and writes, which keeps pending writes visible to subsequent reads within the same operation.

### Domain services for orchestration

`domain/services/` contains services that orchestrate logic across multiple aggregates or coordinate steps that don't belong to a single entity. A service injects the repository ports it needs through its constructor and composes them into a single business operation.

The service does not commit. It performs orchestration on the same shared session that its repositories were constructed with, so all writes participate in whatever transaction the caller opened.

### Unit of Work and transaction boundary

The **caller** owns transaction begin and commit. Typically that caller is a use case in `application/usecases/`. Whoever opens the session is responsible for committing or rolling back.

Repositories and domain services receive that session through dependency injection and operate within it. The transaction stays explicit at the boundary where atomicity makes sense (the use case), while the lower layers remain simple and testable.

<!--
PYTHON_REFERENCE_EXAMPLE — Internal layers

How this materializes in a Python project using FastAPI and SQLAlchemy.

- Folder layout under `app/`:
    - `app/domain/` — entities, value objects, enums, exceptions, repository ports, domain services.
    - `app/application/` — use cases (one class per file, `VerbNounUseCase` pattern), DTOs, service-client ports (LLM, geocoding, messaging, storage, idempotency).
    - `app/infrastructure/` — SQLAlchemy repositories, Anthropic LLM adapter, Telegram adapter, BrasilAPI adapter, S3 adapter.
    - `app/api/` — FastAPI routers, request/response schemas, dependency wiring.
    - `app/core/` — settings (Pydantic), ID helpers, observability config.

- Dependency rule enforced by `import-linter` config in CI:
    - `app.domain` may not import from `app.application`, `app.infrastructure`, or `app.api`.
    - `app.application` may not import from `app.infrastructure` or `app.api`.
    - `app.infrastructure` and `app.api` may import from everywhere below.

- Port placement examples:
    - Domain ports (in `app/domain/ports/`): `BuyerRepository`, `QuotationRepository`, `ServiceAreaRepository`, `DomainEventPublisher`.
    - Application ports (in `app/application/ports/`): `LLMClient`, `MessagingClient`, `GeocodingClient`, `ObjectStorage`, `EmbeddingClient`, `IdempotencyStore`.

- Repository pattern in practice:
    - Interfaces are `Protocol` classes in `app/domain/ports/`.
    - Implementations are concrete classes in `app/infrastructure/db/repositories/`.
    - Methods: `add(entity)`, `update(entity)`, `get_by_id(id)`, `find_by_<key>(...)`. No unified `save()`.
    - Soft-delete-aware repositories filter `WHERE deleted_at IS NULL` by default. `_including_deleted` variants only when an audit/restore caller needs them.

- Domain service example:
    - `app/domain/services/service_area_provisioning_service.py` — given a municipality, find-or-create the matching `ServiceArea`, find-or-create the corresponding state `Region`, and link both — three aggregates touched in one call, all CRUD-only repositories under the hood.
    - Receives repositories via constructor as keyword-only arguments.

- Unit of Work in practice:
    - Use cases open the session via FastAPI `Depends` in `app/api/`.
    - The session is passed down through the repository and service constructors.
    - `await session.commit()` and `await session.rollback()` only in the use case or its UoW helper, never inside repositories.

- FastAPI `Depends`:
    - Used only inside `app/api/`. Never in `application` or `domain`. Enforced by `import-linter`.
-->

---

## 5. Cross-cutting decisions

### Authentication and authorization

Authentication identifies who the actor is. Authorization decides whether that actor may perform the operation.

- Authentication happens at the API gateway layer or middleware. Tokens validated before the request reaches the use case.
- Authorization happens at the use case layer — never only in the router. Frontend or client-side restrictions are UX, not security.
- Public endpoints (no auth) are explicitly listed and justified. Default is private.

### Observability

The system emits structured telemetry from day one. The minimum stack:

- **Structured logs** with consistent event names (`domain.action` pattern in snake_case).
- **Distributed tracing** with OpenTelemetry spans across service boundaries.
- **Metrics** for the core operations (request count, latency, error rate, cost where applicable).

Every LLM call emits a span with at least: model, input tokens, output tokens, cost, latency, purpose. Cost tracked per business operation for unit economics.

### Error handling

Domain errors are raised at the domain/application boundary. They are translated to user-facing language at the messaging or HTTP boundary. Stack traces never reach end users in production.

Each domain exception class has a docstring explaining when it is raised and carries enough context for the caller to react programmatically (e.g., `field` and `reason` attributes for validation errors).

### Idempotency

External-facing entry points (webhooks, public APIs) are idempotent by a stable identifier (message ID, request ID, command ID). Duplicate delivery is a no-op. Idempotency state lives in a fast store (e.g., Redis) with a bounded TTL.

### Rate limiting

Two distinct categories:

- **Inbound rate limiting**: applied at the gateway, scoped per user/IP, with separate caps for sensitive endpoints (auth, LLM-backed).
- **Outbound rate limiting**: applied per external integration, scoped per partner where applicable (e.g., per-supplier daily cap, per-API-key call rate). Critical for systems that fan out to many third parties.

Proactive background jobs respect the outbound limit — they must not starve real user-triggered traffic.

### Development environment

Reproducibility is a first-class architectural commitment. The system must run identically on every contributor's machine and in CI, with no hidden dependencies on what's installed locally.

The project commits to one of two postures:

- **Native dev + containerized infra (default)**. The application runs natively via the package manager (e.g., `uv run`, `npm run dev`). Infrastructure dependencies (database, cache, queue, observability collector) run via Docker Compose. The Dockerfile exists for production builds and CI, but is not the primary dev loop. Trade-off: faster hot-reload and easier debugging vs accepting that the dev's OS/toolchain may differ from production.

- **Fully containerized dev**. The application itself runs inside a container in dev, via `docker compose up app` or an equivalent. Live-reload via volume mount. The Dockerfile is the single source of truth for the runtime environment. Optionally extended with a Dev Container config (`.devcontainer/devcontainer.json`) so VSCode/Cursor and Codespaces can open the project with zero local installs beyond Docker and git. Trade-off: stronger 'works on my machine' guarantees vs slower iteration and more setup friction.

The choice is made explicitly during BS-03 and propagates to BS-04 (Dockerfile is in phase 1 vs phase 2; Dev Container config is in phase 1 if chosen) and BS-07 (TSK ordering reflects when the container build comes online). Regardless of posture, **lockfile commits, runtime version pins, and container base image pins are mandatory** — they are the minimum reproducibility floor that both postures share.

What is NOT acceptable under either posture: relying on globally-installed tools that aren't declared in the project manifest; assuming a specific OS for dev; instructions of the form "first install X with brew/apt" without a containerized alternative.

<!--
PYTHON_REFERENCE_EXAMPLE — Cross-cutting decisions

How this materializes in the author's Python stack.

- Authentication:
    - JWT RS256 (asymmetric). HS256 forbidden.
    - Access token 15 min, refresh 30 days, rotated on every use.
    - Telegram webhook authenticity verified via Telegram signature in `app/api/webhooks/telegram.py`.
    - Mini App auth: Telegram `initData` signed by bot secret, exchanged for short-lived JWT in HttpOnly cookie.

- Authorization:
    - Check in the use case constructor or `execute()` method, with explicit ownership verification.
    - Return 404 (not 403) when the user is not allowed to know the resource exists. Return 403 only when existence is already public.

- Observability:
    - OpenTelemetry + structlog (see ADR 0007 in real project).
    - Every LLM call emits a span with `llm.model`, `llm.input_tokens`, `llm.output_tokens`, `llm.cost_usd`, `llm.latency_ms`, `llm.purpose`.
    - Logger per module: `logger = structlog.get_logger(__name__)`.
    - Event names as `"domain.action"`: `"quotation.created"`, `"whatsapp.send_failed"`.

- Error handling:
    - Domain exceptions in `app/domain/exceptions/`, one file per aggregate.
    - Translated to user-facing pt-BR at the Telegram messaging boundary or HTTP boundary.
    - Global FastAPI exception handler in `app/api/exceptions/` catches unhandled errors, returns trace ID, never leaks stack traces in production.

- Idempotency:
    - Webhook idempotency by Telegram message ID, stored in Redis with 24h TTL.
    - `IdempotencyStore` port in `app/application/ports/`, Redis adapter in infrastructure.

- Rate limiting:
    - Redis sliding window at the gateway.
    - Auth endpoints: 5 / 15 min per IP + 5 / 15 min per account.
    - LLM endpoints: 20 / min, 500 / day per user.
    - Outbound to suppliers: per-supplier daily cap configurable, enforced before enqueuing outbound messages.
    - Proactive price refresh respects supplier outbound limit.

- Development environment:
    - Posture: Native dev + containerized infra.
    - Dev runs `uv run uvicorn app.main:app --reload` directly on the developer machine.
    - Postgres + pgvector, Redis, OTel collector via `docker compose up -d` (compose file at repo root).
    - Dockerfile exists for production builds (multi-stage, Python 3.13-slim-bookworm base). Used by CI for image build/push, not by devs for daily work.
    - No Dev Container config in v1; reconsidered post-MVP if contributor onboarding friction surfaces.
    - Minimum reproducibility floor enforced: `uv.lock` committed, `.python-version` pinned to 3.13.x, Dockerfile base image pinned to a specific digest.
-->

---

## 6. Data — architectural view

The system distinguishes between **source-of-truth stores** and **derived/ephemeral stores**.

| Store category | Role | Source of truth? |
|---|---|---|
| Primary database (relational or document) | Business entities, auth, durable state | ✅ |
| Vector / search index | Semantic and keyword search | ❌ (rebuildable from primary) |
| Cache / queue | Idempotency, rate limit counters, async jobs, sessions | ❌ (ephemeral) |
| Object storage | Generated files, user uploads, static assets | ✅ for binaries |

The principle: **anything rebuildable does not own data**. Vector indexes, full-text indexes, and caches can be wiped and regenerated from the primary store. The primary store and binary object storage are the only places where business truth lives.

<!-- BOOTSTRAP:DATA_VIEW:START -->
> 📝 Filled by bootstrap prompt `BS-03-architecture.md` after the data architecture is decided.
>
> This section will contain a table listing the actual stores in use (e.g., PostgreSQL, pgvector, Redis, S3), what each stores, and which is the source of truth for each kind of data.
>
> If a single store has multiple roles (e.g., PostgreSQL holding both OLTP and pgvector), note that — the source-of-truth distinction is logical, not physical.
<!-- BOOTSTRAP:DATA_VIEW:END -->

---

## 7. Integrations

External integrations are isolated behind ports defined in `application/ports/` and implemented by adapters in `infrastructure/`. Replacing a provider is replacing one adapter.

<!-- BOOTSTRAP:INTEGRATIONS:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after tools and integrations are chosen.
>
> This section will contain a table of each external integration: purpose, phase in which it enters, and the port/adapter pair that isolates it.
>
> Format suggestion:
>
> | Integration | Purpose | Phase | Isolation |
> |---|---|---|---|
> | LLM provider | All LLM calls | v1 | `LLMClient` (port) + `<Provider>LLMAdapter` (adapter) |
> | Object storage | File storage | v1 | `ObjectStorage` (port) + `<Provider>StorageAdapter` (adapter) |
<!-- BOOTSTRAP:INTEGRATIONS:END -->

---

## 8. Quality attributes and trade-offs

Quality attributes are explicit commitments about what the system optimizes for. They guide trade-off decisions throughout the codebase.

<!-- BOOTSTRAP:QUALITY_ATTRIBUTES:START -->
> 📝 Filled by bootstrap prompt `BS-03-architecture.md` after architectural decisions are made.
>
> This section will contain:
> - **Latency targets** — p95 budget per operation (webhook ack, page load, end-to-end user-facing flows, internal queries).
> - **Scale targets** — expected volume of users, requests, data, for a defined horizon (e.g., 6 or 12 months).
> - **Cost discipline** — any cost metric tracked as a first-class concern (e.g., LLM cost per operation, infrastructure cost ceiling).
> - **Accepted trade-offs** — explicit list of decisions where alternatives were considered and rejected, with one-line rationale (e.g., "monolith over microservices to reduce ops complexity", "single primary database over polyglot persistence to keep operations simple").
<!-- BOOTSTRAP:QUALITY_ATTRIBUTES:END -->

---

## 9. What this document explicitly does not cover

This document is the structural view. Implementation-level detail lives in dedicated docs:

- Schema design and column-level model → future `DATA_MODEL.md`
- Endpoint details, request/response schemas → future `API_CONVENTIONS.md`
- Prompt strategy, model selection per task → future `AI_INTEGRATION.md`
- Deploy topology, CI/CD, secrets in runtime → future `DEPLOYMENT.md`
- Observability instrumentation specifics → future `OBSERVABILITY.md`
- Privacy and compliance mapping → future `compliance/` docs
- Incident runbooks → future `runbooks/`

Documents are created as needed, not speculatively.

---

## 10. Open questions

<!-- BOOTSTRAP:OPEN_QUESTIONS:START -->
> 📝 Populated and updated throughout the project life.
>
> This section tracks unresolved structural questions that need answers before production or that warrant a future ADR. Format suggestion:
>
> - **<Topic>** — short description of the trade-off, options under consideration.
>
> Resolved questions become ADRs and are removed from this list. Keeping unresolved questions visible prevents silent drift.
<!-- BOOTSTRAP:OPEN_QUESTIONS:END -->


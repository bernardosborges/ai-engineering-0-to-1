# Tech Stack

Canonical source for all technology choices and versions. When adding a dependency, update this file first.

This document is the human-readable source of truth. Machine-readable sources (`pyproject.toml`, `package.json`, lockfiles, etc.) must agree with it — if they disagree, fix the doc in the same PR.

---

## How to read this doc

Dependencies are grouped by function (runtime, framework, data layer, observability, etc.). Each group is a table with three columns:

- **Item** — the name of the package or tool.
- **Version** — the declared version or range.
- **Purpose** — one line explaining why this dependency is in the project.

Every dependency in this project has a purpose. If a dependency cannot be justified in one line, it should not be in the project.

---

## Runtime

<!-- BOOTSTRAP:RUNTIME:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after the runtime stack is chosen.
>
> This section will list the language runtime, package manager, and lockfile. Typical format:
>
> | Item | Version | Notes |
> |---|---|---|
> | Language | x.y.z | Minimum version; features the team relies on. |
> | Package manager | name | Commands used; what is forbidden (e.g., "No pip, no poetry"). |
> | Lockfile | filename | "Committed. CI fails if drift." |
<!-- BOOTSTRAP:RUNTIME:END -->

---

## Dependency organization

<!-- BOOTSTRAP:DEPENDENCY_ORGANIZATION:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after the package management approach is chosen.
>
> This section will explain how dependencies are grouped (main vs dev vs test vs CI), which groups ship to production, and the commands to install each.
>
> Decide explicitly: are there separate groups for dev, test, and CI? Most projects can collapse them into a single dev group. Add a CI-only group only when a tool is exclusive to CI (e.g., coverage uploader).
<!-- BOOTSTRAP:DEPENDENCY_ORGANIZATION:END -->

---

## Production dependencies

Dependencies that ship to the production image. Organized by function.

<!-- BOOTSTRAP:PRODUCTION_DEPENDENCIES:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after the production stack is chosen.
>
> This section will list production dependencies grouped by function. Typical groupings:
>
> - **Core framework** — HTTP framework, validation, config
> - **Data layer** — ORM/query builder, database driver, migrations, cache
> - **Messaging / channels** — external messaging clients
> - **AI / LLM** — LLM SDK, token counters, embedding clients
> - **Background jobs** — task queue, scheduler
> - **Object storage** — S3, GCS, or equivalent clients
> - **HTTP & resilience** — HTTP client, retry, circuit breaker
> - **Observability** — logging, tracing, metrics
> - **Auth & security** — JWT, password hashing, crypto
> - **Identifiers** — UUID, ULID, or other ID generation
>
> Use one table per group. Skip groups that don't apply.
<!-- BOOTSTRAP:PRODUCTION_DEPENDENCIES:END -->

---

## Development dependencies

Dependencies used during development and CI but not shipped to production.

<!-- BOOTSTRAP:DEVELOPMENT_DEPENDENCIES:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after the dev tooling is chosen.
>
> This section will list dev dependencies grouped by function. Typical groupings:
>
> - **Testing** — test runner, async test support, coverage, containers for integration tests, HTTP mocking
> - **Code quality** — linter, formatter, type checker, architecture rule enforcer
> - **Dev tooling** — pre-commit, local infra (docker-compose), debugger tools
>
> Use one table per group.
<!-- BOOTSTRAP:DEVELOPMENT_DEPENDENCIES:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Full stack

The author's reference Python stack for an AI-first backend. Use as reference
for the level of detail, grouping, and the format of the "Explicitly NOT used"
section. Adapt to the user's chosen stack — the categories carry over, the
specific packages do not.

### Runtime

| Item | Version | Notes |
|---|---|---|
| Python | 3.13.x | Minimum 3.13. Use `match` statements and PEP 695 generics freely. |
| Package manager | uv | `uv sync`, `uv add`, `uv run`. No pip, no poetry. |
| Lockfile | `uv.lock` | Committed. CI fails if drift. |

### Dependency organization

PEP 735 dependency groups in `pyproject.toml`. No `requirements-*.txt` files.

| Group | Location | Purpose | Goes to prod image? |
|---|---|---|---|
| (main) | `[project].dependencies` | Runtime deps | ✅ |
| `dev` | `[dependency-groups].dev` | Local dev + CI base | ❌ |

CI runs `uv sync --group dev`. Production image runs `uv sync --no-dev`.

### Core framework (prod)

| Item | Version | Purpose |
|---|---|---|
| FastAPI | ^0.128 | HTTP API |
| Uvicorn | ^0.32 | ASGI server (dev + prod) |
| Pydantic | ^2.9 | Validation, serialization |
| pydantic-settings | ^2.6 | Env + `.env` config with type validation |

### Data layer (prod)

| Item | Version | Purpose |
|---|---|---|
| SQLAlchemy | ^2.0 | ORM, async only (`AsyncSession`) |
| Alembic | ^1.13 | Migrations |
| asyncpg | ^0.30 | Postgres driver |
| pgvector | ^0.3 | Python bindings for pgvector extension |
| Postgres | 16 | Primary DB + FTS + pgvector |
| Redis | 7 | Queue, cache, idempotency, rate limit |
| redis | ^5.0 | Async Redis client |

### Identifiers (prod)

| Item | Version | Purpose |
|---|---|---|
| uuid-utils | ^0.9 | UUIDv7 generation (Rust-backed) |

All entity IDs are UUIDv7. On Python 3.14, this dep is replaced by stdlib `uuid.uuid7()`.

### AI / LLM (prod)

| Item | Version | Purpose |
|---|---|---|
| anthropic | ^0.97 | Official SDK for Claude |
| tiktoken | ^0.8 | Token counting for budgeting |

All LLM access goes through `app/application/ports/llm_client.py`. The SDK is imported only in `app/infrastructure/llm/`.

### Embedding (prod)

| Item | Version | Purpose |
|---|---|---|
| voyageai | ^0.3 | Voyage AI client (voyage-3 model) |

### Background jobs (prod)

| Item | Version | Purpose |
|---|---|---|
| arq | ^0.26 | Async Redis-based task queue |

### Object storage (prod)

| Item | Version | Purpose |
|---|---|---|
| aioboto3 | ^15.5 | Async AWS SDK (S3 client) |

### HTTP & resilience (prod)

| Item | Version | Purpose |
|---|---|---|
| httpx | ^0.27 | Async HTTP client |
| tenacity | ^9.0 | Retry with backoff |
| purgatory | ^3.0 | Circuit breaker |

### Observability (prod)

| Item | Version | Purpose |
|---|---|---|
| structlog | ^24.4 | Structured JSON logs |
| opentelemetry-api | ^1.27 | Tracing + metrics API |
| opentelemetry-sdk | ^1.27 | SDK |
| opentelemetry-exporter-otlp | ^1.27 | OTLP exporter |
| opentelemetry-instrumentation-fastapi | ^0.48 | Auto-instrumentation |
| opentelemetry-instrumentation-sqlalchemy | ^0.48 | DB span capture |
| opentelemetry-instrumentation-httpx | ^0.48 | Outbound HTTP spans |

### Auth & security (prod)

| Item | Version | Purpose |
|---|---|---|
| pyjwt[crypto] | ^2.9 | JWT signing/verification (RS256) |
| argon2-cffi | ^23.1 | Password hashing (Argon2id) |

### Dev — Testing

| Item | Version | Purpose |
|---|---|---|
| pytest | ^8.3 | Test runner |
| pytest-asyncio | ^0.24 | Async tests |
| pytest-cov | ^6.0 | Coverage |
| testcontainers | ^4.8 | Postgres + Redis in integration tests |
| respx | ^0.21 | Mock httpx calls |
| dirty-equals | ^0.8 | Flexible assertions |
| polyfactory | ^2.18 | Factories for domain objects |
| hypothesis | ^6.115 | Property-based testing |

### Dev — Code quality

| Item | Version | Purpose |
|---|---|---|
| ruff | ^0.7 | Lint + format (replaces black, isort, flake8, most of pylint) |
| mypy | ^1.13 | Type checking, strict mode |
| import-linter | ^2.1 | Enforces architecture dependency rules |

### Dev — Tooling

| Item | Version | Purpose |
|---|---|---|
| pre-commit | ^4.0 | Git hooks for ruff + mypy |

### Explicitly NOT used (Python-specific)

| Rejected | Reason | Use instead |
|---|---|---|
| `requirements.txt`, `requirements-dev.txt` | Legacy; no single lockfile; drift between files | PEP 735 dependency groups |
| Flask, Django, Litestar | Standardized on FastAPI | FastAPI |
| Pydantic v1 | EOL, incompatible API | Pydantic v2 |
| `python-dotenv` (direct) | `load_dotenv()` mutates global state; no validation | `pydantic-settings` |
| `passlib` | Effectively unmaintained; breaks on Python 3.13 | `argon2-cffi` |
| `bcrypt` | No memory-hardness; 72-byte input limit; OWASP prefers Argon2id | `argon2-cffi` (Argon2id) |
| `python-jose` | No releases since 2021, open security issues | `pyjwt[crypto]` |
| `pylint` | Slow, redundant with ruff | `ruff` |
| `coverage` (explicit) | Pulled in transitively by `pytest-cov` | `pytest-cov` |
| SQLAlchemy sync / Django ORM | Async-only codebase | SQLAlchemy 2 async |
| Celery | Heavy, sync-first | arq |
| `requests`, `aiohttp` | Standardized on httpx | httpx |
| `prometheus_client` (direct) | Couples app to Prometheus; OTel is vendor-neutral | OpenTelemetry + OTLP |
| poetry, pipenv, pip-tools | Slower, more friction | uv |
| black + isort + flake8 | Replaced by ruff | ruff |
| LangChain, LlamaIndex | Too much abstraction, churn, hides token usage | Direct SDK + thin internal helpers |
| Qdrant, Weaviate, Pinecone | Extra service to operate at current scale | pgvector |
| unittest | Inconsistent with rest of codebase | pytest |
| UUIDv4 as primary key | B-tree index fragmentation on random inserts | UUIDv7 |
-->

---

## Explicitly NOT used

Decisions already made about technologies the project does **not** use. Anything in this list cannot be re-introduced without opening an ADR. The "NOT used" list is as important as the "used" list — it prevents drift and re-litigation.

<!-- BOOTSTRAP:EXPLICITLY_NOT_USED:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after stack decisions are made, and grows over time as the project rejects alternatives.
>
> Format: `| Rejected | Reason | Use instead |` table.
>
> Entries to seed with: legacy patterns (e.g., old config files), competing frameworks in the same category, libraries with security issues, libraries with poor maintenance, libraries that hide critical concerns (e.g., abstraction layers over LLM SDKs that hide token usage).
<!-- BOOTSTRAP:EXPLICITLY_NOT_USED:END -->

---

## Version bump policy

The project's version bump policy is the same regardless of stack:

- **Patch** (`x.y.Z`): automatic via Renovate/Dependabot, merged on green CI.
- **Minor** (`x.Y.z`): bot opens PR, reviewed by any maintainer.
- **Major** (`X.y.z`): requires ADR in `docs/engineering/adr/`. No direct bumps, no exceptions.

Security patches: auto-merge on green CI, regardless of version bump, per `@docs/engineering/SECURITY.md`.

---

## Runtime version upgrade

<!-- BOOTSTRAP:RUNTIME_UPGRADE_POLICY:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after the runtime is pinned.
>
> This section will state the runtime version pinned, the policy for when the next upgrade is allowed, and the ADR requirements for the upgrade.
>
> Typical content:
>
> Pinned to <language> <version> until at least <date>. Upgrade requires ADR covering:
> - Container base image change
> - CI matrix change
> - Dependency compatibility check
> - Performance benchmark delta
<!-- BOOTSTRAP:RUNTIME_UPGRADE_POLICY:END -->

---

## Where versions actually live

This document is the human-readable source of truth. Machine-readable sources:

<!-- BOOTSTRAP:VERSION_SOURCES:START -->
> 📝 Filled by bootstrap prompt `BS-04-tools.md` after the stack is chosen.
>
> This section lists the files that actually hold version information (package manifest, lockfile, runtime pin, container base image). Format suggestion:
>
> - `<manifest file>` — declared ranges + dependency groups
> - `<lockfile>` — resolved versions
> - `<runtime pin>` — runtime version
> - `<container image>` — container base image
>
> If these disagree with this doc, fix the doc in the same PR.
<!-- BOOTSTRAP:VERSION_SOURCES:END -->

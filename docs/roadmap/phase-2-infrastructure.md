# Phase 2 — Application infrastructure

**Goal:** Web framework running with a health check, primary database connected, migration tool managing schema, structured logging, observability wired, integration tests against real infrastructure. No business features yet.

**Milestone:** start the application; the health endpoint returns OK; migrations can be generated and applied; traces and logs are visible.

> **Note on this phase file.** This is a starting point seeded by the template. It assumes a typical backend setup: HTTP framework, async database, migrations, structured logging, OpenTelemetry, containerized local infra, integration tests via real containers. Adjust the specific tasks to match the project's stack chosen in `BS-04-tools.md` of the bootstrap. The shape and ordering reflect general best practices for this kind of system; the tools themselves are the project's choice.

---

## TSK-013 — Minimal application skeleton

**What:** Create the application entry point with the chosen framework. Implement a single endpoint that returns `{"status": "ok"}`. Wire a startup/shutdown lifecycle hook (still empty but wired). Running the application serves the health endpoint locally.

**Depends on:** Phase 1 complete.

**Acceptance:** the application starts; the health endpoint responds.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-013 in Python

Create `app/main.py` with a FastAPI instance, `GET /health` returning
`{"status": "ok"}`, and a startup/shutdown lifecycle hook (still empty but
wired). `uv run uvicorn app.main:app --reload` serves it.

Acceptance: `uvicorn` starts; `curl http://localhost:8000/health` returns 200.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-014 — Typed configuration

**What:** Create the single typed entry point for configuration (`app/core/config.py` or equivalent). Cover at minimum: `app_name`, `environment` (dev/staging/prod), `log_level`. Load from `.env` file. Create `.env.example` template. The application reads via the typed entry point — never `os.environ` directly.

**Depends on:** TSK-013.

**Acceptance:** the application reads configuration via the typed entry point; `.env.example` exists; `.env` is git-ignored.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-014 in Python

Create `app/core/config.py` with a `Settings` class via `pydantic-settings`
covering: `app_name`, `environment`, `log_level`. Load from `.env`. Create
`.env.example`. `main.py` reads via `settings`.

Acceptance: `Settings` validates env vars at import; missing required vars
fail fast with a clear error.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-015 — Local infrastructure via containers

**What:** Container compose file with the primary database and any always-on dependencies (cache, queue). Volume mounts for data persistence in dev. Document the bring-up command in `README.md`.

**Depends on:** TSK-014.

**Acceptance:** running the compose command brings up dependencies; the application can connect to them locally.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-015 in Python

`docker-compose.yml` with Postgres 16 + pgvector extension and Redis 7.
`docker-compose.override.yml` for dev (exposed ports, volume for data
persistence). Document `docker compose up -d` in README.

Acceptance: `docker compose up -d` brings up Postgres and Redis; psql can
connect; Redis CLI can ping.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-016 — Database session and dependency injection

**What:** Async engine and session factory. Configuration read from settings. DI provider yielding sessions to handlers and use cases. Smoke test connecting to the primary database.

**Depends on:** TSK-015.

**Acceptance:** the application opens a session against the running database container; smoke query returns expected result.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-016 in Python

Create `app/infrastructure/db/session.py` with async engine and
`async_sessionmaker`. Configuration read from settings. Create a DI provider
in `app/api/deps/db.py` yielding sessions. Smoke test connecting to Postgres.

Acceptance: a FastAPI endpoint using the DI session can SELECT 1 from
Postgres via async session.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-017 — Migration tool initialization

**What:** Initialize the migration tool chosen in `@docs/engineering/CODE_STYLE.md`. Configure it to use async (if applicable) and read configuration from settings. Set the file naming template to include timestamps for readable history.

**Depends on:** TSK-016.

**Acceptance:** the migration tool can generate empty migration files; running upgrade on a fresh database produces an empty schema.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-017 in Python

`alembic init --template async alembic`. Adjust `alembic/env.py` for async +
reading from `settings`. Configure `alembic.ini` with file template
`%%(year)d_%%(month).2d_%%(day).2d_%%(hour).2d%%(minute).2d-%%(rev)s_%%(slug)s`
for naming conventions.

Acceptance: `uv run alembic revision -m "empty"` generates a migration file
with a timestamp-prefixed name; `uv run alembic upgrade head` succeeds.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-018 — ID generation helper

**What:** Single shared helper for generating IDs, per the strategy decided in `@docs/engineering/CODE_STYLE.md`. Add the ID-generation dependency to runtime deps if needed. Unit tests covering the desired properties (uniqueness, time-ordering if applicable, format).

**Depends on:** TSK-017.

**Acceptance:** the helper generates IDs of the correct type and shape; properties asserted in unit tests.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-018 in Python

Create `app/core/ids.py` with a `new_id() -> uuid.UUID` function using
`uuid-utils` (per ADR 0001). Add `uuid-utils` to runtime deps. Unit test
covering time-ordered properties of generated IDs.

Acceptance: 1000 sequential calls produce monotonically increasing IDs by
millisecond; `uuid.UUID.version` returns 7 for each.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-019 — First database model and real migration

**What:** Create the first ORM/database model — a minimal entity for migration testing (e.g., `User` or any small entity that makes sense in the domain). Generate a real migration. Review the generated file against the autogenerate pitfalls in `@docs/engineering/CODE_STYLE.md`. Apply the migration to the local database.

**Depends on:** TSK-018.

**Acceptance:** the table exists in the database; rollback (downgrade) also works.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-019 in Python

Create `app/infrastructure/db/models/user.py` with a minimal `UserModel`
(UUIDv7 id, email, created_at). Generate a migration via `alembic revision
--autogenerate -m "add user table"`. Review the generated file against the
autogenerate pitfalls in `docs/engineering/CODE_STYLE.md`. `alembic upgrade head` creates
the table.

Acceptance: `\dt` in psql shows the table; `alembic downgrade -1` drops it
cleanly.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-020 — Structured logging

**What:** Initialize the structured logger chosen in `@docs/engineering/CODE_STYLE.md`. Configure: structured output in production (JSON), human-readable in dev (key-value), correct log level from settings. First structured log emitted from the health endpoint.

**Depends on:** TSK-013.

**Acceptance:** the application emits structured logs to stdout; in dev, logs are human-readable; in prod mode, logs are valid JSON.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-020 in Python

Create `app/core/logging.py` configuring structlog (JSON output in prod,
key-value in dev). Initialize in `main.py` lifecycle. First structured log
from the health endpoint.

Acceptance: `GET /health` emits a log event `"health.checked"` with structured
fields; format switches based on `settings.environment`.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-021 — Observability (tracing and metrics)

**What:** Initialize OpenTelemetry (or the equivalent tracing/metrics SDK chosen). Configure an exporter pointing to a local collector. Add the collector service to the local container compose file. Auto-instrument the HTTP framework, the database client, and the HTTP client used for outbound calls. First trace visible in the collector.

**Depends on:** TSK-020.

**Acceptance:** issuing a request produces a trace visible in the collector; spans include framework, DB, and outbound HTTP automatically.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-021 in Python

Create `app/core/telemetry.py` initializing the OTel SDK with OTLP exporter
pointing to a collector. Add collector service to `docker-compose.yml`.
Auto-instrumentation for FastAPI, SQLAlchemy, and httpx. First trace visible.

Acceptance: `GET /health` produces a span; the span includes the SQLAlchemy
child span if the endpoint queries the DB.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-022 — Integration test infrastructure

**What:** Configure the test infrastructure with **real infrastructure containers** per the principles in `@docs/engineering/TESTING.md`. Session-scoped fixtures bringing up the primary database (and any other always-on dependencies) via testcontainers (or equivalent). First integration test against a real database.

**Depends on:** TSK-016, TSK-019.

**Acceptance:** integration tests start their own containers, run, and tear down cleanly. No shared local installation required.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-022 in Python

`tests/conftest.py` with session-scoped fixtures spinning up Postgres (with
pgvector) and Redis via `testcontainers`. First integration test
(`tests/integration/test_db_connection.py`) verifying SQLAlchemy can query.

Acceptance: `uv run pytest -m integration` brings up containers; query
against real Postgres returns expected result; teardown succeeds.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## Phase 2 retrospective

<!-- Fill when Phase 2 is complete. -->

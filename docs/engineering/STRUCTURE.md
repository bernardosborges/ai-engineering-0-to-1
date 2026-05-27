# Structure

Folder and file layout of the repository. The structure reflects the architectural layers defined in `@docs/engineering/ARCHITECTURE.md`. Treat any deviation as either a bug or a deliberate decision worth documenting.

---

## Principles

These principles guide the folder layout regardless of language or framework:

- **Layers map to folders.** Each architectural layer (api, application, domain, infrastructure) gets its own top-level folder inside the application source. Imports across layers obey the dependency rule, enforced in CI.
- **Tests mirror application structure.** A test for `app/<path>/<file>.py` lives in `tests/<scope>/<path>/test_<file>.py`. Easier to find, easier to keep in sync.
- **One public class per file.** File name matches the main public class. Helpers used only by that class may live in the same file.
- **No dumping ground folders.** Never `utils/`, `helpers/`, `common/`, `misc/`. Each folder has a clear responsibility named after what it owns.
- **Configuration in the root.** Build files (`pyproject.toml`, `package.json`, etc.), lockfiles, linter configs, formatter configs, and editor configs live at the repo root. Hidden configs (`.gitignore`, `.editorconfig`, etc.) too.
- **Operational concerns separated from application code.** Deployment, infrastructure-as-code, scripts, dashboards, and runbooks live in dedicated top-level folders outside the application source.
- **Documentation parallel to code.** `docs/` is a top-level folder. Documentation does not live inside the application source.

---

## Repository layout

The repository follows a three-zone organization at the top level:

- **Application code** — everything that runs as the product (typically inside an `app/` or `src/` folder).
- **Project documentation** — `docs/` and `.ai/`.
- **Operational artifacts** — deployment configs, scripts, CI definitions, dev environment setup.

<!-- BOOTSTRAP:REPOSITORY_LAYOUT:START -->
> 📝 Filled by bootstrap prompt `BS-03-architecture.md` after the architecture and folder structure are decided.
>
> This section will contain the concrete folder tree of the repository, with one-line comments next to each folder explaining what lives there. The tree mirrors the chosen architecture (layered, hexagonal, modular monolith, etc.) and the chosen stack's conventions.
>
> Use a code block with `text` or `bash` syntax highlighting. Keep comments aligned for readability.
<!-- BOOTSTRAP:REPOSITORY_LAYOUT:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Repository layout

The author's reference layout for a Python project using Clean Architecture
with FastAPI, SQLAlchemy async, Alembic, and structlog. Use as reference for
the level of granularity and the style of inline comments expected.

```
project-name/
├── .ai/
│   └── ...                               # See dedicated section
│
├── .github/
│   └── workflows/                        # CI/CD pipelines (GitHub Actions)
│
├── alembic/                              # Database migrations (Alembic)
│   ├── env.py                            # Alembic environment config (async SQLAlchemy)
│   └── versions/                         # Auto-generated migration scripts
│
├── app/                                  # Application code
│   ├── api/                              # HTTP layer — entry point for all requests
│   │   ├── deps/                         # Injectable dependencies (db session, current user, use cases)
│   │   ├── exceptions/                   # HTTP exception handlers and error mapping
│   │   ├── middleware/                   # Correlation ID, request logging, rate limit
│   │   ├── routers/                      # Endpoints organized by domain entity
│   │   ├── webhooks/                     # Inbound webhook handlers per channel
│   │   └── schemas/                      # Pydantic request/response models
│   │
│   ├── application/                      # Use case orchestration — no framework dependencies
│   │   ├── dtos/                         # Data Transfer Objects between layers
│   │   ├── ports/                        # Service-client interfaces (LLM, geocoding, storage, messaging)
│   │   ├── exceptions/                   # Application-level exceptions
│   │   ├── helpers/                      # Reusable logic shared across use cases (not business rules)
│   │   ├── unit_of_work/                 # Transaction control interface (abstract)
│   │   └── usecases/                     # One file per use case (VerbNounUseCase pattern)
│   │
│   ├── core/                             # Cross-cutting setup — no business logic, no I/O concrete impls
│   │   ├── config.py                     # All env vars via Pydantic Settings — single source of truth
│   │   ├── logging.py                    # Structlog setup
│   │   └── telemetry.py                  # OpenTelemetry SDK initialization
│   │
│   ├── domain/                           # Business rules — zero external dependencies
│   │   ├── constants/                    # Business constants (deadlines, limits, defaults)
│   │   ├── entities/                     # Pure domain entities (no ORM, no Pydantic)
│   │   ├── enums/                        # Domain enumerations
│   │   ├── events/                       # Domain events (optional)
│   │   ├── exceptions/                   # Domain exceptions, one file per aggregate
│   │   ├── factories/                    # Complex entity creation logic
│   │   ├── ports/                        # Repository interfaces and domain-owned contracts
│   │   ├── services/                     # Domain services for orchestration across aggregates
│   │   └── value_objects/                # Immutable types
│   │
│   ├── infrastructure/                   # Concrete implementations of ports
│   │   ├── cache/                        # Redis cache implementations
│   │   ├── db/                           # Database access (SQLAlchemy async)
│   │   │   ├── mappers/                  # Entity <-> ORM model conversion
│   │   │   ├── models/                   # SQLAlchemy ORM models
│   │   │   ├── repositories/             # Concrete repository implementations
│   │   │   ├── session.py                # Async engine and session factory
│   │   │   └── unit_of_work/             # Concrete UoW using AsyncSession
│   │   ├── embeddings/                   # Embedding provider adapter
│   │   ├── llm/                          # LLM adapter, prompt builder, tool registry
│   │   ├── observability/                # Custom spans, business metrics, LLM cost tracking
│   │   ├── security/                     # JWT encoding/decoding, password hashing
│   │   ├── services/                     # Third-party integrations
│   │   ├── storage/                      # Object storage adapters (S3)
│   │   ├── jobs/                         # Job queue adapter + task definitions
│   │   └── vector_store/                 # Vector search and indexing operations
│   │
│   ├── prompts/                          # Runtime prompts loaded by the application
│   │
│   ├── workers/                          # Background worker entrypoints
│   │
│   └── main.py                           # FastAPI instance, lifecycle, middleware registration
│
├── deployment/                           # All deploy-related artifacts
│   ├── ansible/                          # Configuration management
│   ├── docker/                           # Container images
│   │   ├── Dockerfile                    # Multi-stage image
│   │   └── Dockerfile.dev                # Dev-only image (if it diverges from main)
│   ├── observability/                    # Grafana dashboards, Prometheus rules
│   └── terraform/                        # Infrastructure provisioning
│
├── docs/                                 # Project documentation
│
├── scripts/                              # One-off operational scripts (seed, backfill, data migration)
│
├── tests/                                # Mirrors app/ structure
│   ├── e2e/                              # Full stack, HTTP-level
│   ├── fixtures/                         # Static test data
│   ├── integration/                      # Real DB/Redis via testcontainers
│   ├── unit/                             # Fast, isolated (domain + application with fakes)
│   └── conftest.py                       # Shared fixtures
│
├── .dockerignore                         # Excludes from Docker build context
├── .env.example                          # Template for local .env (no secrets)
├── .gitignore                            # Git exclusions
├── .pre-commit-config.yaml               # Git hooks (ruff, mypy)
├── .python-version                       # Python version pin for uv
├── CLAUDE.md                             # Project rules and entry point for Claude Code
├── README.md                             # Human onboarding
├── docker-compose.override.yml           # Auto-loaded dev overrides
├── docker-compose.yml                    # Base services for local dev
├── pyproject.toml                        # Dependencies + tool configs
└── uv.lock                               # Resolved versions — committed, never hand-edited
```

Notes on this layout:

- The `app/` folder holds the four architectural layers as siblings: `api/`, `application/`, `domain/`, `infrastructure/`. Plus `core/` for cross-cutting setup, `prompts/` for runtime LLM prompts, `workers/` for background entrypoints, and `main.py` as the FastAPI bootstrap.
- The `tests/` folder mirrors `app/` structure under each scope folder (`unit/`, `integration/`, `e2e/`). For example, a test for `app/domain/entities/user.py` lives at `tests/unit/domain/entities/test_user.py`.
- Alembic lives at the repo root (not inside `app/`) because migrations are operational artifacts, not application code. Same logic for `scripts/`.
- The `deployment/` folder is separated from `app/` to avoid mixing concerns. Infra-as-code, container images, dashboards, and provisioning live there.
-->

---

## The `.ai/` folder

The `.ai/` folder is the operating system of the AI-assisted method used in this project. It exists because LLMs are part of the workflow — remove AI from the team and the folder becomes obsolete. The structure is the same in every project that adopts this method, so it does not depend on the chosen stack.

```
.ai/
├── INDEX.md                              # Router to every workflow and prompt
├── workflows/                            # Operating manuals, one per LLM role
│   ├── BOOTSTRAPPER.md                   # Bootstrap operating manual
│   ├── TASK-PLANNER.md                   # Per-task planning conversation
│   ├── ROADMAP-PLANNER.md                # Roadmap structural-change planning
│   ├── ROADMAP-EDITOR.md                 # Applies a planning prompt to the roadmap
│   ├── ADR-PROPAGATOR.md                 # Propagates an accepted ADR across docs
│   ├── PDR-PROPAGATOR.md                 # Propagates an accepted PDR across docs
│   ├── LDR-PROPAGATOR.md                 # Propagates an accepted LDR across docs
│   ├── DOCS-VALIDATOR.md                 # Documentation integrity workflow
│   └── bootstrap/                        # Step prompts read by BOOTSTRAPPER (BS-01..BS-07)
│       ├── BS-01-product-discovery.md
│       ├── BS-02-product-document.md
│       ├── BS-03-architecture.md
│       ├── BS-04-tools.md
│       └── BS-05-conventions.md          # BS-06, BS-07 added when those steps are written
└── prompts/
    ├── TEMPLATE.md                       # Template every execution/planning prompt fills
    ├── execution/                        # Archived task prompts (one subfolder per phase)
    └── planning/                         # Archived planning prompts (replannings, roadmap changes)
```


See `@.ai/workflows/TASK-PLANNER.md` for what the Planner does and `@.ai/prompts/TEMPLATE.md` for the structure of task prompts.

---

## The `docs/` folder

The `docs/` folder is project documentation that exists independently of the AI workflow. Both humans and LLMs consult it.

```
docs/
├── INDEX.md                              # Router to every other doc
├── PRODUCT.md                            # Product overview and document map
├── ENGINEERING.md                        # Engineering overview (architecture, conventions, ADRs)
├── LEGAL.md                              # Legal overview (policy register, LDRs)
├── ROADMAP.md                            # Build plan, phases, and learnings
├── engineering/                          # Engineering policy docs
│   ├── ARCHITECTURE.md                   # System structure, components, flows
│   ├── STRUCTURE.md                      # This file — repository layout
│   ├── TECH_STACK.md                     # Tools and their justifications
│   ├── CODE_STYLE.md                     # Coding conventions (incl. DB & migrations)
│   ├── TESTING.md                        # Test strategy
│   ├── SECURITY.md                       # Security principles
│   ├── GIT.md                            # Branching, commits, PR conventions
│   ├── LANGUAGE.md                       # Language policy
│   ├── TECH_DEBT.md                      # Deferred technical decisions
│   └── adr/                              # Architecture Decision Records (+ TEMPLATE.md)
├── product/                              # Detailed product specifications
│   ├── TEMPLATE.md                       # Structure for pd-*/ft-* docs
│   └── pdr/                              # Product Decision Records (+ TEMPLATE.md)
├── legal/                                # Legal and policy considerations
│   ├── POLICY_CONSIDERATIONS.md          # Pre-legal register (LP-NN entries)
│   └── ldr/                              # Legal Decision Records (+ TEMPLATE.md)
└── roadmap/                              # Per-phase task files
    ├── phase-1-foundation.md
    └── phase-2-infrastructure.md
```


See `@docs/INDEX.md` for how to navigate the docs by task.

---

## Naming conventions for folders and files

- **Folders**: lowercase, words separated by hyphens or underscores per the language's convention (`use_cases/` in Python, `use-cases/` in TypeScript, etc.).
- **Files**: lowercase, matching the language's convention. Python: `snake_case.py`. TypeScript: `kebab-case.ts` or `PascalCase.ts` depending on the file's main export.
- **File name matches the main public class** or function inside. A file named `user_repository.py` exports `UserRepository`. A file named `create_user_use_case.py` exports `CreateUserUseCase`.

Detailed naming rules per language live in `@docs/engineering/CODE_STYLE.md`.

---

## When the structure must change

Refactoring the folder structure is expensive — it touches many imports, can break tools that hardcode paths (linters, CI configs), and creates noise in git history.

Refactor the structure only when:

- A new architectural pattern is being introduced (e.g., adding a `domain/events/` folder when introducing event-driven flows).
- An existing folder no longer reflects what's inside (rename, not reshuffle).
- The current structure causes recurring confusion (people repeatedly putting files in the wrong place).

Avoid speculative restructuring. Folders are created as the code that lives in them appears, not in anticipation.

When a restructuring is needed, it goes through a planning prompt under `.ai/prompts/planning/` and is referenced from the Planning events section of `@docs/ROADMAP.md`. The PR that implements it touches only the structure — no functional changes in the same commit.

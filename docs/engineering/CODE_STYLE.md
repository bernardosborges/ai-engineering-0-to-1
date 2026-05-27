# Code Style

Rules for writing code in this project. These conventions exist so that code looks like one person wrote it, even when many contributors and LLMs are involved.

The principles below are universal. The Python reference examples (hidden in HTML comments throughout this document) capture how these principles materialize in the author's reference stack. During bootstrap, sections marked with BOOTSTRAP get filled with the chosen stack's idiomatic equivalents.

---

## Foundations

### Formatting

Formatting is delegated to an automatic formatter. The formatter's config lives in the project's root config file (e.g., `pyproject.toml`, `package.json`, `.prettierrc`). Never hand-format code that the formatter would reformat.

The formatter runs:

- On save (editor integration)
- As a pre-commit hook
- In CI as a check (blocks merge on disagreement)

### Type checking

Type checking runs in **strict mode** wherever the language supports it. Loose typing is a temporary debt, not a permanent state.

Type checking runs in two channels:

- **Pre-commit hook** — on changed files only. Fast feedback before each commit.
- **CI** — on the full project. Authoritative check.

Manual full-project validation: available via a single command.

### Type annotations

- All functions have explicit type annotations on every parameter and on the return value, including for procedures that return nothing.
- Use the language's modern type system features (built-in generics, union syntax, pattern matching) rather than legacy aliases.
- Type-checker escape hatches (e.g., `Any`, `unknown`, `as`) require an inline justification comment.

<!--
PYTHON_REFERENCE_EXAMPLE — Foundations

### Formatting

Delegated to ruff. Config in `pyproject.toml` under `[tool.ruff]`.

### Type checking: mypy

Mypy in strict mode via:

- Pre-commit hook — runs on changed files only (`pass_filenames: true`). Fast.
- CI — runs on the full project (`uv run mypy app/`). Authoritative.

Manual: `uv run mypy app/` for full-project validation locally.

### Type hints

- All functions have type hints on parameters and return, including `-> None`.
- Built-in generics: `list[str]`, `dict[str, int]`, `tuple[int, ...]` — not `List`, `Dict`, `Tuple`.
- Union syntax: `str | None` — not `Optional[str]`.
- `Any` requires an inline comment justifying why.
- Use `typing.TYPE_CHECKING` to break import cycles.
- Prefer PEP 695 generics (Python 3.12+): `class Repository[T]:` — not legacy `TypeVar`.

```python
# Good
def find_by_id(self, order_id: uuid.UUID) -> Order | None:
    ...

# Bad
def find_by_id(self, order_id) -> Optional[Order]:
    ...
```
-->

### Imports

- Absolute imports only. Never relative imports.
- Order enforced by the formatter (or its companion sorter): standard library → third-party → first-party.
- Never wildcard imports.
- Never import inside functions unless breaking a justified circular dependency.

<!--
PYTHON_REFERENCE_EXAMPLE — Imports

- Absolute imports only. Never `from ..foo import bar`.
- Order via ruff (isort rules): stdlib → third-party → first-party.
- Never `from x import *`.
- Use `__all__` in `__init__.py` only when exposing a public API.

```python
# Standard library
import uuid
from datetime import UTC, datetime

# Third-party
from fastapi import APIRouter
from sqlalchemy.ext.asyncio import AsyncSession

# First-party (absolute only)
from app.application.dtos.order_dto import OrderCreateDTO
from app.domain.entities.order import Order
```
-->

### File organization

Order inside a module:

1. Module docstring
2. Imports
3. Constants
4. Types / interfaces / protocols
5. Classes
6. Functions
7. Module-level entry (rare)

**One public class per file.** Small private helper classes may live in the same file as the public class that uses them.

---

## Naming

Naming carries semantic weight. Names communicate intent and category — the reader should infer what kind of thing a name refers to without reading its implementation.

### Universal rules

- **Files**: lowercase, words separated according to the language's convention. File name matches the main public class or function inside.
- **Never dumping-ground names**: no `utils`, `helpers`, `common`, `misc`. Name by purpose.
- **Booleans**: prefixed with semantic markers (`is_`, `has_`, `can_`, `should_`).
- **Collections**: plural (`users`, `orders`, `items`).
- **No abbreviations**: `order`, not `ord`. `customer`, not `cust`. `request`, not `req`. Even if the name gets long.
- **Constants**: uppercase with underscores at module level.
- **Loop variables**: meaningful names unless trivially short (`for order in orders`, not `for o in orders`).

### Class naming by category

The project distinguishes class categories by name pattern. Each category has a distinct suffix or shape so that the reader immediately knows what kind of class they're looking at.

<!-- BOOTSTRAP:CLASS_NAMING_TABLE:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will contain a table mapping each class category to its naming pattern. Categories typically include: domain entity, value object, use case, port (interface), adapter (implementation), DTO, HTTP schema, domain service, mapper, ORM model, domain event.
<!-- BOOTSTRAP:CLASS_NAMING_TABLE:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Class naming

| Type | Pattern | Example |
|---|---|---|
| Entity | `PascalCase` (noun) | `Order`, `Customer` |
| Value Object | `PascalCase` (noun) | `Email`, `Money`, `Address` |
| Use case | `VerbNounUseCase` | `CreateOrderUseCase` |
| Port / interface | `NounRepository`, `NounPort` | `OrderRepository`, `LLMPort` |
| Service-client port | `NounClient` | `LLMClient`, `EmailClient` |
| Adapter | `TechNounRepository` | `PostgresOrderRepository`, `AnthropicLLMAdapter` |
| DTO | `NounActionDTO` | `OrderCreateDTO` |
| HTTP Schema | `NounActionRequest` / `NounActionResponse` | `OrderCreateRequest` |
| Domain Service | `NounService` | `PricingService` |
| Mapper | `NounMapper` | `OrderMapper` |
| ORM Model | `NounModel` | `OrderModel` |
| Domain Event | `NounPastParticiple` | `OrderPlaced`, `OrderShipped` |
-->

### Functions and methods

- Verb-noun structure: `calculate_total_price`, `send_order_confirmation`.
- Async functions follow the same naming as sync ones — no `async_` prefix, no `_async` suffix.
- Private methods are prefixed with a leading underscore (per the language's convention).

---

## Structures

### Functions and methods

- Keep functions under ~50 lines. If longer, extract.
- Never use mutable default arguments. Use a null sentinel and assign inside.
- Use keyword-only arguments when a function takes more than 3 parameters.
- Always explicit return type, including for procedures that return nothing.
- Prefer early returns (guard clauses) over deep nesting.

<!--
PYTHON_REFERENCE_EXAMPLE — Functions

```python
# Good
def discount_for(order: Order, *, coupon: Coupon | None = None) -> Money:
    if order.is_empty():
        return Money.zero()
    if coupon is None:
        return Money.zero()
    ...

# Bad
def discount_for(order, coupon=None):
    if not order.is_empty():
        if coupon is not None:
            ...
```
-->

### Classes — choosing the right construct

Different categories of objects use different language constructs. The choice signals intent: immutable vs mutable, validated at construction vs at boundary, persistence-only vs business logic.

<!-- BOOTSTRAP:CLASS_CONSTRUCT_CHOICE:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will map each kind of object to the construct used to express it. Decisions to make:
>
> - What construct represents an immutable value object?
> - What construct represents a domain entity with identity?
> - What construct represents a DTO crossing boundaries?
> - What construct represents an HTTP schema?
> - What construct represents an ORM model?
> - When is a regular class (with custom constructor) justified?
>
> The principle is: the construct should reflect the role, and roles should not be mixed in one class.
<!-- BOOTSTRAP:CLASS_CONSTRUCT_CHOICE:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Class construct choice

| Use | When |
|---|---|
| `@dataclass(frozen=True, slots=True)` | Value objects, simple immutable data |
| `@dataclass(slots=True)` | Domain entities with identity and mutable state |
| Pydantic `BaseModel` | HTTP schemas, DTOs crossing boundaries, settings |
| SQLAlchemy `DeclarativeBase` | ORM models only (infra layer) |
| Regular class | Custom `__init__`, inheritance, or complex behavior |

Rules:
- Avoid inheritance beyond one level. Prefer composition.
- Keep `__init__` thin — no I/O, no heavy logic. Complex creation goes to factory methods or `domain/factories/`.
- Use `@property` sparingly, for cheap derived values only.
- Never put business logic on ORM models. They are persistence-only.
-->

### Value objects

Value objects are immutable, validated on construction, raise domain exceptions on invalid input.

The chosen construct enforces immutability at the language level wherever possible. Validation happens in the constructor — once constructed, a value object is always valid.

<!--
PYTHON_REFERENCE_EXAMPLE — Value objects

```python
@dataclass(frozen=True, slots=True)
class Email:
    value: str

    def __post_init__(self) -> None:
        if not self._is_valid(self.value):
            raise InvalidEmailError(f"Invalid email: {self.value}")

    @staticmethod
    def _is_valid(value: str) -> bool:
        ...
```
-->

### Domain exceptions

- **Location**: dedicated folder under the domain layer (e.g., `domain/exceptions/`). One file per aggregate or grouped concept. Exceptions never live inline in entity, value-object, service, or repository files.
- **Class names** end in a consistent suffix (`Error`, `Exception`) per the language's convention.
- **Each exception has a docstring** explaining when it is raised.
- **Domain code never raises language built-ins** (`ValueError`, `TypeError`, `RuntimeError`). Anything raised inside the domain layer is a domain exception.
- **Field validation errors** carry structured context (e.g., `field` and `reason` attributes) so callers can react to individual fields without parsing the message.
- **Business-logic violations** use granular exceptions per condition, named for the condition itself, carrying whatever context the caller needs to branch on.

<!--
PYTHON_REFERENCE_EXAMPLE — Domain exceptions

- Location: `app/domain/exceptions/`. One file per aggregate.
- Class names end in `Error`.
- Each exception has a docstring.
- Re-export from `app/domain/exceptions/__init__.py` using explicit-re-export style: `from x import Y as Y` (PEP 484).
- Domain code never raises Python built-ins.

Field validation pattern:

```python
class OrderValidationError(Exception):
    """Raised when an Order is constructed or transitioned with invalid field values."""

    def __init__(self, field: str, reason: str) -> None:
        self.field = field
        self.reason = reason
        super().__init__(f"Invalid {field}: {reason}")
```

Business-logic violations: granular exceptions per condition (`InvalidOrderTransitionError`, `MissingShippingAddressError`), carrying context (`from_status`/`to_status`, `customer_id`).
-->

### Domain services

Domain services orchestrate logic across multiple aggregates or coordinate steps that don't belong to a single entity.

- **Location**: dedicated folder under the domain layer (e.g., `domain/services/`). One file per service.
- **Class name** follows a `NounService` pattern.
- **Dependencies are injected via constructor**, typed as the abstract port (never a concrete repository). Use keyword-only arguments for clarity.
- **The service does not commit.** It operates on the shared session/transaction that its injected dependencies were built with. The caller (typically a use case) owns transaction begin/commit.
- **Used for orchestration** across aggregates. If logic belongs to a single entity, it stays a method on that entity.

### Repository pattern

Repositories use **explicit verbs** for write operations. There is **no unified `save()`** that decides between insert and update.

- `add(entity)` — first persistence. Raises on duplicate primary key or unique-constraint violation.
- `update(entity)` — modification of an existing entity. Raises if the entity does not exist.
- `get_by_id(id)` — lookup by primary key. Returns the entity or null per the port's contract.
- `get_by_<key>(...)` / `find_by_<key>(...)` — lookup by another unique field.

Rationale:

1. **Intent clarity** — `add` and `update` express distinct domain operations.
2. **Immutability enforcement** — immutable entities expose `add`, never `update`. Enforced at the type level.
3. **Error semantics** — duplicate insertions fail loudly. An upsert-style `save` silently masks bugs.
4. **Test granularity** — each operation has its own dedicated tests.

### Enums

- **Location**: dedicated folder under the domain layer (e.g., `domain/enums/`).
- **Always use the language's serializable enum type** (e.g., `StrEnum` in Python 3.11+) for values that cross API or database boundaries.
- **Explicit values** — never auto-generated values for enums that cross boundaries.

<!--
PYTHON_REFERENCE_EXAMPLE — Enums

```python
from enum import StrEnum

class OrderStatus(StrEnum):
    DRAFT = "draft"
    PLACED = "placed"
    SHIPPED = "shipped"
    DELIVERED = "delivered"
    CANCELLED = "cancelled"
```

Use `StrEnum` for serializable enum values, never `(str, Enum)`.
-->

### IDs

<!-- BOOTSTRAP:ID_STRATEGY:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will define:
>
> - What ID type the project uses (UUIDv7, UUIDv4, ULID, KSUID, NanoID, autoincrement integer, etc.)
> - Where IDs are generated (application layer? domain layer? database?)
> - How IDs are typed in entities, DTOs, and APIs
> - How IDs are serialized in responses
> - Whether there is a shared helper that all entities use to generate IDs
>
> Recommendation: pick a time-sortable, opaque ID type. Generate at the domain/application layer through a single shared helper so future migrations are a one-file change.
<!-- BOOTSTRAP:ID_STRATEGY:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — IDs

- All entities use **UUIDv7** as primary key — never auto-increment integers, never UUIDv4.
- Generated at the domain/application layer, never by the database.
- Typed as `uuid.UUID` in entities and DTOs. APIs treat all UUIDs uniformly regardless of version.
- Serialized as `string` in API responses.

```python
import uuid
from dataclasses import dataclass, field
import uuid_utils

def _new_id() -> uuid.UUID:
    return uuid.UUID(str(uuid_utils.uuid7()))

@dataclass(slots=True)
class Order:
    id: uuid.UUID = field(default_factory=_new_id)
```

Convention: every model that generates IDs uses a single `_new_id` helper (or imports from a shared `app/core/ids.py`) — never calls `uuid_utils.uuid7()` inline. This keeps the Python 3.14 migration to a single-file change.
-->

---

## Idioms

### Control flow

- Prefer early returns and guard clauses over deep nesting.
- Never use `else` after `return` or `raise`.
- Use pattern matching (when the language supports it) for dispatch on shape or type — not chained type checks.
- Never use exceptions for regular control flow.

### Strings

- Use the language's modern string interpolation by default (f-strings, template literals).
- Never legacy formatting (`%` formatting, `str.format()`, string concatenation with `+`) — except in logging, where the structured logger takes context as keyword arguments.
- Multi-line strings: use the language's dedent helper or indented heredocs intentionally.
- Secrets never go into interpolated strings that reach logs or error messages.

### Null and empty values

- Null represents absence. Never use empty string, zero, or `-1` as sentinels for "not set".
- Optional fields: explicit nullable type with a default of null.
- Collections are never null — return empty collection (`[]`, `{}`, `set()`, `()`) instead.

<!--
PYTHON_REFERENCE_EXAMPLE — Null and empty values

```python
# Good
class CustomerCreateRequest(BaseModel):
    email: str | None = None
    tags: list[str] = []

# Bad
class CustomerCreateRequest(BaseModel):
    email: str = ""
    tags: list[str] | None = None
```
-->

### Dates and times

- Always timezone-aware. Never naive timestamps.
- Use UTC for storage and computation. Convert to local time only at the API boundary.
- Serialize as ISO 8601.
- Use the language's stdlib timezone support (e.g., `zoneinfo` in Python, native `Date` with TZ awareness in TypeScript).

<!--
PYTHON_REFERENCE_EXAMPLE — Dates and times

- Use `datetime.now(UTC)` — never `datetime.now()` or `datetime.utcnow()` (deprecated since 3.12).
- Use `zoneinfo.ZoneInfo` (stdlib) for non-UTC zones.

```python
from datetime import UTC, datetime
from dataclasses import dataclass, field

@dataclass(slots=True)
class Order:
    created_at: datetime = field(default_factory=lambda: datetime.now(UTC))
```
-->

### Paths and files

- Always use the language's path abstraction (e.g., `pathlib.Path` in Python, `path` module in Node). Never string concatenation for paths.
- Absolute paths in config, not hardcoded in runtime code.
- Use the language's packaged-resources mechanism for files distributed with the application (e.g., `importlib.resources` in Python).

### Numbers

- Underscores in large literals where the language allows: `1_000_000`, not `1000000`.
- Explicit decimal type for money — never floating point.
- Use the language's identity operator for null comparisons where applicable.
- Explicit boolean comparisons: `if user.is_active:`, not `if user.is_active == True`.

### Collections

- Comprehensions / map-filter idioms preferred over imperative loops when the operation is a pure transformation.
- Generator expressions / lazy sequences for large datasets.
- `dict.get(key, default)` (or equivalent) over try/catch when absence is expected.
- Destructuring / unpacking preferred over indexing.

### Async

- All I/O is async. Sync functions only for pure CPU-bound logic.
- Database operations are async.
- External service calls (HTTP, queue, LLM, email) are always async.
- Never mix sync entry points inside async code.
- Timeouts are mandatory on every external call.
- Fire-and-forget tasks must be stored and awaited at some point; never leave dangling coroutines.

<!--
PYTHON_REFERENCE_EXAMPLE — Async

- Route handlers, use cases, and all I/O functions are `async def`.
- Database via `AsyncSession`.
- Never mix `asyncio.run()` inside async code.
- Timeouts: `asyncio.timeout()` mandatory on external calls.
- Fire-and-forget: `asyncio.create_task()` and stored/awaited; never dangling coroutines.
-->

---

## Documentation

### Docstrings

- Required on: public modules, public classes, public functions with non-obvious behavior.
- Optional on: small, self-explanatory functions; private helpers.
- Never paraphrase the function name. A docstring that just restates the signature is noise — remove it.
- Use **one docstring style consistently across the project** (Google, NumPy, JSDoc, etc., per the language's convention). Pick one in conventions step; don't mix.

<!--
PYTHON_REFERENCE_EXAMPLE — Docstrings

Google style.

```python
def calculate_total_price(items: list[OrderItem], *, strategy: PricingStrategy) -> Money:
    """Compute the total price for the items under the given pricing strategy.

    Args:
        items: Line items in the order. Must be non-empty.
        strategy: Rules that affect totals (e.g., discount tiers, tax inclusion).

    Returns:
        The final total.

    Raises:
        EmptyOrderError: If the order has no items.
    """
```
-->

### Comments

- Explain **why**, not what. The code already says what.
- TODO comments include the author (or ticket reference) and context: `# TODO(author): context` — never a bare `# TODO`.
- Never commit commented-out code. Trust the version control history.

<!--
PYTHON_REFERENCE_EXAMPLE — Comments

```python
# Good
# Suppliers in BR use comma as decimal separator; normalize before parsing.
price = raw.replace(",", ".")

# Bad
# Replace comma with dot
price = raw.replace(",", ".")
```
-->

---

## Infrastructure idioms

### Logging

The project uses a structured logger from day one. Never use raw print statements. Never use the language's basic logger as the primary logging path.

- One logger per module, instantiated at module level.
- Event names use a consistent shape: `domain.action` in snake_case (or the equivalent convention for the language).
- Context passed as structured fields (keyword arguments or context maps), never interpolated into the message string.
- Never log: passwords, tokens, full credentials, PII beyond IDs, prompts sent to LLMs, LLM responses containing PII.

<!--
PYTHON_REFERENCE_EXAMPLE — Logging

Uses `structlog`. Never `print()`, never stdlib `logging` directly.

- Logger per module: `logger = structlog.get_logger(__name__)`.
- Event names as `"domain.action"` in snake_case with dots: `"order.created"`, `"email.send_failed"`.
- Pass context as keyword arguments, not f-strings.
- Never log: passwords, JWTs, full tokens, full document numbers, PII beyond IDs.

```python
import structlog

logger = structlog.get_logger(__name__)

# Good
logger.info("order.created", order_id=str(order.id), item_count=len(items))
logger.error("email.send_failed", customer_id=str(customer_id), error=str(exc))

# Bad
print(f"Order created: {order.id}")
logger.info(f"Token: {token}")
```
-->

### Environment and configuration

- Access all config through a **single typed entry point**. Never read environment variables ad-hoc throughout the codebase.
- All env vars are typed in the config module with validation.
- Never hardcode secrets, URLs, or environment-specific values.
- Secrets are typed as protected types that prevent accidental logging (e.g., `SecretStr` in Pydantic, equivalent in other libraries).

<!--
PYTHON_REFERENCE_EXAMPLE — Environment and configuration

```python
# Good
from app.core.config import settings

db_url = settings.database_url

# Bad
import os

db_url = os.environ["DATABASE_URL"]
```

- All env vars typed in `app/core/config.py` via Pydantic Settings.
- Secrets as `pydantic.SecretStr`.
-->

### Dependency injection

- **Constructor injection always.** No service locator, no globals, no module-level singletons.
- Framework-specific DI primitives (FastAPI `Depends`, Spring `@Autowired`, etc.) live **only at the framework's natural layer** (HTTP API). Never leak into application or domain layers.
- Shared I/O resources (DB session, HTTP client, cache client) are injected, never imported as module-level singletons.

---

## Database and migrations

These rules apply to any project with a relational database, regardless of the specific RDBMS or ORM.

### Queries and sessions

- All database access is async (when the language supports it).
- The session is injected via dependency injection, never instantiated inside use cases or domain code.
- One session per request or per worker job — never share across requests.
- Use the ORM/query builder's modern style (e.g., SQLAlchemy 2.x style in Python). Never legacy APIs that have been deprecated.
- **Never** interpolate user input into raw SQL. If dynamic SQL is unavoidable, use the ORM's expression language with bound parameters.
- Transactions are managed by the caller (typically a use case via Unit of Work pattern), not inside repositories.

### Migrations: foundations

All schema and data changes go through the migration tool. **Never hand-edit the database in any environment**, including dev. If the schema has drifted from migrations, the fix is in the migrations.

Each project picks one migration tool and one folder layout. Config and migrations live at the repo root (not inside the application source) because migrations are operational artifacts, not application code.

<!-- BOOTSTRAP:MIGRATION_TOOL:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will state:
>
> - The migration tool in use (Alembic, Flyway, Liquibase, Prisma Migrate, Drizzle, etc.)
> - Where the config and migrations folder live in the repo
> - The command to generate a new migration
> - The command to apply migrations
> - The naming convention for migration files
<!-- BOOTSTRAP:MIGRATION_TOOL:END -->

### Migration types

Migrations are one of two kinds, **never mixed in the same file**:

- **Schema migration**: DDL only. `CREATE`, `ALTER`, `DROP`, indexes, constraints, enums. Fast, transactional.
- **Data migration**: DML only. `UPDATE`, `INSERT`, backfills. May be slow, may need batching.

If a feature needs both (new column + backfill), create **two sequential migrations**. This keeps rollback scopes small and makes failures diagnosable.

### Reviewing autogenerated migrations

If the migration tool supports autogeneration (e.g., Alembic's `--autogenerate`, Prisma's `prisma migrate dev`), treat the output as a **starting point, never the final answer**.

Common autogenerate pitfalls — **the generated file must be reviewed against these every time**:

- **Renames** generated as `DROP + CREATE`, losing all data. Always rewrite as `ALTER TABLE ... RENAME COLUMN`.
- **Enum changes** with incomplete support; manual statements often needed.
- **Named constraints** with mismatched names; diff against models.
- **Check constraints** sometimes missed entirely.
- **Indexes with conditions or expressions** often missed.
- **Column type changes** generated as drop+add; rewrite with the correct `ALTER COLUMN TYPE` plus any conversion clause.

### Required in every migration

Every migration file must have:

- A descriptive message in English, imperative mood (e.g., `"add order table"`, not `"added_order"` or `"order changes"`).
- Both **upgrade** and **downgrade** implemented. Downgrade may explicitly mark itself irreversible with a reason, but the function must exist.
- A docstring at the top of the file describing what the migration does and why (especially for non-obvious changes).
- **No imports from the application source** — migrations must be self-contained. Schemas, enums, and constants are **duplicated** inside the migration file on purpose, to ensure the migration works even if application code evolves.

### Destructive operations

Operations that can lose data (`DROP TABLE`, `DROP COLUMN`, `TRUNCATE`, destructive `UPDATE`) require:

1. **Separate migration**, not mixed with other changes.
2. **Explicit approval in the PR** by a reviewer acknowledging the data loss.
3. **Backup evidence** mentioned in the PR description (what backup exists before deploy).
4. **Downgrade is typically impossible** for data loss; say so explicitly.

### Breaking changes: expand / contract

Never rename or remove a column in a single migration that the running app still references. Use the expand/contract pattern across **three deploys**:

1. **Expand** — add the new column / table. Old and new coexist. Deploy.
2. **Migrate reads and writes** — application code updated to use the new column. Deploy.
3. **Contract** — remove the old column. Deploy.

Same pattern for renames: add new column, copy data, switch code, drop old.

This allows zero-downtime deploys and safe rollback at every step.

### One migration per PR

A PR introduces **one migration**, usually. Multiple migrations in a single PR require justification in the PR description — typical legitimate cases:

- Schema + data migration for the same feature.
- Breaking changes using expand/contract that must ship together.

Unrelated migrations bundled together: refuse — split into separate PRs.

### Running migrations

- **Local dev**: run via the tool's standard command.
- **Tests**: the test container setup applies all migrations against a fresh database at session start. No manual DB setup.
- **CI**: migration tests run `upgrade` on a fresh DB to verify the migration is valid.
- **Staging and production**: migrations run **automatically as part of the deploy pipeline, before the app starts on the new version**. If migration fails, deploy is aborted.

### Data migration specifics

For migrations with `INSERT` / `UPDATE` operations:

- **Use raw SQL or the ORM's low-level expression language, not application-level models.** Application models evolve; the migration must run identically in 2 years as today.
- **Batch large updates.** `UPDATE ... WHERE id IN (SELECT ... LIMIT N)` in a loop, not `UPDATE` on the entire table.
- **Generated values come from the database** (e.g., `gen_random_uuid()`, `now() AT TIME ZONE 'UTC'`), not from application code. Application-generated values in DML are slow and block the DB.

### AI-assisted migration workflow

Migrations are frequently generated by an LLM coding assistant. The workflow:

1. **Describe the change in domain terms.** Prefer: *"Add an `Order` entity with fields X, Y, Z and a foreign key to `Customer`"*, not: *"Create migration"*.
2. The assistant generates the data model first, then runs the migration tool's autogenerate command.
3. **The assistant opens the generated migration file and reviews it against the Autogenerate pitfalls above.**
4. The assistant reports what it found and what it adjusted. If a rename was detected, it flags the `DROP+CREATE` risk and rewrites as `ALTER`.
5. Migration is committed in the same PR as the model change.
6. Human reviewer verifies the migration matches the intent — no silent destructive changes.

**The assistant never invents migration file names** — it uses the timestamp and revision identifier generated by the migration tool.

**The assistant never edits an already-applied migration**. Once merged to main, a migration is immutable. Corrections come as new migrations.

### Never

- ❌ Hand-editing a migration after it's been applied in any shared environment.
- ❌ Using raw psql / direct DB clients / GUI tools to alter schema outside migrations.
- ❌ Importing from application source inside a migration.
- ❌ Running application-language ID generators or timestamp functions inside data migrations.
- ❌ Combining schema DDL and data DML in the same migration file.
- ❌ Destructive changes without separate migration and explicit PR approval.
- ❌ Renames via autogenerate without rewriting as proper `RENAME` statements.
- ❌ Migrations that depend on application code (imports, functions, constants).

<!--
PYTHON_REFERENCE_EXAMPLE — Database and migrations (Python stack)

### Queries and sessions

- SQLAlchemy 2.x async (`AsyncSession`).
- Session injected via DI; never instantiated inside use cases or domain code.
- 2.0-style `select()` — never legacy `Query` API.
- Never `session.execute(text("..."))` with f-string interpolation. Use expression language with bound parameters.

### Migration tool

Alembic in async mode. Config at repo root: `alembic.ini`. Environment at `alembic/env.py`. Versions at `alembic/versions/`.

Filename: `YYYY_MM_DD_HHMM-<revision_id>_<slug>.py` (Alembic default template, configured in `alembic.ini` to include timestamp).

### Creating a migration

```bash
uv run alembic revision --autogenerate -m "add order table"
```

Produces a file like `alembic/versions/2026_04_21_1430-abc123_add_order_table.py`.

### Migration message rules

- English, imperative, lowercase.
- Examples: `"add order table"`, `"add index on price_history.supplier_id"`, `"backfill customer_tier default"`, `"drop legacy user_roles table"`, `"rename item.category_id to item.category_code"`.

### Required in every migration

- `upgrade()` and `downgrade()` both implemented. Downgrade may `raise NotImplementedError("irreversible: <reason>")`.
- Docstring at the top.
- **No imports from `app/`** — schemas, enums, and constants duplicated in the migration on purpose.

### Data migration specifics

- Use SQL, not ORM models. ORM models evolve; migration runs the same way in 2 years.
- Use `op.execute()` or SQLAlchemy Core (`table()`, `column()`).
- UUIDs: use Postgres-native (`gen_random_uuid()` for v4, `pg_uuidv7` extension for v7), not Python-generated UUIDs.
- Timestamps: `now() AT TIME ZONE 'UTC'`, not Python `datetime.now(UTC)`.

### Local commands

- `uv run alembic upgrade head` — apply migrations.
- `uv run alembic revision --autogenerate -m "..."` — generate a new migration.
- `uv run alembic downgrade -1` — revert one step (rare, dev only).
-->

# CLAUDE.md

Project rules for Claude Code and any other LLM working on this project. Read this file first on every session. It is a router and a set of universal rules — for any topic where this file points to another doc, consult that doc as the source of truth.

References prefixed with `@` should be loaded into context when that topic is relevant to the current task.

---

## What this project is

<!-- BOOTSTRAP:PROJECT_OVERVIEW:START -->
> 📝 Filled by bootstrap step `BS-01-product-discovery.md` after defining the project domain and purpose.
>
> This section will contain a 2-3 paragraph summary of what the project does, who uses it, and what makes it distinct. It should be readable by someone arriving at the codebase for the first time.
<!-- BOOTSTRAP:PROJECT_OVERVIEW:END -->

Full context: @docs/product/PRODUCT.md and @docs/engineering/ARCHITECTURE.md

---

## Core rules (apply always)

### Language

- One language for all code, comments, docstrings, commits, docs, logs, event names, API fields.
- A second language is used only for user-facing content (UI strings, user-visible errors) when the product serves non-English speakers.
- Domain-specific terms with regulatory or industry meaning stay in their original form even inside code.

<!--
PYTHON_REFERENCE_EXAMPLE — Language (CLAUDE.md level)

- English everywhere by default: code, comments, commits, logs, user-facing content.
- If a second user-facing language is introduced, it stays in a localization layer. Code never mixes.
- Domain terms keep their canonical name (e.g. `Invoice`, `PurchaseOrder`).

For deeper conventions on language and naming, see @docs/engineering/LANGUAGE.md.
-->

<!-- BOOTSTRAP:LANGUAGE_RULES:START -->
> 📝 Filled by bootstrap step `BS-05-conventions.md` during the conventions step.
>
> This section will record the project's specific language choices: which language is used where, and any domain terms that stay in another form.
<!-- BOOTSTRAP:LANGUAGE_RULES:END -->

Details: @docs/engineering/LANGUAGE.md

### Stack

<!-- BOOTSTRAP:STACK_RULES:START -->
> 📝 Filled by bootstrap step `BS-04-tools.md` after choosing the technology stack.
>
> This section will summarize the chosen languages, frameworks, libraries, package manager, and any non-negotiable tool decisions. Each item must be defensible — chosen for a reason, not by default.
<!-- BOOTSTRAP:STACK_RULES:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Stack (CLAUDE.md level)

- Python 3.13, FastAPI, Pydantic v2, SQLAlchemy 2 async, PostgreSQL 16 + pgvector, Redis 7.
- Package manager: uv. PEP 735 dependency groups in `pyproject.toml`. No `requirements*.txt`.
- IDs: UUIDv7 via `uuid-utils`. Hashing: Argon2id. JWT: PyJWT. Observability: OpenTelemetry + structlog.

For the full list and what is explicitly NOT used, see @docs/engineering/TECH_STACK.md.
-->

Full list and what is NOT used: @docs/engineering/TECH_STACK.md

### Architecture

- The application has explicit layers with one-directional dependencies. Inner layers do not know about outer layers.
- Business logic does not depend on frameworks, databases, or external services.
- External integrations live behind ports, implemented by adapters in the infrastructure layer.

<!--
PYTHON_REFERENCE_EXAMPLE — Architecture (CLAUDE.md level)

- Clean Architecture / hexagonal. Dependency rule: `domain ← application ← infrastructure / api`. Enforced by `import-linter`.
- Repository ports in `app/domain/ports/`. Service-client ports in `app/application/ports/`. Adapters in `app/infrastructure/`.
- Use cases in `app/application/usecases/`, one class per file, `VerbNounUseCase` pattern.
- FastAPI `Depends` only in `app/api/`. Never leaks into application or domain.

Full layering and folder rules: see @docs/engineering/STRUCTURE.md and @docs/engineering/ARCHITECTURE.md.
-->

Full layering and folder rules: @docs/engineering/STRUCTURE.md and @docs/engineering/ARCHITECTURE.md

### Code style

- Formatting delegated to an automatic formatter. Type checking runs in strict mode.
- All functions have explicit type annotations on parameters and return values.
- Wildcard imports banned. Imports are explicit and absolute.
- One public class per file. File name matches the main public class.
- Mutable default arguments banned. Early returns preferred over deep nesting.

<!--
PYTHON_REFERENCE_EXAMPLE — Code Style (CLAUDE.md level)

- Type hints on every function (parameters and return, including `-> None`).
- `str | None`, not `Optional[str]`. `list[str]`, not `List[str]`. Built-in `Any` requires inline justification.
- `datetime.now(UTC)` — never naive datetime, never `utcnow()`.
- `pathlib.Path` — never `os.path` with strings.
- `StrEnum` for enum values, never `(str, Enum)`.
- Value objects: `@dataclass(frozen=True, slots=True)`, validate in `__post_init__`.

Full rules: see @docs/engineering/CODE_STYLE.md.
-->

Full rules: @docs/engineering/CODE_STYLE.md

### Security

- Never commit secrets. All configuration accessed through a single typed entry point.
- Every external input passes through strict validation with size and length limits.
- Authorization happens at the use case layer, not just in the router.
- Database queries use parametrized queries only. String interpolation into SQL is banned.
- Never return raw database models from API endpoints.
- For LLM features: user input delimited in prompts, output validated against schema before triggering side effects.
- Never log credentials, tokens, personal documents, prompts, or LLM responses containing PII.

<!--
PYTHON_REFERENCE_EXAMPLE — Security (CLAUDE.md level)

- Access config via `settings` only (`app/core/config.py`), never `os.environ`.
- Secrets typed as `pydantic.SecretStr`.
- Every external input through Pydantic with size limits. No raw `dict` crosses the API boundary.
- Never return ORM models from endpoints. Explicit response schemas only.
- SQL: SQLAlchemy parametrized only. `text()` with f-string is banned.
- LLM: user input delimited in prompts, output validated against schema before triggering actions.

Full checklist: see @docs/engineering/SECURITY.md.
-->

Full checklist: @docs/engineering/SECURITY.md

### Testing

- New code has tests appropriate to its layer (unit, integration, e2e).
- Tests are deterministic. No flaky tests, no order dependencies.
- Tests verify behavior, not implementation. Mocks asserting on internal calls are anti-patterns.
- Use fakes for collaborators (in-memory implementations honoring the same contract). Reserve mocks for external systems where behavior cannot be reproduced.
- Factories for domain objects, fixtures for infrastructure. No mixing.
- Every new use case operating on a protected resource has an authorization test.

<!--
PYTHON_REFERENCE_EXAMPLE — Testing (CLAUDE.md level)

- New code has unit tests (domain + application).
- New adapter has integration tests.
- Every new use case has an authorization test (unauthorized → 403/404). Enforced by meta-test in CI.
- Unit tests use `FakeLLMAdapter` — never real LLM API except behind `@pytest.mark.llm_live`.
- Factories (polyfactory) for domain objects, fixtures for infrastructure.
- Testcontainers for Postgres/Redis. No local shared database.
- Coverage: domain+application ≥ 90%, infrastructure ≥ 70%.

Full rules: see @docs/engineering/TESTING.md.
-->

Full rules: @docs/engineering/TESTING.md

### Git

These git rules are baked into the method. The bootstrap workflow will surface them so you can adjust if needed, but most projects keep them as is.

- **Trunk-based development.** Branches off `main`, short-lived (1-3 days), squash merge, delete branch on merge.
- **Conventional Commits** in English, imperative, 72 chars max on first line. Standard types: `feat, fix, refactor, perf, docs, test, chore, build, ci, revert`.
- **Squash merge only.** Linear history on `main` — no merge commits.
- **Signed commits required.**
- **PR size cap: ~400 lines** of diff (excluding lockfiles and auto-generated files).
- **At least one approving review per PR** (or self-review by the maintainer in solo projects).
- **Rebase to update with main.** Never `git merge main` into a feature branch.
- **Curated commits, not captured.** No `git add .`.

<!-- BOOTSTRAP:GIT_RULES:START -->
> 📝 Filled by bootstrap step `BS-05-conventions.md` during the conventions step.
>
> If the project adopts non-default git conventions, record them here. Otherwise this section stays empty and the defaults above apply.
<!-- BOOTSTRAP:GIT_RULES:END -->

Full rules: @docs/engineering/GIT.md

---

## Navigating docs during task execution

Always load `@CLAUDE.md` and `@docs/INDEX.md` at the start of every task. INDEX is your map of all docs by task type — use it throughout execution, not just at the start.

**Consult specific docs during execution whenever you make decisions in these categories:**

| Deciding about... | Consult |
|---|---|
| Where to place a test file, marker to use, fixture structure | @docs/engineering/TESTING.md |
| Where to place application code, folder structure | @docs/engineering/STRUCTURE.md |
| Naming, type hints, async patterns, error handling | @docs/engineering/CODE_STYLE.md |
| Database queries, migrations, sessions | @docs/engineering/CODE_STYLE.md (Database section) |
| Auth, secrets, input validation, LLM security | @docs/engineering/SECURITY.md |
| Adding or pinning a dependency | @docs/engineering/TECH_STACK.md |
| Commits, branches, PR format | @docs/engineering/GIT.md |
| Language choices, naming domain terms | @docs/engineering/LANGUAGE.md |
| System design, layering, component responsibilities | @docs/engineering/ARCHITECTURE.md |

For task-level orientation (what doc for what kind of work), see `@docs/INDEX.md`.

**Quick workflow reference:**

| Working on... | Load |
|---|---|
| Planning what to build | @docs/ROADMAP.md |
| Just merged a task | @docs/ROADMAP.md — update status and fill Learnings |
| New feature | @docs/engineering/ARCHITECTURE.md @docs/engineering/STRUCTURE.md @docs/engineering/CODE_STYLE.md @docs/engineering/TESTING.md |
| New endpoint or external boundary | @docs/engineering/ARCHITECTURE.md @docs/engineering/SECURITY.md @docs/engineering/TESTING.md |
| New use case | @docs/engineering/ARCHITECTURE.md @docs/engineering/SECURITY.md @docs/engineering/TESTING.md |
| LLM integration | @docs/engineering/SECURITY.md @docs/engineering/ARCHITECTURE.md @docs/engineering/CODE_STYLE.md |
| Database work | @docs/engineering/ARCHITECTURE.md @docs/engineering/CODE_STYLE.md @docs/engineering/SECURITY.md |
| Adding a dependency | @docs/engineering/TECH_STACK.md |
| Significant technical decision | @docs/engineering/adr/ — write an ADR |

**If a prompt conflicts with a doc:**

If the prompt says one thing and a doc says another (e.g., prompt says "put test in `tests/`" but TESTING.md says "put in `tests/unit/`"), STOP and ask the user which takes precedence. Do not silently follow the prompt. The answer might be "update the doc" or "update the prompt" — not your call unilaterally.

---

## Working with prompts from `.ai/prompts/`

Prompts carry the decisions already made during planning. The user has already thought through the major choices. Your job is execution, not re-planning.

**Before asking anything, check:**

1. Does the prompt specify it? → follow the prompt.
2. Do the project docs (`CLAUDE.md`, `docs/*.md`) specify it? → follow the docs.
3. Is it a universal language convention (formatting, naming, idiomatic patterns)? → apply it.
4. Is it a trivial choice already enforced by a linter or formatter? → apply and move on.

**Ask the user only when:**

The choice genuinely affects the solution, has no precedent in the repo, and getting it wrong would require a new task to fix.

Examples worth asking:

- "The prompt says 'use a caching library' — this is the first cache in the repo. Which library?"
- "The prompt requires structured logging. The stack uses `structlog`. Should the formatter be JSON (production) or key-value (development)?"
- "You asked to add a new endpoint. There is no existing pattern for authentication in this module. JWT like the existing pattern, or something different?"

Examples NOT worth asking (decide yourself):

- "Should the test file follow the framework's default naming?" (yes, follow it)
- "Should I use the async or sync variant?" (the docs answer this)
- "What indent size?" (the formatter answers this)
- "Should I add a docstring?" (the docs say yes for public functions)

**How to ask, when asking is warranted:**

Follow the same A/B/C structure the Planner uses. Give the user material to decide quickly, not open-ended questions.

Format:

```
Context: <brief explanation of where the ambiguity is and why it matters>.

Options:
A) <option>. Pro: <pro>. Con: <con>.
B) <option>. Pro: <pro>. Con: <con>.
C) <option>. Pro: <pro>. Con: <con>.

My recommendation: <letter>, because <reasoning>.

Which do you prefer?
```

Always take a position with a recommendation. Do not ask neutrally ("which one?"). Listing alternatives without an opinion shifts thinking work back to the user.

**After getting the user's answer, record the decision.**

Append a brief entry to the `<runtime-decisions>` section at the bottom of the same prompt file, using the format defined in `@.ai/prompts/TEMPLATE.md`. This keeps prompts self-contained for future reuse.

Include the updated prompt file in the archive commit for the task.

### Planning prompts

Some prompts do not correspond to a single implementation task — they capture replanning events, documentation reorganizations, convention changes, or other structural decisions that span multiple tasks or none at all. These live in a separate directory:

- **Location:** `.ai/prompts/planning/`
- **File name pattern:** `YYYY-MM-DD-slug-in-english.md` (e.g., `2026-05-07-roadmap-reorganization.md`).
- **When to use:** any prompt whose output is structural (renaming conventions, splitting docs, replanning future tasks, adding new policy sections) rather than a feature implementation.
- **Reference from `@docs/ROADMAP.md`** in a "Planning events" section, so the chronological log stays discoverable from the roadmap index.

Execution prompts live in `.ai/prompts/execution/<phase>/TSK-NNN-<slug>.md`. The `planning/` directory is reserved for non-implementation work.

---

## Current build status

The project follows an incremental build plan in `@docs/ROADMAP.md`. The roadmap is the source of truth for what's done, what's in progress, and what's next.

**Before starting work:**

1. Open `@docs/ROADMAP.md` and find the current phase and the next task in queue.
2. Confirm the task's `Depends on` prerequisites are all marked complete.
3. Do not skip ahead — later tasks assume the foundation of earlier ones.

**After merging a task:**

1. Update the task's status to ✅ Complete with the link to the merged PR in `@docs/ROADMAP.md`.
2. Fill the "Learnings" subsection: what was done, what surprised you, what decisions were made along the way.
3. If the phase is complete, fill the phase retrospective.

This turns the roadmap into a living technical diary of the project. Future tasks read past learnings and calibrate accordingly.

---

## Hard "no"s

These are non-negotiable. No exceptions without a new ADR.

The defaults below apply to any project using this template:

- ❌ Committing secrets in any form (`.env`, fixtures, logs, error messages, response payloads).
- ❌ Returning raw database models from API endpoints.
- ❌ Reading environment variables directly anywhere in the application (use the typed settings entry point).
- ❌ Raw SQL with string interpolation.
- ❌ Print statements for logging. Use the project's structured logger.
- ❌ Wildcard imports (`from x import *`).
- ❌ Relative imports.
- ❌ Mutable default arguments.
- ❌ Merge commits on `main` — squash only, linear history.
- ❌ User input treated as LLM instruction without delimiters and output validation.
- ❌ Tool calls from LLM triggering side effects without validation or user confirmation.
- ❌ Mocks asserting on internal implementation details — use fakes for collaborators.
- ❌ Snapshot tests.

<!--
PYTHON_REFERENCE_EXAMPLE — Hard "no"s

Additional Python-specific hard "no"s used by the author:

- ❌ `requirements.txt`, `requirements-dev.txt`, `requirements-ci.txt` — use `pyproject.toml` groups.
- ❌ UUIDv4 or autoincrement IDs.
- ❌ `python-dotenv`, `passlib`, `bcrypt`, `python-jose`, `pylint`, `LangChain`, `LlamaIndex`.
- ❌ `os.environ` direct access anywhere in the app.
- ❌ `datetime.now()` without timezone, or `datetime.utcnow()` (deprecated).
-->

<!-- BOOTSTRAP:HARD_NOS:START -->
> 📝 Filled and extended by bootstrap step `BS-05-conventions.md` during the conventions step, and grows as the project encounters new constraints.
>
> Project-specific hard "no"s are added here. They are non-negotiable like the defaults above — but specific to this project's stack, domain, or compliance constraints.
<!-- BOOTSTRAP:HARD_NOS:END -->

---

## Before writing code

1. Check the relevant doc(s) — don't improvise on topics already decided.
2. If the task suggests a technical decision not yet made, **ask** before assuming.
3. If the task conflicts with an existing doc, **flag it** — don't silently contradict.

## Before opening a PR

Run through the security checklist in `@docs/engineering/SECURITY.md` and the PR checklist in `@.github/PULL_REQUEST_TEMPLATE.md` (if it exists).

---

## Current phase

<!-- BOOTSTRAP:CURRENT_PHASE:START -->
> 📝 Filled by bootstrap step `BS-07-roadmap.md` after the roadmap is created.
>
> This section will name the active phase, list what is in scope for it, and (importantly) what is out of scope. Future phases are documented in `@docs/ROADMAP.md` — they are not implemented now, even if the code would be "cleaner" by anticipating them. Scope discipline matters more than speculative generality.
>
> When the active phase changes, update this section.
<!-- BOOTSTRAP:CURRENT_PHASE:END -->

---

## Ignored paths

The following paths contain drafts, notes, or speculative content. Never read them, never reference them, never include them when reasoning about the project:

- `docs/_drafts/**` — draft documents not yet accepted as policy
- `**/*.draft.md` — any file with `.draft.md` extension
- `NOTES.md`, `SCRATCH.md` (if they exist) — personal notes

If the user explicitly asks you to read a file in these paths, ask for confirmation first and note that the content is non-authoritative.

# Phase 1 — Repository foundation

**Goal:** Repository fully configured. CI, linting, type checking, pre-commit hooks, and tests all running on every PR. No business code yet.

**Milestone:** you can clone the repo, install dependencies, run tests, open a PR with signed commits, and CI validates everything automatically.

> **Note on this phase file.** This is a starting point seeded by the template. It assumes a typical setup for a modern engineering project: tooling, lint/format, type check, tests, CI, pre-commit, signed commits, branch protection. Adjust the specific tasks to match the project's stack chosen in `BS-04-tools.md` of the bootstrap. The shape and ordering are intentional — they minimize broken intermediate states — but the tools themselves are the project's choice.

---

## TSK-001 — Initial commit: documentation and project skeleton

**What:** Add all documentation already written (`CLAUDE.md`, `docs/` contents, `.github/PULL_REQUEST_TEMPLATE.md`). Create the application folder skeleton with empty subdirectories (using `.gitkeep`).

**Why first:** documentation is the shared context everything else depends on. Without it, later tasks lack grounding.

**Depends on:** nothing.

**Acceptance:** repository contains all docs; folder structure matches `@docs/engineering/STRUCTURE.md`; no application code yet.

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-002 — `.gitignore` and equivalent build-exclude files

**What:** Stack-appropriate `.gitignore` covering virtual environments, caches, IDE files, secrets files, build artifacts. If using containers, add `.dockerignore` excluding the same plus `tests/`, `docs/`, `.git/`.

**Why early:** prevents polluting the first real commits with junk files. Build-exclude files matter even now so later container builds are lean.

**Depends on:** TSK-001.

**Acceptance:** virtual environments and caches never show up in `git status`; container builds (when they exist) don't include excluded paths.

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-003 — Project manifest skeleton

**What:** Minimal project manifest declaring the project (name, version, runtime version pin). One runtime dependency for smoke testing. Empty dev dependency group. No tool configs yet.

**Why before tooling configs:** linter, type checker, test runner configs all live inside the project manifest (or are referenced from it). Need the file to exist first.

**Depends on:** TSK-002.

**Acceptance:** manifest parses as valid; follows the stack's standard structure.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-003 in Python

Create minimal `pyproject.toml` declaring the project (name, version, Python ≥3.13).
One runtime dep for smoke testing (e.g. `fastapi`). Empty `dev` dependency group.
No tool configs yet.

Acceptance: file parses as valid TOML; follows PEP 621 structure.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-004 — Configure package manager

**What:** Pin the runtime version. Run the package manager to generate the lockfile. Commit the lockfile. Document basic commands in `README.md`.

**Why before other tooling:** every later task will install tools via the package manager. Need it working first.

**Depends on:** TSK-003.

**Acceptance:** install command completes cleanly; lockfile is committed; runtime version prints correctly.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-004 in Python

`.python-version` pinning 3.13. Run `uv sync` to generate `uv.lock`. Commit
the lockfile. Document basic commands in `README.md` (placeholder until TSK-012).

Acceptance: `uv sync` completes cleanly; `uv.lock` is committed; `uv run python --version` prints 3.13.x.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-005 — Configure linter and formatter

**What:** Add linter and formatter to dev group. Configure them in the project manifest: line length, target version, rules, per-path exceptions. First check passes (nothing to check yet).

**Why before type checker:** linter is fastest feedback loop; covers more ground; easier to adopt incrementally.

**Depends on:** TSK-004.

**Acceptance:** linter check and format check both pass on the empty repo.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-005 in Python

Add `ruff` to dev group. Configure `[tool.ruff]` section in `pyproject.toml`:
line length, target version, rules, per-path exceptions. First `uv run ruff
check .` passes (nothing to check yet).

Acceptance: `uv run ruff check .` and `uv run ruff format --check .` both pass.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-006 — Configure type checker

**What:** Add type checker to dev group. Configure it in strict mode. Create a dummy module with a typed function to verify it runs. Type check passes.

**Why after linter, before pre-commit:** pre-commit will call the type checker; needs it installed and configured first.

**Depends on:** TSK-005.

**Acceptance:** type check passes; strict mode is active.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-006 in Python

Add `mypy` to dev group. Configure `[tool.mypy]` in strict mode. Create a
dummy `app/__init__.py` with a typed function to verify mypy runs. `uv run
mypy app/` passes.

Acceptance: `uv run mypy app/` passes; strict mode is active.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-007 — Configure pre-commit

**What:** Pre-commit config with hooks: linter (check + format), type checker, general validators (trailing-whitespace, end-of-file-fixer, YAML/TOML/JSON validators, large-file checker), secret scanner. Install locally and verify hooks run on commit.

**Why after linter and type checker:** the hooks invoke these tools; they must exist.

**Depends on:** TSK-005, TSK-006.

**Acceptance:** `pre-commit install` works; attempting to commit a file with trailing whitespace fails the hook; committing a clean file passes.

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-008 — Configure test runner

**What:** Configure the test runner in the project manifest. Register test markers (unit, integration, e2e, llm_live, slow — see `@docs/engineering/TESTING.md`). Add test runner dependencies to dev group. Create the empty test infrastructure files and a single smoke test.

**Why before architecture rules:** layering checks may run as part of test execution or as a separate step — the test runner must be working first.

**Depends on:** TSK-004.

**Acceptance:** test runner executes the smoke test and passes; marker filtering works.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-008 in Python

`[tool.pytest.ini_options]` in `pyproject.toml`. Register markers (`unit`,
`integration`, `e2e`, `llm_live`, `slow`). Add `pytest`, `pytest-asyncio`,
`pytest-cov` to dev group. Create `tests/conftest.py` (empty) and
`tests/test_smoke.py` with a single passing test.

Acceptance: `uv run pytest` runs and passes; `uv run pytest -m unit` also works.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-009 — Configure architecture rule enforcement

**What:** Configure the layering rules (per `@docs/engineering/ARCHITECTURE.md`) in an enforcement tool: domain imports nothing from application or infrastructure; application imports only from domain; etc. Create empty stub files in each layer so the rules have something to validate. Architecture check passes.

**Why now:** enforces architecture from day one. Adding later means refactoring accumulated violations.

**Depends on:** TSK-008.

**Acceptance:** architecture check passes against the empty skeleton; a test violation (temporarily adding a bad import) fails as expected.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-009 in Python

Add `import-linter` to dev group. Configure rules in `pyproject.toml` (or
`.importlinter`) reflecting `docs/engineering/ARCHITECTURE.md`. Create empty stub files
in each layer so the rules have something to validate. `uv run lint-imports`
passes.

Acceptance: `uv run lint-imports` passes against the empty skeleton.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-010 — CI pipeline

**What:** CI configuration running on every PR: install dependencies, run linter, formatter check, type checker, architecture rules, tests (excluding live/slow markers), dependency audit, secret scan. Configure branch protection on `main`: require CI green, require approval, require signed commits, require linear history, delete branch on merge.

**Why after all tooling:** CI must be able to run the same checks that pass locally. Configuring earlier means broken CI.

**Depends on:** TSK-005..TSK-009.

**Acceptance:** opening a PR triggers CI; all checks green; merging to `main` requires passing CI.

<!--
PYTHON_REFERENCE_EXAMPLE — TSK-010 in Python (GitHub Actions)

`.github/workflows/ci.yml` running on every PR: setup Python 3.13, `uv sync
--group dev`, `uv run ruff check`, `uv run ruff format --check`, `uv run mypy
app/`, `uv run lint-imports`, `uv run pytest -m "not llm_live and not slow"`.

Configure branch protection on `main`: require CI green, require 1 approval,
require signed commits, require linear history, delete branch on merge.
-->

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-011 — PR template

**What:** Verify `.github/PULL_REQUEST_TEMPLATE.md` is in place (committed in TSK-001 or added here). New PRs open with the template pre-filled.

**Why separate:** the template only makes sense once CI and checks exist, because it references them.

**Depends on:** TSK-010.

**Acceptance:** new PRs open with the template pre-filled.

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## TSK-012 — `README.md`

**What:** Human-facing README for the repo root. Describes: what the project is (1 paragraph), how to install dependencies, how to run tests, how to contribute (link to `@docs/engineering/GIT.md` and `@docs/INDEX.md`).

**Why last in Phase 1:** README can only be accurate once the setup works end to end.

**Depends on:** TSK-011.

**Acceptance:** a new developer following only the README can get the repo running locally; README links to the docs for deeper context.

**Status:** ⬜ Not started
**PR link:** —
**Learnings:** —

---

## Phase 1 retrospective

<!-- Fill when Phase 1 is complete. Cover: what worked, what was suboptimal, what to replicate, what to try differently in Phase 2. -->

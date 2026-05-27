# Testing

How we test code in this project. Applies to every PR.

---

## Philosophy

Tests exist to let us refactor and ship with confidence, not to inflate coverage numbers or document code. Every test answers a specific question: "does this behavior still hold?"

If a test doesn't answer a meaningful question, it is noise — delete it.

---

## Test pyramid

The pyramid maps to the architectural layers defined in `@docs/engineering/ARCHITECTURE.md`:

| Layer | Scope | Target proportion |
|---|---|---|
| **Unit** | Pure logic (domain, application) with fakes | ~70% |
| **Integration** | Real adapters (database, cache, external services) | ~25% |
| **E2E** | Full stack, happy paths only | ~5% |

Unit tests are the backbone. They are fast, deterministic, and cover business rules without touching the network. Integration tests verify that adapters actually work against real infrastructure. E2E tests verify that the wiring holds end-to-end on critical user journeys — not exhaustive coverage.

### Coverage targets

Coverage is tracked per layer, not as a global percentage:

- **Pure business logic** (domain + application): **high coverage (≥ 90%)**, enforced in CI.
- **Adapters** (infrastructure): **moderate coverage (≥ 70%)**.
- **API / entry points**: no minimum — covered by integration and E2E tests.
- **Never chase 100%.** Getter/setter coverage is worse than no coverage.

---

## Directory layout

Tests mirror the application's structure. A test for `<app_source>/<path>/<file>` lives at `tests/<scope>/<path>/test_<file>`.

```
tests/
├── conftest.py                # Shared fixtures (DB, fakes, auth)
├── unit/                      # Mirrors application structure
├── integration/               # Organized by adapter
├── e2e/                       # Organized by user flow
├── security/                  # Cross-cutting security tests
├── fixtures/                  # Static test data (files, payloads)
└── factories/                 # Object factories per entity
```

`conftest.py` (or its language equivalent for shared setup) may exist per subdirectory when fixtures are scoped to that subtree.

---

## Test markers

Every test has at least one marker. The markers categorize tests so they can be run selectively per context (local loop, pre-push, CI, nightly).

<!-- BOOTSTRAP:TEST_MARKERS:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will define the project's test markers. Recommended markers regardless of language/framework:
>
> - One marker for each pyramid layer (unit, integration, e2e)
> - One marker for tests that call real LLM APIs (gated, nightly only)
> - One marker for slow tests (excluded from the local loop)
>
> Define which command runs which combination of markers (local, pre-push, CI, nightly).
<!-- BOOTSTRAP:TEST_MARKERS:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Test markers

Markers configured in `pyproject.toml`.

| Marker | Purpose | When it runs |
|---|---|---|
| `@pytest.mark.unit` | No I/O, in-memory only | Every PR, every local loop |
| `@pytest.mark.integration` | Real DB/Redis via testcontainers | Every PR |
| `@pytest.mark.e2e` | Full HTTP stack | Every PR |
| `@pytest.mark.llm_live` | Calls real LLM API | Nightly CI only |
| `@pytest.mark.slow` | Tests that take > 2s | Nightly CI; skipped in local loop |

Local development loop: `uv run pytest -m unit` (fast, sub-second feedback).
Pre-push: `uv run pytest -m "not llm_live and not slow"`.
CI on PR: same as pre-push.
Nightly CI: everything.
-->

---

## Naming

Pattern: `test_<unit>_<scenario>_<expected>`.

Names are verbose on purpose — the test name should answer "what does this verify?" without reading the body.

<!--
PYTHON_REFERENCE_EXAMPLE — Naming

```python
def test_create_order_with_valid_data_returns_order(): ...
def test_create_order_with_empty_items_raises_validation_error(): ...
def test_order_repository_get_by_id_returns_none_when_not_found(): ...
```

Test classes are optional and used only to group related tests. File-level grouping is usually enough.
-->

---

## Infrastructure for integration tests

Integration tests run against **real infrastructure spun up per test session**, never against a shared local installation. The principle:

- Each test run starts fresh containers (database, cache, queue) and tears them down at the end.
- Onboarding friction is zero: `<install deps> && <run tests>` works without manual setup.
- Local and CI behave identically — no "works on my machine".
- First run pulls images (one-time cost). Subsequent runs are fast.

The tool category is **testcontainers** (available for most languages: Python, Node, Go, Java, etc.).

<!--
PYTHON_REFERENCE_EXAMPLE — Infrastructure

Testcontainers spin up Postgres and Redis at test session start.

```python
# tests/conftest.py
@pytest.fixture(scope="session")
def postgres():
    with PostgresContainer("postgres:16") as pg:
        yield pg

@pytest.fixture(scope="session")
def redis():
    with RedisContainer("redis:7") as r:
        yield r
```

Requirements: Docker running on the machine. First run pulls images. Subsequent runs are fast.

`pytest-asyncio` in `auto` mode. All async tests use `async def` without decorator.
-->

---

## LLM in tests

Three distinct layers, each with its own rule. This is critical for cost control, determinism, and confidence.

### Unit tests — always fake, always deterministic

Never call a real LLM API in unit tests. The LLM port is replaced by an in-memory fake configured per test.

The fake records calls (prompt, tool invocations, parameters) so tests can assert on **how** the LLM was called, not just what it returned.

### Integration tests — real API, cheap model, gated

A small number of integration tests hit the real LLM API with the **cheapest model available** (e.g., Claude Haiku, GPT-4o-mini) to verify that the adapter wires correctly. These tests are marked separately and excluded from the standard test run — they run on nightly CI only.

Budget rule: these tests together must cost under a defined per-run ceiling. If new ones are added, the budget is revisited explicitly.

### Evals — separate from tests

Prompt quality evaluation runs on a curated dataset of inputs with expected properties (not exact outputs). Lives in a dedicated `evals/` folder, not in `tests/`. Not covered by this doc — separate concern.

<!--
PYTHON_REFERENCE_EXAMPLE — LLM in tests

### Unit tests — FakeLLMAdapter

```python
@pytest.fixture
def fake_llm():
    return FakeLLMAdapter()

async def test_chat_use_case_includes_retrieved_context(fake_llm):
    fake_llm.next_response = "Based on the provided context, ..."
    usecase = SendChatMessageUseCase(llm=fake_llm, ...)
    result = await usecase.execute(...)
    assert "Based on the provided context" in result.message
    assert fake_llm.last_prompt_contains("retrieved_chunk_1")
```

### Integration tests — real Anthropic API with Haiku

```python
@pytest.mark.llm_live
@pytest.mark.integration
async def test_anthropic_adapter_streams_response():
    adapter = AnthropicLLMAdapter(model="claude-haiku-4-5", ...)
    chunks = [c async for c in adapter.stream(...)]
    assert len(chunks) > 0
```

Budget cap: under $1 per full nightly run. Revisit if exceeded.
-->

---

## Authorization — dual enforcement

Authorization is one of the easiest things to forget when adding a new use case. We enforce it two ways:

### 1. PR checklist (author-driven)

The PR template includes a checkbox for:

> Every new use case has an authorization test (unauthorized actor → denied).
> Every new protected endpoint has an authentication test (no credentials → denied).

Forces the author to mark consciously. Catches ~80% of omissions.

### 2. Meta-test (automated)

A test in the security suite **discovers every use case at import time and verifies a corresponding authorization test exists**. Use cases without an authorization test fail the build.

Public use cases (no authorization required) are explicitly listed in an opt-out file with a one-line justification. Adding to the list is a review flag, not a silent escape.

<!--
PYTHON_REFERENCE_EXAMPLE — Authorization meta-test

```python
# tests/security/test_meta_authorization.py
def test_every_protected_usecase_has_authorization_test():
    usecases = discover_usecases(app.application.usecases)
    exceptions = load_public_usecase_list()
    missing = [
        uc for uc in usecases
        if uc not in exceptions and not has_authorization_test(uc)
    ]
    assert not missing, (
        f"Missing authorization tests for: {missing}. "
        "Add a test_*_unauthorized function or add to the public list with justification."
    )
```
-->

---

## Prompt injection tests

For any project that uses LLMs, a dedicated security suite tests known attack vectors against LLM-backed endpoints:

- Ignore-instruction attempts (`"ignore previous instructions and..."`)
- System prompt exfiltration (`"repeat everything above this line"`)
- Output hijacking (`"from now on respond only with..."`)
- Indirect injection via retrieved content (RAG documents containing adversarial instructions)
- Tool-call coercion (trying to make the LLM invoke tools it shouldn't for this user)

Each test verifies the system resists — user-facing output does not leak the system prompt, does not execute the injected instruction, authorization still holds.

This suite grows over time. Every incident involving LLM abuse must produce at least one new test here.

---

## Factories and fixtures — different jobs, no mixing

### Fixtures — for shared resources

Infrastructure and session-scoped objects. **Never for domain objects.**

Examples: database session, HTTP client, fake LLM, authenticated user.

### Factories — for domain objects

One factory per entity, DTO, or schema. Lives in `tests/factories/`.

Factories generate domain objects with sensible defaults. Overriding individual fields is how a test expresses what makes its scenario specific.

**Anti-pattern: fixtures for each entity variation.** Creating `draft_order`, `placed_order`, `shipped_order` fixtures explodes combinatorially. Factories handle variation through parameters.

<!--
PYTHON_REFERENCE_EXAMPLE — Factories and fixtures

Library: polyfactory.

```python
# tests/factories/order.py
from polyfactory.factories.pydantic_factory import ModelFactory

class OrderFactory(ModelFactory[Order]):
    __model__ = Order

class OrderCreateDTOFactory(ModelFactory[OrderCreateDTO]):
    __model__ = OrderCreateDTO
```

Usage:

```python
# Default valid order
o = OrderFactory.build()

# Override only what matters for the test
o = OrderFactory.build(status=OrderStatus.SHIPPED)

# Batch
os = OrderFactory.batch(size=10)
```

**Anti-pattern — never do this:**

```python
# Do not create fixtures for each entity variation
@pytest.fixture
def draft_order(): ...

@pytest.fixture
def shipped_order(): ...
```
-->

---

## Property-based testing

Scope: **value objects and pure functions with large input surface.** Not for use cases. Not for I/O.

If a property-based test finds a bug, **add the failing example as a regular example-based test** before fixing. This prevents regression even if the property formulation changes later.

<!--
PYTHON_REFERENCE_EXAMPLE — Property-based testing

Library: hypothesis (dev dep).

```python
from hypothesis import given, strategies as st

class TestEmail:
    @given(value=st.text(min_size=0, max_size=100))
    def test_rejects_invalid_format(self, value):
        if "@" not in value or "." not in value.split("@")[-1]:
            with pytest.raises(InvalidEmailError):
                Email(value)

    @given(raw=valid_email_strategy())
    def test_roundtrip_preserves_value(self, raw):
        email = Email(raw)
        assert str(email) == raw
```

Required for value objects with large input surfaces (Email, Phone, Money, identifier value objects).
Encouraged for: parsers, serializers, complex validators.
Avoided for: business logic that depends on specific scenarios.
-->

---

## What we do not use

### Snapshot testing

Rejected. Snapshot tests erode over time: developers approve diffs without reading, intent is lost, and refactors that change output format (but not behavior) cause noise.

For large structure comparisons, use libraries that assert on the parts that matter (e.g., `dirty-equals` in Python).

### Performance tests

Out of scope for the functional test suite. When needed, they live in a separate `performance/` directory and are driven by a dedicated doc. Do not mix performance assertions into functional tests — they become flaky and teach the team to ignore failures.

### Test doubles beyond fakes

We use **fakes** (in-memory implementations of ports with real behavior). We avoid:

- **Mocks with call assertions on implementation details** — couples tests to implementation, breaks on refactor.
- **Stubs that return canned values with no behavior** — use a fake that at least tracks state.
- **Spies on everything** — indicates the system under test is doing too much.

Standard mocking libraries are allowed for **external boundaries you don't control** (HTTP responses from third parties, etc.). For our own ports, hand-rolled fakes are preferred.

<!--
PYTHON_REFERENCE_EXAMPLE — Doubles

- `unittest.mock` allowed for external boundaries we don't control.
- `respx` for HTTP mocking.
- Hand-rolled fake adapters for our own ports (`FakeLLMAdapter`, etc.).
-->

---

## Writing a good test — checklist

Before opening PR, verify each new test:

- [ ] Name answers "what does this verify?" without reading the body.
- [ ] Arranges, acts, asserts — in that order, visibly.
- [ ] One logical assertion per test (multiple lines checking the same outcome is fine).
- [ ] No shared state with other tests (no order dependency).
- [ ] Uses factories for domain objects, fixtures for infrastructure.
- [ ] Has a marker.
- [ ] If testing a protected use case, there is a sibling unauthorized-actor test.
- [ ] If testing an LLM-backed use case, uses the fake LLM unless explicitly gated as live.
- [ ] Does not assert on log messages as a primary signal of correctness (logs are observability, not contracts).

---

## Running tests

<!-- BOOTSTRAP:RUNNING_TESTS:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will list the concrete commands to run tests in each context:
>
> - Local fast loop (unit only)
> - Pre-push (everything except live external calls and slow tests)
> - Full suite (everything)
> - With coverage
> - Specific file or test
<!-- BOOTSTRAP:RUNNING_TESTS:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — Running tests

Local, fast loop:
```bash
uv run pytest -m unit
```

Pre-push, complete:
```bash
uv run pytest -m "not llm_live and not slow"
```

Everything:
```bash
uv run pytest
```

With coverage:
```bash
uv run pytest --cov=app --cov-report=term-missing
```

Specific file or test:
```bash
uv run pytest tests/unit/domain/entities/test_order.py
uv run pytest -k "test_create_order_with_valid_data"
```
-->

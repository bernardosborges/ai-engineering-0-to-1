# BS-04 — Tools

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-04 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the tech stack formalization conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern), this file for the specific content of BS-04, and the output of BS-01 + BS-02 + BS-03 (`docs/PRODUCT.md` + `docs/product/pd-*.md` + `docs/product/ft-*.md` + `docs/engineering/ARCHITECTURE.md` + `docs/engineering/STRUCTURE.md`) as input to this step.

---

## 1. Goal

Formalize the project's tech stack by walking each section of `docs/engineering/TECH_STACK.md` with the Maintainer. The stack signals surfaced during BS-01, BS-02, and BS-03 (the Decisions Captured mental register) get pinned to concrete versions, organized into dependency groups, and justified one-line-per-item. Architecturally significant tool choices get their own ADRs.

The goal is **not** to invent the stack from scratch. Many decisions were already made — perhaps "Python" was captured in BS-01, "PostgreSQL with pgvector" in BS-02, "FastAPI" and "Anthropic" in BS-03. BS-04 takes those and:

- Confirms each one is still the choice (the Maintainer hasn't changed mind between steps)
- Pins versions (the actual version range that goes into the manifest)
- Organizes dependencies into production vs development groups
- Captures explicit rejections in "Explicitly NOT used" so they don't get re-litigated later
- Creates ADRs for choices where a future contributor would benefit from knowing why

By the end of BS-04, a competent engineer joining the project could read `TECH_STACK.md` and:

- Install the same stack with one command from the manifest
- Know which group each dependency belongs to (prod vs dev) and why
- Know which alternatives were rejected and why
- Find the ADRs that explain non-obvious choices

---

## 2. What this step produces

### Documents updated

- **`docs/engineering/TECH_STACK.md`** — every BOOTSTRAP marker filled with content specific to the project:
  - Runtime section: language version, package manager, lockfile policy
  - Dependency organization: how production vs development deps are separated, install commands
  - Production dependencies: tables by category (Core framework, Data layer, AI/LLM, etc.), with version ranges and one-line purpose per item
  - Development dependencies: tables by category (Testing, Code quality, Dev tooling)
  - Explicitly NOT used: rejected alternatives with reasons
  - Runtime upgrade policy: when the next runtime version bump is acceptable and what it requires
  - Where versions actually live: pointer to manifest, lockfile, runtime pin, container base image

  Fixed sections (How to read this doc, Version bump policy) reviewed and adjusted only if a project-specific reason emerges.

### Documents potentially created

- **`docs/engineering/adr/NNNN-<slug>.md`** — ADRs for architecturally significant tool choices that surface during BS-04. The Bootstrapper does NOT come into BS-04 with a fixed checklist of ADRs to create. Candidates emerge from the conversation and the Maintainer decides.

  Typical candidates (not exhaustive, varies by project):
  - Choice of database extension (e.g., pgvector over a dedicated vector DB)
  - Choice of LLM provider when alternatives are credible
  - Choice of async/sync model (when the codebase commits to async, sync, or mixed)
  - Choice of package manager when a non-default is chosen (e.g., uv over pip/poetry)
  - Choice of observability stack (OTel + specific logger + specific tracer combination)
  - Choice of ID generation strategy (UUIDv7 vs UUIDv4 vs BIGSERIAL)
  - Choice of password hashing algorithm (Argon2id vs bcrypt vs scrypt)
  - Choice of cache/queue infrastructure when multiple options were credible

  The rule is: **if a future contributor without context might question the choice, write an ADR**. If the choice is obvious from the ecosystem (e.g., `git` for version control, `ruff` for Python linting in 2026), no ADR needed.

  ADRs use `docs/engineering/adr/TEMPLATE.md` as a floor.

### What is NOT produced in BS-04

- Code style conventions, naming patterns, async rules, error handling patterns — BS-05 (Conventions)
- Test pyramid shape, marker conventions, fake adapter patterns — BS-05
- Security policy detail (token lifetimes, rate limit values, JWT algorithm choice if cross-cutting was set in BS-03) — BS-05 expands the architectural commitments from BS-03 §5
- Deployment topology, container orchestration, CI/CD pipeline — emerge during implementation; partially touched only when a tool choice has deploy implications (e.g., choosing managed Postgres affects deploy)
- Application code — bootstrap doesn't write code
- The actual `pyproject.toml`, `package.json`, lockfiles, or any other machine-readable manifest — those are written during TSK-001 (the first execution task), based on TECH_STACK.md

### Critical premise — BS-04 formalizes Decisions Captured

By BS-04, the Bootstrapper holds in mental state a substantial list of Decisions Captured from BS-01, BS-02, and BS-03. The opening of BS-04 (sub-block 7.1) recaps these explicitly, confirms each one still holds, and uses them as the starting position for each section walk.

If the Maintainer revises a Decision Captured during BS-04 (e.g., "actually let's switch from FastAPI to Litestar"), the Bootstrapper:

- Acknowledges the revision
- Surfaces ripple effects (any earlier doc that referenced the old choice may need updating)
- Applies the ripple per BOOTSTRAPPER section 11 (Ripple effects and propagation)
- Continues with the new choice

Revisions are normal in BS-04 — sometimes the act of pinning a version surfaces an issue that wasn't visible during the earlier abstract mention.

---

## 3. Exit criteria

BS-04 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

- [ ] **Runtime section** filled with language version (specific), package manager (specific), lockfile name (specific). Forbidden tools in this category named (e.g., "no pip, no poetry").

- [ ] **Dependency organization section** filled with the group structure (production vs dev, possibly a separate CI group if justified), the install commands for each, and which groups ship to the production image.

- [ ] **Production dependencies section** filled with one table per applicable category. At minimum, the categories that match the components defined in ARCHITECTURE.md §2 (Component view) are covered. Each dependency has version range and one-line purpose.

- [ ] **Development dependencies section** filled with tables for Testing, Code quality, and Dev tooling. Each dependency has version range and one-line purpose.

- [ ] **Explicitly NOT used section** filled with at least the rejections that came up during BS-04 conversation. The "use instead" column references the actually-used dependency by name.

- [ ] **Runtime upgrade policy section** filled with the runtime version pinned, the upgrade trigger (date, version EOL, dependency requirement), and what an upgrade requires (typically: ADR + container base image change + CI matrix update + dependency compat check).

- [ ] **Where versions actually live section** filled with the manifest file, lockfile, runtime pin file, and container base image references for the chosen stack.

### Soft requirements (worth pushing for but not blockers)

- [ ] ADRs created for non-default tool choices where a future contributor would benefit from the rationale. Number is variable — could be zero (if every choice is ecosystem-default) or many (if the project makes multiple non-obvious choices).

- [ ] Cross-references from TECH_STACK.md to ADRs added inline (e.g., "see ADR 0003 for the rationale" next to the relevant dependency).

- [ ] Cross-references from `docs/engineering/ARCHITECTURE.md` updated when a tool choice clarifies or constrains an architectural decision (e.g., the LLM provider name added to §7 Integrations table, the observability stack added to §5 sub-section).

### What does NOT block exit

- Specific schema design for any entity — `DATA_MODEL.md` is a future doc
- Specific endpoint design — `API_CONVENTIONS.md` is a future doc
- Specific prompt files or model-per-task strategy — `AI_INTEGRATION.md` is a future doc
- Code style decisions, naming patterns, async patterns — BS-05
- Test strategy specifics beyond naming the test libraries — BS-05
- Security implementation specifics (token lifetimes, rate limit numbers, JWT algorithm) — BS-05
- CI/CD pipeline definitions — emerge during implementation

The Bootstrapper does not advance to BS-05 silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before moving on, the Explicitly NOT used section is empty. We made several rejections during the conversation (e.g., `<X>` over `<Y>`). I want to capture those before BS-05."

---

## 4. Anchor questions

These are the four questions that **must be answered with confidence** before BS-04 closes. They are not asked in order — they emerge through the section-by-section walk (see Section 7 — Conversation flow). The Bootstrapper keeps mental track of which ones are still open, and surfaces any that remain unanswered before declaring the step complete.

### Anchor 1 — What is the exact runtime the project commits to?

This becomes the Runtime section of TECH_STACK.md. The Maintainer must commit to:

- **Specific language version** — not "Python 3.x", but "Python 3.13.x with minimum 3.13" or "Node 20 LTS pinned to 20.18.x"
- **Specific package manager** — including a forbidden list (e.g., "uv only, no pip, no poetry, no pipenv")
- **Specific lockfile** — name of the file, commit-or-not policy

The forcing function here is **specificity at the version level**. "We'll use a recent Python" is not a commitment — "Python 3.13.x, pinned in `.python-version`, with `uv.lock` committed" is.

If the Maintainer hasn't decided between two options (e.g., "Node vs Bun"), use forcing function 8.5 to land a commitment. A deferred decision here blocks every other section.

### Anchor 2 — How are dependencies organized into groups?

This becomes the Dependency organization section. The Maintainer must commit to:

- **The group structure** — at minimum, production and development. Possibly a separate CI group if a tool is exclusive to CI.
- **The install commands per group** — what the Maintainer (and future contributors) will type to set up each environment
- **What ships to production** — explicitly, which groups end up in the production image and which don't

The risk to detect: the Maintainer wants every tool in one group ("just install everything"). Push back if this is unjustified — production images bloated with dev tooling are a security and cost concern.

### Anchor 3 — What's in the production stack, by category?

This becomes the Production dependencies section. The Bootstrapper walks each category that applies to the project's component view (from ARCHITECTURE.md §2):

- Core framework
- Data layer (DB driver, ORM, migrations, cache)
- Messaging/channels (if the project has external messaging)
- AI/LLM (LLM SDK, token counting, embedding clients)
- Background jobs (queue, scheduler)
- Object storage (if the project handles files)
- HTTP & resilience (HTTP client, retry, circuit breaker)
- Observability (logging, tracing, metrics)
- Auth & security (JWT, password hashing, crypto)
- Identifiers (UUID, ULID, etc.)

For each category that applies, the Maintainer commits to specific packages with version ranges and one-line purposes. Categories that don't apply are skipped (e.g., a project without LLM integration doesn't fill the AI/LLM category).

The Bootstrapper does NOT assume which categories apply. The component view from BS-03 determines which categories are necessary; the conversation confirms each.

### Anchor 4 — What is explicitly rejected, and why?

This becomes the Explicitly NOT used section. Every BS-04 conversation surfaces rejections — alternatives the Maintainer considered and ruled out. The Bootstrapper captures these systematically:

- Old patterns from the ecosystem (`requirements.txt`, `setup.py`, `pyproject.toml` without uv)
- Competing libraries in the same slot (FastAPI vs Litestar vs Flask)
- Libraries with poor maintenance or security issues
- Libraries that hide critical concerns (e.g., LangChain hiding token usage)
- Patterns the Maintainer outgrew in past projects

If after the full walk the Explicitly NOT used section is empty, the Bootstrapper probes: "Every project rejects something. What did you consider and rule out for at least one of these slots?" An empty section after probing is acceptable but rare.

---

## 5. How BS-04 relates to BS-03

BS-04 reuses the **same persistence pattern as BS-03**: section-by-section, with immediate application of each section's final content. The Maintainer is at the keyboard, applies 7-9 small edits across the step (one per section of TECH_STACK.md plus any ADRs created).

This is intentional — once the Maintainer learned the pattern in BS-03, BS-04 doesn't surprise them. The opening (sub-block 7.1) references this directly:

> "BS-04 works the same way as BS-03: section-by-section, with you applying each section's final content before we move to the next. Difference: BS-04 is tighter — fewer sections to walk, but each section requires more specific commitment (versions, exact packages). Plan for 7-9 small edits."

### What carries over from BS-03

- **Section-by-section walk** with the same 6 sub-steps (A: Present what's there → B: Check conflicts with product → C: Discuss adjustments and fill markers → D: Present final content → E: Maintainer applies → F: Move to next)
- **Capture-aloud pattern** within each section's discussion
- **`~~~markdown` fenced blocks** for the final content of each section
- **BOOTSTRAP marker replacement** rule: the entire marker block (START/END comments + placeholder description) is replaced by the actual content
- **Decisions Captured** mental register continues to accumulate (any signals for BS-05 that emerge during BS-04 — code style preferences, security commitments, test stack — get held for the bridge to BS-05)

### What differs from BS-03

- **Tighter scope.** BS-04 touches one main file (TECH_STACK.md) plus possibly some ADRs. BS-03 touched two main files (ARCHITECTURE.md + STRUCTURE.md). Fewer section visits — typically 7-9 instead of 16.
- **More specificity required.** BS-03's content can be abstract in places (the component view describes shape, not implementation). BS-04 demands exactness: version ranges, package names, install commands. The Bootstrapper pushes for specificity throughout.
- **More frequent ADR candidates.** BS-03 typically produced 0-3 ADRs (architectural choices are usually defaults). BS-04 produces variable number — could be zero (every choice is ecosystem-default for the language) or many (project makes multiple non-default choices). The Bootstrapper proposes ADRs as candidates emerge.
- **Heavier use of Decisions Captured.** BS-04 opens with a substantial recap of stack signals from BS-01/BS-02/BS-03. Each section walk starts from those signals, not from a blank slate.

### What the Maintainer needs at the keyboard

Same as BS-03:

- Active editor on TECH_STACK.md (open, ready to apply per-section edits)
- Active editor on `docs/engineering/adr/` (ready to create new files if ADRs emerge)
- Possibly active editor on ARCHITECTURE.md (for the cross-reference updates noted in Section 3 Soft requirements)

If the Maintainer can't apply edits during the conversation, they should pause BS-04 and resume when they can.

---

## 6. Coverage strategy

Like BS-03, BS-04's coverage is **fixed by the structure of TECH_STACK.md**. The Bootstrapper walks the sections in a defined order, doing the appropriate work at each.

### Walk order

| Order | Section | Type | What BS-04 does there |
|---|---|---|---|
| 1 | How to read this doc | Fixed | Quick review. Usually no changes. |
| 2 | Runtime | Marker | Pin language, package manager, lockfile. Foundational — every later section depends on language. |
| 3 | Dependency organization | Marker | Define groups, install commands, what ships to prod. Required before listing deps because the list goes into groups. |
| 4 | Production dependencies | Marker | Walk each applicable category (Core framework, Data layer, AI/LLM, etc.). Fill tables. This is the heaviest section. |
| 5 | Development dependencies | Marker | Walk Testing, Code quality, Dev tooling. Fill tables. |
| 6 | Explicitly NOT used | Marker | Capture rejections that emerged during the walk. |
| 7 | Version bump policy | Fixed | Quick review. Defaults usually stand. |
| 8 | Runtime upgrade policy | Marker | Pin upgrade triggers and requirements. |
| 9 | Where versions actually live | Marker | Pointer table to manifest, lockfile, runtime pin, container base image. |

This order produces **9 section visits**. Visits 2 (Runtime), 3 (Dependency organization), and 4 (Production dependencies) are the heavy ones — together they take 20-40 minutes of the step. The rest are quick (2-5 minutes each).

Total BS-04 time: 30-60 minutes typical.

### Why this order

- **Runtime first** because every later section depends on language conventions (Python deps look different from Node deps, package manager affects install commands, etc.)
- **Dependency organization second** because production and development lists need to know which groups exist before they can be populated
- **Production dependencies third** because it's the largest section and the Maintainer's energy is highest early
- **Development dependencies fourth** because dev tools are language-specific and the Maintainer has already committed to the language in §1
- **Explicitly NOT used after both deps sections** because rejections accumulate during the walk — capturing them at the end means the section reflects the full conversation
- **Version bump policy and Runtime upgrade policy near the end** because they're meta-decisions about how the stack evolves; they make most sense once the stack is on the page
- **Where versions actually live last** because it's a pointer table to artifacts that will exist after BS-04 (manifest, lockfile, container image) — easier to fill once the stack is concrete

### What's constant within each section visit

Regardless of section type (Fixed vs Marker), the Bootstrapper applies the same six steps per section (carried over from BS-03 Section 7.2):

1. Present what's there
2. Check conflicts with product (and with ARCHITECTURE.md from BS-03)
3. Discuss adjustments and fill markers
4. Present the final section content
5. Maintainer applies
6. Confirm applied, move to next

The depth of step 3 varies by section. Runtime and Dependency organization usually have minimal discussion (Decisions Captured already drive the answer). Production dependencies have substantive discussion per category — the heaviest step of BS-04.

### When the Maintainer wants to deviate from this order

Acceptable on request, but the Bootstrapper flags the cost:

> "We can fill Production dependencies before Dependency organization if you prefer, but normally I do organization first because the deps section needs to know which groups exist. If we flip the order, we may need to re-assign deps to groups later. OK with that?"

The Maintainer's call.

---

## 7. Conversation flow

The BS-04 conversation has **three macro-phases**: opening (7.1), section-by-section walk (7.2), and bridge to BS-05 (7.3). Same structural shape as BS-03 but tighter — the walk is 9 sections instead of 16.

### 7.1 — Opening, Decisions Captured recap, pattern reminder (10-15 minutes)

**Purpose:** Re-establish context after BS-03. Recap Decisions Captured accumulated across BS-01/BS-02/BS-03 — this is the largest recap of any BS step because BS-04 is where stack signals finally get formalized. Confirm each signal still holds. Remind the Maintainer the pattern is the same as BS-03.

**Technique:**

1. **Recap BS-03 output briefly.** Two or three sentences:

   > "BS-03 captured the architectural shape: component view, main flows, data view, integrations, quality attributes. Now in BS-04 we pin the tech stack — every component named in ARCHITECTURE.md §2 gets concrete library names, versions, and install organization. The result is `TECH_STACK.md` complete and any non-obvious tool choice with its own ADR."

2. **Recap Decisions Captured exhaustively.** This is the largest recap of any bootstrap step. List every stack signal:

   > "Tech stack signals carried into BS-04, from all earlier steps:
   >
   > **From BS-01:**
   > - Language: <X>
   > - Primary channel: <Y>
   >
   > **From BS-02:**
   > - Database family: <Z>
   > - <any other infra signals>
   >
   > **From BS-03:**
   > - Web framework: <A>
   > - Async work / queue: <B>
   > - LLM provider: <C>
   > - Observability stack: <D>
   > - Object storage: <E>
   > - <any other tool-specific mentions>
   >
   > These are starting positions for each section. Any of them changed since they came up? Anything still open that we deferred?"

   The Maintainer either confirms or revises. Revisions get applied with ripple per BOOTSTRAPPER section 11.

3. **Remind the section-by-section pattern.** Brief — the Maintainer already learned this in BS-03:

   > "Same pattern as BS-03: section-by-section, you apply each section's content before we move on. Plan for 7-9 small edits in TECH_STACK.md plus any ADRs we decide to write. We can pause between sections if you need."

4. **Preview the walk order.** Brief:

   > "We'll go through TECH_STACK.md in this order: Runtime, Dependency organization, Production dependencies (the longest — we walk each applicable category), Development dependencies, Explicitly NOT used, Runtime upgrade policy, Where versions live. Plus a quick review of the two fixed sections (How to read, Version bump policy) and any ADRs that emerge along the way. Total around 30-60 minutes."

**Signal to move on:** Decisions Captured are confirmed, the pattern is re-acknowledged, the Maintainer is at the keyboard. Start the section walk.

### 7.2 — Section-by-section walk (variable, ~20-45 minutes total)

**Purpose:** Walk TECH_STACK.md in the order defined in Section 6. For each section, apply the six-step pattern (present, check conflicts, discuss, present final, apply, confirm) — same scaffold as BS-03 Section 7.2.

This sub-block is not one conversation — it's 9 micro-conversations chained together. The scaffold is identical to BS-03's; the substance changes.

**Generic per-section pattern (applies to all 9 visits):**

The 6 sub-steps (A through F) work the same way as in BS-03. See BS-03 Section 7.2 for the canonical description. What's specific to BS-04 is per-section operational guidance below.

---

**Per-section operational guidance (the 9 visits):**

#### Visit 1 — How to read this doc (Fixed)

Fast (1-2 minutes). The fixed content (item/version/purpose explanation, "every dependency must be justified in one line" rule) is universal. The Bootstrapper says:

> "How to read this doc is universal — every project follows the same format. Anything you want to change before we start filling?"

Usually no. Move to Visit 2.

#### Visit 2 — Runtime (Marker)

Medium-heavy (5-10 minutes). This is foundational — every other section depends on language.

**Step B (check conflicts):** "Language captured as <X> in BS-01. Confirm the specific version: <propose minimum> or different?"

**Step C (discuss):** push for specificity. Three sub-decisions per the marker:

> "Three things to pin for the Runtime section:
>
> 1. **Language version.** Specific: <e.g., 'Python 3.13.x with minimum 3.13', 'Node 20 LTS pinned to 20.18.x'>. What's the project's pin?
> 2. **Package manager.** What's the chosen tool, and what's forbidden? (e.g., `uv` only, no pip/poetry/pipenv).
> 3. **Lockfile.** Filename, and whether it's committed (it should be — CI fails if drift).
>
> If a non-default package manager is chosen (e.g., uv over pip+venv, pnpm over npm), this likely warrants an ADR. We'll come back to that at the end of this visit."

After capture, propose ADR if non-default package manager.

#### Visit 3 — Dependency organization (Marker)

Medium (5-10 minutes).

**Step B (check conflicts):** none usually — this is a new decision.

**Step C (discuss):** present the group structure as A/B options:

> "Two common shapes for dependency groups:
>
> **A)** Two groups: production + dev (dev includes test, lint, type-check, pre-commit).
> **B)** Three groups: production + dev + ci (ci-only tools like coverage uploaders).
>
> Recommendation: **A** unless you have a tool that's strictly CI-only. Two groups is simpler and matches most projects. Which?"

Then walk the install commands and the "what ships to prod" matrix. Capture as a table.

#### Visit 4 — Production dependencies (Marker) — HEAVIEST

Heavy (10-25 minutes). This section is the bulk of BS-04. The Bootstrapper walks each **applicable** category in order.

**Critical premise:** the categories that apply are determined by ARCHITECTURE.md §2 (Component view) from BS-03. The Bootstrapper does NOT assume every category applies. The conversation walks only categories that have a component to back them.

**Procedure:**

For each category that applies (based on §2 components):

1. **Announce the category.**

   > "Next category: **Core framework**. From BS-03 you named <X> as the web framework. Confirm and pin the version range?"

2. **Walk dependencies in the category one by one.** For each:

   > "We need <function> in this category. From the conversation so far, the candidate is <Y> version range <range>. Confirm?"

3. **Push for specificity per item.** Every dependency has version range + one-line purpose. The Bootstrapper does NOT accept "FastAPI, latest" — it wants "FastAPI, ^0.128" with rationale (e.g., "minimum needed for async lifespan").

4. **Propose ADRs as candidates emerge.** Examples:
   - "pgvector chosen over dedicated vector DB (Qdrant/Weaviate/Pinecone) — this feels ADR-worthy. Want me to draft after this section?"
   - "UUIDv7 as primary key (via `uuid-utils`) — the choice over UUIDv4 has real performance implications. ADR-worthy?"
   - "Anthropic as LLM provider — if OpenAI is a credible alternative, ADR-worthy. If you've already decided and the alternative wasn't really considered, no ADR needed."

5. **Skip categories that don't apply.** Explicitly note the skip:

   > "Background jobs category: your ARCHITECTURE.md §3 main flow has no async work in v1. Skipping this category. If async work emerges later, the project will add the category then."

6. **Capture aloud each category as it completes.**

   > "Capturing for Production dependencies — Core framework: <table content>. Data layer: <table content>. AI/LLM: <table content>. Skipping Background jobs and Object storage (not applicable in v1). I'll compose the full §4 in step D."

The PYTHON_REFERENCE_EXAMPLE comment in the template is used here **only as reference for the Bootstrapper to know what categories typically exist in AI-first projects**, NOT as the answer. Each dependency is a real decision — see Section 12.3 on PYTHON_REFERENCE_EXAMPLE handling.

#### Visit 5 — Development dependencies (Marker)

Medium (5-10 minutes). Same scaffold as Visit 4 but lighter — three sub-categories (Testing, Code quality, Dev tooling) and fewer libraries per sub-category typically.

The Bootstrapper walks each sub-category and pushes for specificity:

> "Testing sub-category: what's the test runner, async test support library (if applicable), coverage tool, integration test infrastructure (e.g., testcontainers), HTTP mock library, factory library?"

Same pattern for Code quality (linter, formatter, type checker, architecture rule enforcer) and Dev tooling (pre-commit, local infra orchestration).

#### Visit 6 — Explicitly NOT used (Marker)

Medium (5-10 minutes). The Bootstrapper synthesizes rejections captured during Visits 2-5 into a table.

**Procedure:**

1. **Replay captured rejections.** "During the walk we surfaced these rejections. Confirm or add to the list:"

   > "- `requirements.txt` rejected → use uv lockfile
   > - Flask rejected → use FastAPI
   > - bcrypt rejected → use Argon2id
   > - LangChain rejected → use direct Anthropic SDK
   > - <etc.>
   >
   > Anything else you considered and ruled out, in any category? Patterns from past projects you don't want here?"

2. **Probe if the section is too thin.** Per forcing function 8.6 below.

3. **Compose the table.** Standard format: `| Rejected | Reason | Use instead |`.

#### Visit 7 — Version bump policy (Fixed)

Fast (1-2 minutes). The fixed content (patch auto-merge, minor PR review, major requires ADR) is universal. The Bootstrapper says:

> "Version bump policy is universal: patch auto-merge on green CI, minor PR review, major requires ADR. Security patches auto-merge regardless. Anything different for your project?"

Usually no. Move to Visit 8.

#### Visit 8 — Runtime upgrade policy (Marker)

Medium (3-5 minutes). The Maintainer commits to:

- **The runtime version pinned** (from Visit 2)
- **The upgrade trigger** (date, EOL, or dependency requirement)
- **What an upgrade requires** (typically: ADR + container base image + CI matrix + dependency compat + benchmark)

> "Three commitments for runtime upgrade:
>
> 1. **Pin until when?** Common shapes: 'until <date> at minimum', 'until <version> EOL', 'until a critical dep requires upgrade'.
> 2. **What does the upgrade require?** Default: ADR covering container base image change, CI matrix change, dep compatibility check, performance benchmark delta. Adjust if you want a different bar.
> 3. **Who signs off?** Default: any maintainer can open the ADR; merge requires the ADR accepted."

Capture and compose.

#### Visit 9 — Where versions actually live (Marker)

Fast (2-3 minutes). The Maintainer commits to:

- **Manifest file** (e.g., `pyproject.toml`, `package.json`) — declared version ranges
- **Lockfile** (e.g., `uv.lock`, `pnpm-lock.yaml`) — resolved versions
- **Runtime pin** (e.g., `.python-version`, `.nvmrc`) — language version
- **Container base image** (e.g., `Dockerfile` line `FROM python:3.13-slim-bookworm`) — base image pin

> "Final section: pointers to where versions actually live in the repo. Standard four:
> - Manifest: <X>
> - Lockfile: <Y>
> - Runtime pin: <Z>
> - Container base image: <W>
>
> Confirm or adjust?"

Compose and apply. BS-04 walk is done.

---

**Total time for 7.2:** 20-45 minutes typical, with Visit 4 (Production dependencies) eating most of the budget.

### 7.3 — Verify outcomes, capture Decisions Captured for BS-05, bridge to BS-05 (10-15 minutes)

**Purpose:** Close BS-04 cleanly. Walk the exit criteria checklist. Recap what's been captured for BS-05 (the next step). Set the Maintainer up to start BS-05 or pause.

**Technique:**

1. **Walk the Exit criteria checklist (Section 3).** For each:
   - Runtime filled?
   - Dependency organization filled?
   - Production dependencies covered all applicable categories?
   - Development dependencies covered Testing + Code quality + Dev tooling?
   - Explicitly NOT used has at least the rejections from the walk?
   - Runtime upgrade policy filled?
   - Where versions live filled?
   - ADRs created for non-default tool choices?

   Surface any unmet outcome. Per Section 10.2 below, the gap is either covered now or deferred with structure.

2. **Recap Decisions Captured for BS-05.** BS-04 surfaces convention signals naturally — when the Maintainer says "ruff for linting + mypy for type checking", that implies code style preferences (line length, strict typing); when the Maintainer says "pytest + pytest-asyncio + testcontainers", that implies test strategy preferences (real DB in integration tests, async-first). Recap them:

   > "Decisions Captured during BS-04, to formalize in BS-05 (Conventions):
   >
   > - **Code style stack**: <linter>, <formatter>, <type checker>, <arch rule enforcer> — implies CODE_STYLE.md conventions
   > - **Test stack**: <test runner>, <integration infra>, <factory library> — implies TESTING.md structure
   > - **Auth library**: <library> — implies SECURITY.md auth section detail
   > - **Observability stack**: <OTel + logger + tracer> — implies SECURITY.md observability section and CODE_STYLE.md instrumentation conventions
   > - **ID strategy**: <UUIDv7 / etc.> — implies CODE_STYLE.md ID generation conventions
   >
   > These shape the BS-05 conversations on each convention doc."

3. **Preview BS-05.** Two sentences:

   > "BS-05 reviews the five convention docs against the stack we just pinned: CODE_STYLE.md, SECURITY.md, TESTING.md, LANGUAGE.md, GIT.md. Each doc has fixed conventions (universal) plus markers for project-specific extensions. By the end of BS-05, every coding rule the project is subject to has been confirmed or adjusted."

4. **Ask for readiness.**

   > "BS-05 typically takes 30-60 minutes. We continue in this same chat. Ready, or want a break?"

5. **If the Maintainer pauses:** TECH_STACK.md edits and any ADRs are already in the repo (applied during 7.2). Next session resumes by re-reading TECH_STACK.md and confirming nothing changed.

**Signal that BS-04 is done:** the recap is agreed, Decisions Captured for BS-05 are confirmed, the Maintainer either kicks off BS-05 or explicitly pauses. The Bootstrapper does NOT silently roll into BS-05.

---

## 8. Forcing functions specific to BS-04

The forcing functions from BOOTSTRAPPER section 10 and BS-03 Section 8 apply across all BS steps. The following are calibrated variations specifically for tech stack formalization.

### 8.1 — When the Maintainer says "let's use the latest" without pinning

**Symptom:** At any version pin moment, the Maintainer says "let's just use the latest" or "no version range, just pin at install time".

**Why it's a problem:** unpinned dependencies mean every fresh install can pull a different version. Lockfiles help, but the declared range in the manifest still matters — it controls what `uv add` / `npm install` resolves on a new machine.

**Response pattern:**

> "Two reasons to pin a range now:
>
> 1. **Reproducibility.** Fresh installs on contributor machines should match what's in CI. Lockfile pins the resolved version, manifest pins the acceptable range.
> 2. **Upgrade discipline.** Patch auto-merges, minor reviews, major requires ADR (per Version bump policy). All three need a declared range to compare against.
>
> Reasonable default: `^<current major>.<current minor>` for stable libraries, `~<current major>.<current minor>` for libraries with frequent breaking changes. What's the current version of <library>, and does ^ or ~ fit?"

If the Maintainer still resists, capture as a soft acceptance ("ranges TBD at install time") and flag in §10 Open questions as a follow-up.

### 8.2 — When the Maintainer wants to add a library "just in case"

**Symptom:** During Production dependencies walk, the Maintainer suggests adding a library not tied to a current component need ("we might want to add Sentry later — let's include it now").

**Why it's a problem:** speculative dependencies bloat the install footprint, the security audit surface, and the cognitive load of "what does this project actually use?". Future-need-only dependencies should be added when the need is concrete.

**Response pattern:**

> "Sentry isn't backed by a current ARCHITECTURE.md component or a Decision Captured. Two options:
>
> **A)** Add it to §10 Open questions in ARCHITECTURE.md as 'error tracking — defer to first error storm or month 3'.
> **B)** Add it now if you're sure it ships in v1. In that case, where does it appear in §2 Component view? If nowhere, then it's premature.
>
> Recommendation: **A**. Sentry is easy to add later; bloating the production stack today is harder to undo. OK?"

Default to A. Push back politely.

### 8.3 — When the Maintainer doesn't know which category a dependency belongs to

**Symptom:** During Production dependencies walk, the Maintainer says "is `httpx` a Core framework or a HTTP & resilience dep?".

**Why it's a problem:** category ambiguity isn't a real problem — it just slows the walk. Resolve by ownership.

**Response pattern:**

> "Quick rule: categories follow **what the library does most prominently in the project**, not what it CAN do. `httpx` here is HTTP & resilience because it's used for outbound HTTP calls to LLM/integrations, not because it's part of the framework. If you used it as a request client tightly coupled with FastAPI's testing, it could be Core framework — but here it's resilience-side."

Make the call, move on.

### 8.4 — When the production list and the dev list overlap

**Symptom:** A dependency seems to belong in both groups (e.g., `httpx` for prod outbound calls + dev tests).

**Why it's a problem:** typically it's just in production — the dev/test usage is "we also use the prod library in tests". No duplication needed.

**Response pattern:**

> "`httpx` goes in Production dependencies — the dev tests just use the same library. The dev group has `respx` for mocking `httpx`, but not `httpx` itself. Confirm?"

Move on. If the Maintainer genuinely needs a separate dev-only version (rare), capture it.

### 8.5 — When the Maintainer can't decide between two options

**Symptom:** At any dependency pin, the Maintainer can't choose between two credible options (e.g., "FastAPI or Litestar?", "PostgreSQL or MongoDB?").

**Why it's a problem:** undecided here blocks BS-05 (conventions depend on framework choice) and TSK-001 (the first execution prompt needs to know which lib to install).

**Response pattern:**

> "Both <A> and <B> are credible. Three angles to break the tie:
>
> 1. **Which one fits your team's experience?** If you've shipped with one before and not the other, lean toward the known one. Unknowns in v1 cost more.
> 2. **Which one fits the ecosystem signals captured in BS-01/BS-02/BS-03?** E.g., if Anthropic SDK was already captured and one option integrates better, that matters.
> 3. **Which one has the strongest "Explicitly NOT used" hit against the other?** If <B> has a known issue you'd put in the NOT used list, that's the answer.
>
> If still tied: pick <A>. Coin-flip is better than undecided. Future ADR can supersede if the choice surfaces problems."

Force a commitment. If genuinely tied, recommend an arbitrary tiebreaker and create an ADR explaining "chose A over B by tiebreaker; revisit at <event>".

### 8.6 — When Explicitly NOT used ends up too thin

**Symptom:** By Visit 6, the Bootstrapper has captured 1-2 rejections during the walk and the Maintainer says "that's it".

**Why it's a problem:** every real AI-first backend project rejects a lot. An empty or thin NOT used section in 2026 is suspicious — common rejections (LangChain, requirements.txt, bcrypt, sync ORM, etc.) usually surface naturally; absence means the Maintainer is hiding or hasn't considered alternatives.

**Response pattern:**

Probe systematically by category:

> "Let me probe by category. For each, tell me what you considered and ruled out (or 'didn't consider, was obvious'):
>
> - **Package manager**: did you consider pip + venv? poetry? pipenv?
> - **Web framework**: did you consider Flask? Django? Litestar?
> - **ORM**: did you consider Django ORM? Tortoise? raw SQL?
> - **LLM abstraction**: did you consider LangChain? LlamaIndex?
> - **Vector store**: did you consider Qdrant? Weaviate? Pinecone?
> - **Password hashing**: did you consider bcrypt? scrypt?
> - **HTTP client**: did you consider requests? aiohttp?
> - **Test runner**: did you consider unittest? nose2?
> - **Linter**: did you consider pylint? flake8? black + isort separately?
>
> Even 'didn't consider, was obvious' is worth capturing — it documents 'we didn't even look at X' as a position."

This usually surfaces 5-15 rejections. If after probing the section is still thin, accept as-is but note in §10 Open questions.

### 8.7 — When the Maintainer wants to skip the PYTHON_REFERENCE_EXAMPLE entirely

**Symptom:** The Maintainer sees the PYTHON_REFERENCE_EXAMPLE comment in TECH_STACK.md and says "let's just delete it, my project is TypeScript".

**Why it's a problem:** the comment block is template-author reference for the Bootstrapper's structure guidance. It's not the project's content. Deleting it isn't wrong, but the Maintainer doing it during BS-04 is premature — the Bootstrapper still references it internally to know which categories typically exist.

**Response pattern:**

> "The PYTHON_REFERENCE_EXAMPLE is reference material — it's inside an HTML comment, so it doesn't render in your docs anyway. I use it as a guide for which categories to walk (Core framework, Data layer, AI/LLM, etc.) — the **structure** is universal, the **packages** are Python-specific.
>
> Two options:
>
> **A)** Leave it in place. It's harmless (HTML comment), and a future maintainer might find it useful.
> **B)** Remove it at the very end of BS-04, after the walk is complete. By then I won't need it anymore.
>
> Recommendation: **B**. Clean docs are nice. I'll prompt you to remove it at the end."

If the Maintainer chooses A, leave it. If B, capture for the end of BS-04 and remove during 7.3.

### 8.8 — When a tool choice has obvious ADR signal but the Maintainer waves it off

**Symptom:** A non-default choice surfaces (e.g., uv over pip+venv, pgvector over Qdrant, UUIDv7 over UUIDv4), and the Bootstrapper proposes an ADR. The Maintainer says "nah, it's obvious".

**Why it's a problem:** "obvious" today is "questionable" in 18 months when a new contributor joins. The ADR is cheap insurance.

**Response pattern:**

> "The choice may feel obvious to you now, but consider:
>
> - A new contributor in 18 months won't have your context. They'll see uv and wonder 'why not pip?'.
> - The ADR is 15 minutes to write. It documents your reasoning so the question doesn't come back.
> - If the choice is genuinely uncontroversial (e.g., 'git for version control'), no ADR. But uv-over-pip in 2026 is non-trivial enough to warrant 15 minutes.
>
> My recommendation stands: write the ADR. Want to draft it now (after this section) or batch the ADR drafting at the end?"

If the Maintainer still refuses, accept and note in §10 Open questions as a future-ADR candidate. Don't force.

### 8.9 — When the Maintainer wants to add experimental/pre-release dependencies

**Symptom:** During the walk, the Maintainer wants to include a `0.x` or pre-release library (e.g., `some-lib >=0.1.0,<0.2.0`).

**Why it's a problem:** pre-1.0 libraries have unstable APIs. They're fine if the team accepts the maintenance cost, but the cost should be explicit.

**Response pattern:**

> "<library> is at version 0.x — minor versions can introduce breaking changes. Two questions:
>
> 1. **Is it critical, or could you defer until 1.0?** If deferrable, defer.
> 2. **If critical now, do you accept the 'every minor is a major' cost?** That means minor bumps require PR review (not auto-merge), possibly an ADR per bump if it breaks API.
>
> Recommendation: include it but pin tightly (e.g., `~0.1.0` not `^0.1.0`) and add a note in the Notes column. Want to do that?"

Pin tight, document the cost, move on.

### 8.10 — When the deps walk exceeds the time budget

**Symptom:** Visit 4 (Production dependencies) is at 30+ minutes and there are still categories to cover.

**Why it's a problem:** BS-04 has a 30-60 minute target. Going over means BS-05 will start tired or skipped.

**Response pattern:**

> "We've been on Production dependencies for 30 minutes and have <N> categories left. Two options:
>
> **A)** Push through the remaining categories now, accepting BS-04 will be 70-90 minutes total. Good if you have energy.
> **B)** Pause here, ship a partial Production dependencies (categories covered so far), defer remaining categories to a follow-up BS-04 session. Mark deferred categories in §10 Open questions of ARCHITECTURE.md.
>
> Recommendation: **A** if you're still sharp, **B** if you're losing focus. Quality of decisions matters more than finishing in one session."

Maintainer chooses. Either is acceptable — BS-04 can be split across two sessions if needed.

---

## 9. Pre-fill rules (section-by-section)

Same fundamental rules as BS-03 Section 9 (section-by-section, capture-aloud during discussion, `~~~markdown` fence for final section content, BOOTSTRAP marker replacement). What follows is BS-04-specific behavior.

### 9.1 — Capture-aloud format for tables

Most BS-04 sections produce **tables** (Runtime, Production dependencies, Development dependencies, Explicitly NOT used). The capture-aloud format for tables is:

> "Capturing for §<N>, <sub-category>:
>
> | Item | Version | Purpose |
> |---|---|---|
> | <pkg> | <range> | <one-line> |
> | <pkg> | <range> | <one-line> |
>
> This goes into the final block for this section."

Tables are easier to validate at capture time than narrative bullets — the Maintainer sees the exact row that goes into the file.

### 9.2 — The PYTHON_REFERENCE_EXAMPLE handling

The template ships TECH_STACK.md with a substantial HTML comment block tagged `PYTHON_REFERENCE_EXAMPLE`. This block contains a complete reference Python stack (Python 3.13 + uv + FastAPI + SQLAlchemy 2 + Anthropic + ...) plus the "Explicitly NOT used" Python-specific list.

**Critical rule:** the PYTHON_REFERENCE_EXAMPLE is **always reference material, never the project's final content**. This is different from how ARCHITECTURE.md and STRUCTURE.md handle their reference examples (which can become final content if the stack is Python).

The Bootstrapper:

- **Uses the example internally** as a guide for which categories typically exist in AI-first projects (Core framework, Data layer, AI/LLM, etc.)
- **Uses the example internally** as a guide for the format of each table row (item / version / purpose)
- **Does NOT copy-paste from the example** into the Maintainer's TECH_STACK.md, even if the project is Python
- **Asks the Maintainer for every dependency** as a genuine decision, with the example as background knowledge of "what would a typical AI-first Python project use here?" — useful for the Bootstrapper's recommendations, not for the final content

**Why this rule:** even when two Python AI-first projects look similar, the specific versions, the specific extension choices, and the specific "Explicitly NOT used" list reflect the Maintainer's experience and the project's specific constraints. The Bootstrapper avoids assuming the reference is the answer.

**The comment block itself:**

At the end of BS-04, the Bootstrapper offers to remove the PYTHON_REFERENCE_EXAMPLE comment block from TECH_STACK.md. Two options:

- **Leave in place** if the Maintainer is fine with reference material persisting in the file (it's an HTML comment — invisible in rendered output, but visible to anyone reading the raw file)
- **Remove at end of BS-04** if the Maintainer wants clean docs

Default recommendation: **remove at end**. Bootstrap is done; reference material isn't needed any more. But this is the Maintainer's call.

### 9.3 — When a category is skipped

If the Bootstrapper skips a category during Visit 4 (Production dependencies) — e.g., the project has no Background jobs because there's no async work in v1 — the section's final block **does not include a table for that category at all**. The category simply isn't there.

The Bootstrapper notes the skip in chat (not in the file):

> "Background jobs category: skipped, no async work in v1. The section's final block won't have a Background jobs table."

This keeps TECH_STACK.md clean — empty tables aren't valuable.

### 9.4 — ADR creation during BS-04

Same flow as BS-03 §9.6 (ADR drafted in chat, applied as new file in `docs/engineering/adr/`, cross-references added to TECH_STACK.md).

BS-04 typically surfaces more ADR candidates than BS-03 because tool choices have more "future contributor might question" signal. Common BS-04 ADRs:

- Package manager choice when non-default for the ecosystem (uv vs pip, pnpm vs npm)
- Database extension or pattern choice (pgvector vs dedicated vector DB, JSONB-heavy vs normalized)
- LLM provider choice when alternatives are credible (Anthropic vs OpenAI vs both)
- Async vs sync codebase commitment
- ID strategy (UUIDv7 vs UUIDv4 vs BIGSERIAL)
- Password hashing algorithm (Argon2id vs bcrypt vs scrypt)
- Observability stack composition (OTel + structlog + Tempo as one decision)

Important: **one decision = one ADR, even if it covers multiple libraries**. The observability stack ADR covers OTel + structlog + Tempo as a single architectural decision — not three separate ADRs.

### 9.5 — Decisions Captured for BS-05

BS-04 surfaces convention signals naturally. The Bootstrapper holds these mentally for the Bridge to BS-05 (sub-block 7.3):

- **Code style stack** → CODE_STYLE.md will be calibrated to the linter/formatter/type-checker chosen
- **Test stack** → TESTING.md will be calibrated to the test runner + integration infra + factory library chosen
- **Auth library** → SECURITY.md auth section will reference the specific library
- **Observability stack** → SECURITY.md observability + CODE_STYLE.md instrumentation conventions
- **ID strategy** → CODE_STYLE.md ID generation conventions

Same pattern as previous Decisions Captured handling.

---

## 10. Required outcomes (checklist for closing BS-04)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step. The checklist mirrors Section 3 (Exit criteria) but reformatted for the closing conversation.

### 10.1 — Outcomes that must be true to close BS-04

#### TECH_STACK.md

- [ ] Runtime section — language version, package manager, lockfile filled with specifics
- [ ] Dependency organization section — groups, install commands, prod-image membership filled
- [ ] Production dependencies — every applicable category filled with version-pinned tables
- [ ] Development dependencies — Testing + Code quality + Dev tooling tables filled
- [ ] Explicitly NOT used — at least the rejections from the walk captured
- [ ] Version bump policy — reviewed (usually no changes from default)
- [ ] Runtime upgrade policy — pin until + upgrade trigger + upgrade requirements filled
- [ ] Where versions actually live — manifest + lockfile + runtime pin + container base image filled

#### ADRs (if any were created)

- [ ] ADRs for non-default tool choices created in `docs/engineering/adr/`
- [ ] Cross-references from TECH_STACK.md to relevant ADRs added inline

#### PYTHON_REFERENCE_EXAMPLE comment block

- [ ] Decision made on whether to leave or remove the comment block at end of BS-04

#### Anchor questions answered

- [ ] **Anchor 1 (Runtime exact pin)** — captured in Runtime section
- [ ] **Anchor 2 (Dependency organization)** — captured in Dependency organization section
- [ ] **Anchor 3 (Production stack by category)** — captured in Production dependencies section
- [ ] **Anchor 4 (Explicit rejections)** — captured in Explicitly NOT used section

#### Decisions Captured for BS-05

- [ ] BS-04 contributed convention signals beyond BS-01/BS-02/BS-03. The Bootstrapper holds these in mental state for BS-05.

### 10.2 — When a required outcome is unmet

Same options as BS-03:

**Option A — Cover it now.** Pick up the conversation thread on the gap.

**Option B — Defer it with structure.** Document the gap explicitly:
- Missing dependency category content → entry in §10 Open questions of ARCHITECTURE.md (referencing TECH_STACK.md gap)
- Undecided tie between two options → ADR with tie-broken default + "revisit at <event>"
- Pre-release dependency without firm pin → §10 Open questions entry tagging it for review at 1.0 release

The Bootstrapper recommends Option A when the gap blocks BS-05 (e.g., test stack not chosen — TESTING.md can't be calibrated). Option B when the gap is genuinely premature.

### 10.3 — What's explicitly NOT required to close BS-04

To prevent perfectionism:

- Code style decisions, naming patterns, async patterns — BS-05
- Test pyramid shape, fake adapter patterns — BS-05
- Security implementation specifics (token lifetimes, rate limit numbers, JWT algorithm) — BS-05
- Schema design for any entity — future `DATA_MODEL.md`
- Endpoint design — future `API_CONVENTIONS.md`
- Prompt files or model-per-task strategy — future `AI_INTEGRATION.md`
- The actual manifest files (`pyproject.toml`, `package.json`, etc.) — TSK-001
- Lockfile generation — TSK-001 (`uv lock`, `npm install`, etc.)
- Container base image Dockerfile — TSK-001 or near-deploy phase

The Bootstrapper resists pulling BS-05+ content into BS-04. Tool pinning is BS-04's scope; everything more concrete lives downstream.

---

## 11. Bridge to BS-05

When the Required outcomes checklist is complete (or consciously deferred with notes), the Bootstrapper transitions to BS-05 with the following sequence:

### 11.1 — Recap

Three or four sentences:

> "BS-04 pinned the tech stack. `TECH_STACK.md` has the runtime (<X> <version>), dependency organization (<N> groups), production deps (<N> applicable categories), dev deps (Testing + Code quality + Dev tooling). <N> ADRs created for non-default choices: <list>. Explicitly NOT used captured <M> rejections. Runtime upgrade pinned until <date/event>. Open questions remain on <list 1-2 main ones>."

### 11.2 — Confirm Decisions Captured for BS-05

Recap convention-relevant decisions surfaced across BS-01/BS-02/BS-03/BS-04:

> "Convention signals carried into BS-05:
>
> - **Code style stack**: <linter> + <formatter> + <type checker> + <arch enforcer>
> - **Test stack**: <runner> + <async support> + <integration infra> + <factory>
> - **Auth library**: <library>
> - **Observability stack**: <OTel + logger + tracer>
> - **ID strategy**: <UUIDv7 / etc.>
> - **Language policy signals** (from BS-01/BS-02): <user-facing language, code/docs language>
> - **Git workflow signals**: <commit format, branch naming if any was captured>
>
> In BS-05 we calibrate each of the five convention docs (CODE_STYLE, SECURITY, TESTING, LANGUAGE, GIT) against the stack we just pinned."

### 11.3 — Preview BS-05

Two sentences:

> "BS-05 reviews CODE_STYLE.md, SECURITY.md, TESTING.md, LANGUAGE.md, GIT.md against the stack. Each doc has fixed conventions (universal) plus markers for project-specific extensions. By the end of BS-05, every coding rule the project is subject to has been confirmed or adjusted."

### 11.4 — Commit BS-04 before continuing or pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

Before moving to BS-05 (or pausing), the Maintainer commits the work BS-04 produced:

```
git add docs/engineering/TECH_STACK.md \
        docs/engineering/adr/NNNN-<slug>.md
        # ... one line per ADR created during BS-04
        # also include any ARCHITECTURE.md cross-reference updates
        # made during BS-04 ripple effects

git commit -S -m "docs(bootstrap): complete BS-04 — tools"
git push origin main
```

ADR lines and the ARCHITECTURE.md line are included only when those files were actually touched during BS-04. The Bootstrapper presents the exact list based on the session.

After the commit lands, proceed to 11.5 (continue) or 11.6 (pause).

### 11.5 — Continue in the same chat

> "BS-05 typically takes 30-60 minutes. We continue in this same chat — the stack context from BS-04 carries forward. Ready to start BS-05, or want a break?"

If the Maintainer pauses, the same recovery pattern applies — docs are already in the repo, resumption re-reads TECH_STACK.md to refresh state.

### 11.6 — If the Maintainer wants to stop entirely

Same handling as BS-01/BS-02/BS-03 (everything is saved, stay in same chat session for resumption, "resuming bootstrap" as the resumption phrase).

---

## 12. Notes for the Bootstrapper LLM running BS-04

These are operational reminders specific to BS-04, on top of universal BOOTSTRAPPER guidance and BS-03 Section 12 notes.

### 12.1 — Specificity is the BS-04 forcing function

Where BS-03 was about structural shape (can be abstract in places), BS-04 is about **exact pins**. Every dependency has a version range. Every install command is spelled out. Every category that doesn't apply is consciously skipped, not silently dropped.

The Bootstrapper pushes for specificity throughout. "We'll use the latest" is not an acceptable answer. "FastAPI ^0.128" is. The forcing functions in Section 8 exist specifically to drive specificity.

### 12.2 — Decisions Captured is the BS-04 superpower

By the time BS-04 starts, the Bootstrapper holds the largest accumulated Decisions Captured register of any BS step. Every signal from BS-01, BS-02, BS-03 is potentially relevant: language, channel, database family, web framework, async work, LLM provider, observability stack, object storage.

Open BS-04 with the full recap (sub-block 7.1 step 2). Confirm each signal still holds. Use them as starting positions for each section walk — the Maintainer should not re-decide things they already decided.

### 12.3 — The PYTHON_REFERENCE_EXAMPLE is reference, not answer

This is the most important operational rule of BS-04, and it differs from BS-03's handling of its own reference examples.

**Rule:** the PYTHON_REFERENCE_EXAMPLE comment in TECH_STACK.md is **always reference material, never the project's final content** — even when the project is Python.

**Why:** even similar Python AI-first projects make different choices. The reference helps the Bootstrapper know what categories typically exist and what a typical dependency row looks like. It does NOT give the Bootstrapper the answer to "what should this project use here?".

**In practice:**

- Use the example to know which categories to walk in Visit 4
- Use the example to know what a well-formed table row looks like
- Do NOT copy-paste from the example into the Maintainer's TECH_STACK.md
- Ask the Maintainer for every dependency — even when you're confident the answer matches the example

This rule extends BS-03 §12.9 with a stricter constraint for the heavier reference block in TECH_STACK.md.

### 12.4 — ADR detection is continuous

The Bootstrapper does not batch ADR creation at the end of BS-04. ADR candidates emerge throughout — as each non-default choice is captured, the Bootstrapper evaluates and proposes immediately.

Two timing options when an ADR candidate emerges:

- **Draft immediately after the section it emerged in.** Useful when the ADR is small or the Maintainer wants to get it out of the way.
- **Batch at end of BS-04.** Useful when multiple ADRs emerged and the Maintainer prefers writing them in one pass.

The Maintainer chooses. Default recommendation: batch at end if there are 3+ candidates, draft immediately if 1-2.

### 12.5 — Skipping categories is fine; silent dropping is not

In Visit 4 (Production dependencies), some categories will not apply to the project (e.g., no Background jobs in v1, no Object storage if no files). The Bootstrapper:

- **Confirms the skip explicitly with the Maintainer:** "Background jobs category: your §2 Component view has no worker. Confirm skip?"
- **Notes the skip in chat (not in the file):** the skipped category simply isn't in the final block
- **Does NOT add a placeholder** like "TBD" or "Not applicable" in TECH_STACK.md — the table is just absent

If a skipped category later becomes relevant, the project adds it via PR (no need for a planning event for a simple dependency addition).

### 12.6 — Cross-references to ARCHITECTURE.md may need updates

When BS-04 pins a specific tool (e.g., "Anthropic SDK ^0.97"), the abstract reference in ARCHITECTURE.md §7 Integrations (which may say "LLM provider adapter" generically) becomes more concrete. The Bootstrapper:

- Proposes updating ARCHITECTURE.md to reflect the concrete tool: "ARCHITECTURE.md §7 Integrations row says 'LLM provider'. Now that we've pinned Anthropic, want to update to 'Anthropic' specifically?"
- Applies the update inline (the Maintainer's edit to ARCHITECTURE.md happens during BS-04, not deferred)

This is the BS-04 ripple effect into BS-03 outputs. It's expected — concrete tools refine abstract references.

### 12.7 — Resist completionism on Production dependencies

Visit 4 is the longest part of BS-04. The Bootstrapper resists letting it become a "every possible library" exercise. The bar is:

- **What does v1 need to ship?** That's the dep list.
- **What does v1 not need?** Defer.

If the Maintainer wants to discuss libraries for v2+ features, capture as §10 Open questions of ARCHITECTURE.md and move on.

### 12.8 — Pacing

BS-04 typically takes 30-60 minutes:

- **First 10-15 minutes**: opening, Decisions Captured recap (largest of any step), pattern reminder (7.1)
- **Middle 20-45 minutes**: section walk (7.2) — 9 visits, with Visit 4 eating most of the budget
- **Final 10-15 minutes**: outcomes verification, Decisions Captured for BS-05, bridge (7.3)

If the conversation runs long (60+ minutes), check energy. Same pattern as BS-03 — offer pause, offer split BS-04 across two sessions, never force exhausted Maintainers through full depth.

### 12.9 — When BS-04 stack signals contradict BS-03 architectural commitments

**Symptom:** During BS-04 Visit 4, the Maintainer says "actually let's use a sync ORM" — but BS-03 §4 committed to "async-only codebase".

**Why it's a problem:** BS-04 stack choices can invalidate BS-03 architectural commitments. The Bootstrapper must surface this immediately.

**Response pattern:**

> "Wait — BS-03 §4 said async-only. A sync ORM contradicts that. Two options:
>
> **A)** Revise BS-03 §4 to allow sync (or async-with-sync-carve-out). This is a real architectural change — needs to ripple to ARCHITECTURE.md and possibly an ADR.
> **B)** Keep BS-03 async-only and choose an async ORM here.
>
> Which fits?"

Resolve before proceeding. Do not silently let BS-04 break BS-03 commitments.

### 12.10 — TECH_STACK.md template BOOTSTRAP markers reference old step names

Same pattern as BS-03 §12.10: the BOOTSTRAP markers in TECH_STACK.md may reference `04-tools.md` in their placeholder descriptions. This is template-maintenance pendency, not BS-04's concern.

The Bootstrapper:

- Ignores the stale step name references when interpreting marker intent
- Replaces the marker block entirely during application
- Does not "fix" old references as a side mission
- Flags as known issue if the Maintainer notices

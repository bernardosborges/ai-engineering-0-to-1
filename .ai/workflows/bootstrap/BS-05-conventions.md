# BS-05 — Conventions

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-05 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the conventions review conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern), this file for the specific content of BS-05, and the output of BS-01 + BS-02 + BS-03 + BS-04 (`docs/PRODUCT.md` + `docs/product/pd-*.md` + `docs/product/ft-*.md` + `docs/engineering/ARCHITECTURE.md` + `docs/engineering/STRUCTURE.md` + `docs/engineering/TECH_STACK.md`) as input to this step.

---

## 1. Goal

Review the five convention documents against the product (BS-01/BS-02), the architecture (BS-03), and the pinned tech stack (BS-04). Each document ships with opinionated defaults that solve specific problems (consistency, security posture, test discipline, branching strategy, language policy). BS-05 walks each document with the Maintainer to:

- Confirm the fixed conventions still fit the project
- Adjust the fixed conventions where they conflict with the product or stack
- Fill the BOOTSTRAP markers that capture project-specific extensions (class naming patterns, ID strategy, migration tool specifics, data tier examples, rate limit values, commit scopes, PR title format, user-facing language, domain terms)
- Create ADRs for non-obvious convention choices when relevant

The goal is **not** to redesign conventions from scratch. The defaults represent battle-tested choices for AI-assisted backend development. The Maintainer engages with each convention, accepts or adjusts, and the result is five docs that the project (and any Claude Code session reading them) can rely on.

By the end of BS-05, a competent engineer joining the project could read the convention docs and:

- Know the naming pattern for any new class category they create
- Know how IDs are generated, where they live in code, how they cross boundaries
- Know how migrations are written and what review pitfalls to watch
- Know what data tier each new piece of information falls into
- Know the rate limit defaults and how to add per-endpoint exceptions
- Know what test markers exist and which command runs which subset
- Know the commit format, branch naming, PR size cap, and merge strategy
- Know the language policy across code, docs, user-facing surfaces, and LLM prompts

---

## 2. What this step produces

### Documents updated

The five convention docs, in the order BS-05 walks them:

- **`docs/engineering/LANGUAGE.md`** — fixed rule of thumb confirmed; BOOTSTRAP markers filled with the project's user-facing language policy and domain terms that stay in their original language.

- **`docs/engineering/CODE_STYLE.md`** — foundations (formatting, type checking, type annotations, imports, file organization) confirmed against the stack; BOOTSTRAP markers filled for class naming table, class construct choice, ID strategy, migration tool. Fixed conventions adjusted where the stack changes the natural idiom (e.g., async patterns reflect the actual async library, logging reflects the actual logger).

- **`docs/engineering/TESTING.md`** — test pyramid percentages and coverage targets confirmed; BOOTSTRAP markers filled for test markers and running tests commands. Fixed conventions (naming pattern, factories vs fixtures, LLM-in-tests rules, authorization dual enforcement, property-based scope) reviewed against the product and stack.

- **`docs/engineering/SECURITY.md`** — fixed conventions in 16 sections reviewed; BOOTSTRAP markers filled for data tier examples specific to the project, rate limit values, file upload types, incident notification timeline, PR checklist extensions. Architectural commitments from BS-03 §5 expanded with implementation-level specifics.

- **`docs/engineering/GIT.md`** — branching strategy (trunk-based), Conventional Commits format, signed commits, PR rules, merge strategy reviewed; BOOTSTRAP markers filled for commit scopes specific to the project and PR title convention.

### Documents potentially created

- **`docs/engineering/adr/NNNN-<slug>.md`** — ADRs for non-default convention choices. BS-05 typically produces 0-2 ADRs because most convention decisions are universal patterns. Candidates emerge from the conversation and the Maintainer decides.

  Typical BS-05 ADR candidates:
  - Branching strategy if non-default (e.g., GitFlow instead of trunk-based)
  - Coverage threshold if departing from the default 90%/70%
  - JWT algorithm if departing from RS256
  - Migration tool if the choice was significant (often handled in BS-04 ADRs instead)
  - Test pyramid proportions if materially different from 70/25/5
  - Docstring style choice when the language has competing conventions

  The rule remains: **if a future contributor without context might question the choice, write an ADR**. ADRs use `docs/engineering/adr/TEMPLATE.md` as a floor.

### What is NOT produced in BS-05

- INDEX.md updates — BS-06 (Docs map) handles INDEX.md
- ROADMAP.md updates — BS-07 (Roadmap) handles roadmap structure
- Application code — bootstrap doesn't write code
- The actual linter/formatter/type-checker config files (`pyproject.toml [tool.ruff]`, `.prettierrc`, `mypy.ini`, etc.) — those are written during TSK-001 based on CODE_STYLE.md
- The actual pre-commit hooks config — TSK-001
- The actual CI workflow definitions — emerge during early phase 1 tasks
- The PR template at `.github/PULL_REQUEST_TEMPLATE.md` — it ships with the template; BS-05 may suggest adjustments but the file already exists
- Future docs (`DATA_MODEL.md`, `API_CONVENTIONS.md`, `AI_INTEGRATION.md`, `OBSERVABILITY.md`, `DEPLOYMENT.md`, runbooks) — created when needed, not in bootstrap

### Critical premise — BS-05 formalizes the rules the project will be subject to

After BS-05, every rule that constrains code, tests, security posture, branching, commits, and language is on paper. The next step (BS-06) maps these docs into INDEX.md so contributors can find them. The step after (BS-07) plans the phases that will be written under these rules.

If a convention is unclear or contested at the end of BS-05, it will surface as friction every time it applies — in every PR, every test, every commit. The Bootstrapper pushes for resolution at BS-05 time, not "we'll figure it out later".

---

## 3. Exit criteria

BS-05 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

#### LANGUAGE.md

- [ ] User-facing language section filled (monolingual confirmation, or bilingual/multilingual with locale codes and translation surfaces)
- [ ] Domain terms section filled (list of terms that stay in their original language, with one-line reason each — or explicit "none" with justification)

#### CODE_STYLE.md

- [ ] Class naming table filled with the project's chosen patterns for each class category
- [ ] Class construct choice filled — which language construct maps to which kind of object
- [ ] ID strategy filled — type, generation location, serialization, helper convention
- [ ] Migration tool section filled — tool name, config location, generation command, apply command, naming convention
- [ ] Foundations sections (formatting, type checking, type annotations, imports, file organization) reviewed against TECH_STACK.md; tool names from BS-04 confirmed
- [ ] Idioms sections (control flow, strings, null/empty, dates/times, paths, numbers, collections, async) reviewed; stack-specific idioms confirmed
- [ ] Infrastructure idioms (logging, environment/config, dependency injection) reviewed against TECH_STACK.md choices
- [ ] Database and migrations section reviewed in detail — this is the longest sub-section of CODE_STYLE.md

#### TESTING.md

- [ ] Test pyramid percentages confirmed or adjusted with rationale
- [ ] Coverage targets per layer confirmed or adjusted
- [ ] Test markers section filled with project-specific markers and commands
- [ ] Running tests section filled with concrete commands per context (local, pre-push, full, with coverage, specific file)
- [ ] Directory layout confirmed against STRUCTURE.md
- [ ] LLM-in-tests rules reviewed (fake in unit, real-cheap in nightly integration, evals separate)
- [ ] Authorization dual enforcement reviewed (PR checklist + meta-test)
- [ ] Factories vs fixtures rule reviewed
- [ ] Property-based testing scope reviewed

#### SECURITY.md

- [ ] Threat model summary reviewed against the product
- [ ] Data tier examples filled with project-specific data types per tier
- [ ] Rate limit values filled with concrete numbers per endpoint category
- [ ] Data map filled with the project's personal/sensitive data categories, storage, retention
- [ ] Applicable regulations identified (GDPR, LGPD, CCPA, HIPAA, etc., or explicit "pre-launch")
- [ ] Audit events extended with project-specific high-stakes events
- [ ] File upload types filled (allowed types, size limits, virus scan tool) — or explicit "no file uploads in v1"
- [ ] Incident notification filled with regulation-specific timelines and authorities
- [ ] PR checklist extensions filled with project-specific items
- [ ] Authentication section reviewed (token lifetimes, password policy, lockout policy)
- [ ] Authorization section reviewed (use case layer enforcement, 404-not-403 rule)
- [ ] Input validation, output exposure, transport/headers, error handling sections reviewed
- [ ] Dependencies and supply chain section reviewed (lockfile, vuln scan, auto-PRs)
- [ ] AI/LLM security section reviewed (prompt injection, system prompts location, tool calling safety, PII handling, cost/quota)
- [ ] Secrets management section reviewed (storage tiers, rotation cadence, leak procedure)

#### GIT.md

- [ ] Commit scopes section filled with project-specific scope list (domain areas + infrastructure + cross-cutting)
- [ ] PR title convention filled with the ID format and examples
- [ ] Branching strategy reviewed (trunk-based, branch lifetime, branch naming)
- [ ] Conventional Commits format reviewed (types, rules, examples, anti-patterns)
- [ ] Signed commits requirement confirmed
- [ ] PR rules reviewed (size cap, review requirement, CI gates)
- [ ] Merge strategy reviewed (squash merge, linear history)
- [ ] Main branch protection rules confirmed
- [ ] Per-PR workflow reviewed (no `git add .`, two levels of granularity, ROADMAP updates at end)

### Soft requirements (worth pushing for but not blockers)

- [ ] ADRs created for non-default convention choices where a future contributor would benefit from the rationale
- [ ] Cross-references between convention docs updated (e.g., SECURITY.md auth section may reference CODE_STYLE.md's domain exception patterns)
- [ ] Cross-references from ARCHITECTURE.md to CODE_STYLE.md added where BS-03 cross-cutting decisions get implementation-level detail in BS-05

### What does NOT block exit

- Linter/formatter/type-checker config file content — TSK-001
- Pre-commit hook config — TSK-001
- CI workflow YAML — Phase 1 tasks
- Production runbooks — emerge near production deploy
- Specific schema design, endpoint design, prompt strategy — future docs

The Bootstrapper does not advance to BS-06 silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before moving on, SECURITY.md data tier examples is empty. We discussed several data types during the conversation (e.g., <X>, <Y>) that need to be placed in tiers. Cover now or capture deferral?"

---

## 4. Anchor questions

These are the four questions that **must be answered with confidence** before BS-05 closes. They are not asked in order — they emerge through the doc-by-doc walk (see Section 7 — Conversation flow). The Bootstrapper keeps mental track of which ones are still open, and surfaces any that remain unanswered before declaring the step complete.

### Anchor 1 — How are class categories named, and which language construct does each use?

This becomes the **Class naming table** and **Class construct choice** sections in CODE_STYLE.md. The Maintainer must commit to a complete map covering:

- Entities, value objects, use cases, ports (domain and application), adapters
- DTOs, HTTP request/response schemas, ORM models, mappers
- Domain services, domain events
- Any project-specific category not on the universal list

For each category, two pieces of information:

1. **Naming pattern** — `PascalCase`, `NounRepository`, `VerbNounUseCase`, etc.
2. **Language construct** — `@dataclass(frozen=True, slots=True)`, Pydantic `BaseModel`, SQLAlchemy `DeclarativeBase`, etc.

The forcing function: **a contributor adding a new class should be able to look at the table and know exactly how to name it and which construct to use**. If the table leaves room for interpretation, the codebase will drift.

### Anchor 2 — What test markers exist, and what command runs each context?

This becomes the **Test markers** and **Running tests** sections in TESTING.md. The Maintainer must commit to:

- At least one marker per pyramid layer (unit, integration, e2e)
- A marker for tests that call real external APIs (LLM, payment, etc.) — gated to nightly
- A marker for slow tests (excluded from local loop)
- Concrete commands for: local fast loop, pre-push, full suite, with coverage, specific file or test

The forcing function: **a contributor running the test suite at any moment must know exactly which command to use for their current context**. "Just run pytest" is not enough.

### Anchor 3 — What data tier does each kind of information fall into, and what are the rate limits?

This becomes the **Data tier examples** and **Rate limit values** sections in SECURITY.md. The Maintainer must commit to:

- For each tier (Critical, Sensitive, Internal, Public), project-specific examples of data types
- For each endpoint category (authentication, LLM-backed, general authenticated, public), concrete rate limit numbers in requests per time window

The forcing function: **classification of data and limits on traffic must be settled before code is written**, because every endpoint, every log statement, every response schema will reference these tier rules.

### Anchor 4 — What is the project's language policy across all surfaces?

This becomes the **User-facing language** and **Domain terms** sections in LANGUAGE.md. The Maintainer must commit to:

- Whether the project is monolingual (English-only) or bilingual/multilingual
- If bilingual: target locale codes, where translations live, what surfaces are translated
- Domain terms that stay in their original language (with one-line reason each)

The forcing function: **language policy must be settled before any user-facing string is written**, because changing the policy mid-project requires reworking every translated surface.

---

## 5. How BS-05 relates to BS-04

BS-05 reuses the **doc-by-doc persistence pattern** decided for conventions (different from BS-03 and BS-04's section-by-section). The Maintainer:

- Discusses one doc fully (all sections of that doc), with capture-aloud during the discussion
- Receives a consolidated final block for the doc at the end of that doc's walk
- Applies the consolidated block before moving to the next doc

This is a hybrid between BS-02 (single consolidation per file) and BS-03/BS-04 (section-by-section). It exists because:

- BS-05 touches 5 docs, each with multiple sections. Section-by-section would mean ~20+ apply moments — too granular for the Maintainer to track without fatigue.
- Each doc is conceptually independent (LANGUAGE has nothing to do with GIT). The Maintainer can absorb one doc, apply it, move on without holding context across docs.
- Some docs (CODE_STYLE, SECURITY) are heavy. Consolidating per-doc means the Maintainer sees the full doc state before applying, which is easier than chasing 5+ partial edits in a long file.

### What carries over from BS-03/BS-04

- **Capture-aloud pattern** within each doc's discussion. Same format as before: "Capturing for `<doc>.md` §<section>: <content>. This goes into the final block for this doc."
- **`~~~markdown` fenced blocks** for the final consolidated content of each doc
- **BOOTSTRAP marker replacement** rule: marker block (START/END comments + placeholder) is replaced by actual content
- **Decisions Captured** mental register continues to accumulate (any signals for BS-06 and BS-07 that emerge — additional docs needed, INDEX.md cross-references, roadmap implications)

### What differs from BS-03/BS-04

- **Doc-by-doc apply instead of section-by-section.** One consolidation per doc, not per section.
- **5 docs to walk instead of 1-2 files.** The walk is longer in total but each unit is independently completable.
- **Heavy reliance on Decisions Captured from BS-04.** BS-04 pinned the stack; BS-05 conventions reflect that stack. Almost every section of CODE_STYLE.md, TESTING.md, SECURITY.md depends on what was decided in BS-04.
- **Less likely to spawn ADRs.** BS-04 produces variable ADRs based on tool choices. BS-05 conventions are more standardized — ADRs are rare (0-2 typical).
- **Fixed content reviewed but rarely changed.** Defaults usually stand. The Maintainer engages mostly with markers.

### What the Maintainer needs at the keyboard

Same as BS-03/BS-04:

- Active editor on the convention doc currently being walked
- Active editor on `docs/engineering/adr/` (ready to create new files if ADRs emerge)
- Possibly active editor on ARCHITECTURE.md (for cross-reference updates if convention discussions clarify architectural decisions)

If the Maintainer can't apply edits during the conversation, they should pause BS-05 and resume when they can. Doc-by-doc apply works only when the Maintainer applies each doc's consolidated block before the next doc starts.

---

## 6. Coverage strategy

BS-05's coverage is **fixed by the structure of the five convention docs**. The Bootstrapper walks them in a defined order, doing the appropriate work at each.

### Walk order

The order reflects dependency and density:

| Order | Doc | Density | Time | Why this position |
|---|---|---|---|---|
| 1 | LANGUAGE.md | Light | 5-10 min | Independent and short. Establishes language used throughout subsequent docs. |
| 2 | CODE_STYLE.md | Heavy | 15-25 min | Depends on TECH_STACK.md (linter, formatter, type checker, ORM, migration tool). Walked early because subsequent docs reference its patterns (TESTING.md test naming, SECURITY.md exception patterns). |
| 3 | TESTING.md | Medium | 10-15 min | Depends on TECH_STACK.md (test stack) and CODE_STYLE.md (where tests live, naming patterns). |
| 4 | SECURITY.md | Heavy | 15-25 min | Depends on ARCHITECTURE.md §5 (cross-cutting decisions), TECH_STACK.md (auth library, observability), and CODE_STYLE.md (instrumentation conventions). Walked late because it integrates everything. |
| 5 | GIT.md | Light-Medium | 10-15 min | Mostly independent — Conventional Commits, branching, PRs are universal patterns. Walked last because it's the bridge to "now you can start committing" and naturally precedes BS-06 (Docs map). |

**Total time for BS-05: 60-100 minutes** typical, with CODE_STYLE.md and SECURITY.md eating most of the budget.

### Markers to fill per doc

| Doc | Markers | Total |
|---|---|---|
| LANGUAGE.md | USER_FACING_LANGUAGE, DOMAIN_TERMS | 2 |
| CODE_STYLE.md | CLASS_NAMING_TABLE, CLASS_CONSTRUCT_CHOICE, ID_STRATEGY, MIGRATION_TOOL | 4 |
| TESTING.md | TEST_MARKERS, RUNNING_TESTS | 2 |
| SECURITY.md | DATA_TIER_EXAMPLES, RATE_LIMIT_VALUES, DATA_MAP, APPLICABLE_REGULATIONS, AUDIT_EVENTS, FILE_UPLOAD_TYPES, INCIDENT_NOTIFICATION, PR_CHECKLIST_EXTENSIONS | 8 |
| GIT.md | COMMIT_SCOPES, PR_TITLE_CONVENTION | 2 |
| **Total** | | **18 markers** |

### What's constant within each doc visit

For each of the 5 docs, the Bootstrapper applies the same six steps (carried over from BS-03/BS-04, adapted to doc-by-doc granularity):

1. **Present what's there** — summarize the doc's structure: which sections are fixed and which have markers
2. **Check conflicts with product, architecture, stack** — surface any tension between the doc's defaults and what was decided in earlier BS steps
3. **Discuss adjustments and fill markers** — walk each section, with focused discussion on markers and audit questions on fixed sections
4. **Present the final doc content** — composed in a `~~~markdown` fence at the end of the discussion
5. **Maintainer applies** — replaces the doc content with the consolidated block (or applies section-level edits if the doc has many fixed sections that didn't change)
6. **Confirm applied, move to next doc** — Bootstrapper does not start the next doc until the current one is confirmed applied

The depth of step 3 varies by doc. Light docs (LANGUAGE, GIT) have focused marker-filling and minimal fixed-content discussion. Heavy docs (CODE_STYLE, SECURITY) have substantive discussion across multiple sections with several capture-aloud moments.

### Special handling for fixed sections

When most of a doc is fixed content and the only changes are marker-filling, the final block at Step D may be **only the affected sections** rather than the entire doc:

> "Most of GIT.md stays as-is. The two markers (COMMIT_SCOPES, PR_TITLE_CONVENTION) get the content below. Apply just these two sections, keeping everything else unchanged."

This avoids the Maintainer copying 17KB of GIT.md back into the file when only a small portion changed.

The Bootstrapper uses judgment per doc:

- **Whole-doc replacement** when many sections were adjusted (likely CODE_STYLE.md, SECURITY.md if the stack diverges from defaults)
- **Marker-only replacement** when fixed content stood and only markers were filled (likely GIT.md, often LANGUAGE.md)
- **Mixed**: marker-only for most of the doc + one or two targeted line edits for small adjustments in fixed sections

### When the Maintainer wants to deviate from the doc order

Acceptable on request, but the Bootstrapper flags the cost:

> "We can walk SECURITY.md before CODE_STYLE.md if you prefer, but SECURITY.md references CODE_STYLE.md's exception patterns. If we flip the order, we'll revisit some SECURITY.md decisions once CODE_STYLE.md is settled. OK with that?"

The Maintainer's call.

---

## 7. Conversation flow

The BS-05 conversation has **three macro-phases**: opening (7.1), doc-by-doc walk (7.2), and bridge to BS-06 (7.3). Same structural shape as BS-03 and BS-04 but the walk unit is "doc", not "section within a single doc".

### 7.1 — Opening, Decisions Captured recap, pattern announcement (10-15 minutes)

**Purpose:** Re-establish context after BS-04. Recap convention-relevant Decisions Captured (the largest convention-focused recap of any BS step). Announce the doc-by-doc pattern (different from BS-03 and BS-04). Get the Maintainer ready to apply 5 doc-level edits across the step.

**Technique:**

1. **Recap BS-04 output briefly.** Two or three sentences:

   > "BS-04 pinned the tech stack: <runtime>, <web framework>, <test stack>, <auth library>, <observability stack>, <ID strategy>. Now in BS-05 we review the five convention docs against that stack — they ship with opinionated defaults that mostly stand, but the markers in each doc need project-specific content, and a few fixed sections may need adjustments where the stack diverges from the reference."

2. **Recap convention-relevant Decisions Captured exhaustively.** This is the conversation's anchor:

   > "Convention signals carried into BS-05, from all earlier steps:
   >
   > **Code style stack (from BS-04):**
   > - Linter: <X>
   > - Formatter: <Y>
   > - Type checker: <Z>
   > - Architecture rule enforcer: <W>
   >
   > **Test stack (from BS-04):**
   > - Test runner: <A>
   > - Async test support: <B>
   > - Integration infra: <C>
   > - Factory library: <D>
   > - HTTP mock: <E>
   >
   > **Security-relevant stack (from BS-04):**
   > - Auth library: <F>
   > - Password hashing: <G>
   > - JWT library: <H>
   > - Observability: <I>
   >
   > **ID strategy (from BS-04):** <J>
   >
   > **Language signals (from BS-01/BS-02):**
   > - User-facing language: <K>
   > - Domain terms in non-English: <L>
   >
   > **Architectural cross-cutting (from BS-03 §5):**
   > - Auth approach: <M>
   > - Observability approach: <N>
   > - Idempotency approach: <O>
   > - Rate limiting approach: <P>
   >
   > These shape every BS-05 conversation. Confirm or revise?"

   The Maintainer either confirms or adjusts. Adjustments get applied with ripple per BOOTSTRAPPER section 11.

3. **Announce the doc-by-doc pattern.** This is a new pattern — different from BS-03/BS-04 section-by-section:

   > "BS-05 works slightly different from BS-03/BS-04. Instead of section-by-section, we go **doc-by-doc**: we discuss one convention doc fully — all its sections, all its markers — and at the end of the doc I give you a consolidated block to apply. Then we move to the next doc.
   >
   > Why doc-by-doc here: 5 docs with multiple sections each means ~20+ apply moments if we did section-by-section. Doc-by-doc keeps it manageable — 5 apply moments total, one per doc.
   >
   > For light docs (LANGUAGE.md, GIT.md), the consolidation is small — maybe just the markers and one or two adjustments. For heavy docs (CODE_STYLE.md, SECURITY.md), the consolidation is more substantial but still one apply per doc."

4. **Confirm Maintainer is at the keyboard.** Same as BS-03/BS-04:

   > "Ready to apply edits as we go? If you can't right now (away from desk, in a meeting), we can pause and resume."

5. **Preview the doc order.**

   > "We'll walk the 5 docs in this order: LANGUAGE.md (light, ~5-10 min) → CODE_STYLE.md (heavy, ~15-25 min) → TESTING.md (medium, ~10-15 min) → SECURITY.md (heavy, ~15-25 min) → GIT.md (light-medium, ~10-15 min). Total around 60-100 minutes."

**Signal to move on:** Decisions Captured are confirmed, the doc-by-doc pattern is understood, the Maintainer is at the keyboard. Start the walk.

### 7.2 — Doc-by-doc walk (variable, ~45-75 minutes total)

**Purpose:** Walk the 5 convention docs in order. For each doc, apply the six-step pattern (present, check conflicts, discuss, present final, apply, confirm) at the doc level — not at the section level.

This sub-block is 5 micro-conversations chained together. Each doc visit has the same scaffold; what varies is the substance and the density.

**Generic per-doc pattern (applies to all 5 visits):**

The 6 sub-steps (A through F) work the same as BS-03 and BS-04, but the unit is "doc" instead of "section". See BS-03 Section 7.2 for the canonical description of A-F. The BS-05-specific guidance below is per-doc.

---

**Per-doc operational guidance (the 5 visits):**

#### Visit 1 — LANGUAGE.md (Light, 5-10 minutes)

The simplest doc. Two markers, mostly downstream of decisions already captured.

**Step A (present):** "LANGUAGE.md has one rule of thumb ('English by default, second language only when a human user reads it') and two markers: USER_FACING_LANGUAGE and DOMAIN_TERMS. Fixed sections cover developer-facing content (always English), LLM prompts (English system prompts + explicit output language), errors as bridge between internal and user-facing, and what's not covered (third-party data, user-generated content)."

**Step B (check conflicts):** "From BS-01/BS-02 the user-facing language is <X>. Confirm, and tell me the locale code (e.g., `pt-BR`, `en-US`, `es-MX`)?"

**Step C (discuss and fill):**

For USER_FACING_LANGUAGE marker:

> "Two paths:
>
> **Monolingual (English-only).** I'll write 'User-facing content is also in English; no translation layer needed.' Simple.
>
> **Bilingual/multilingual.** I'll capture:
> - Target locale codes
> - Where translations live (i18n catalog file, dedicated folder, localization library)
> - What surfaces are translated (UI labels, errors, emails, dates/numbers/currency, help text, legal text)
> - How third-party data and user-generated content are handled (typically stored as-is)
>
> Which fits, and if bilingual, fill in the specifics?"

For DOMAIN_TERMS marker:

> "Are there domain or regulatory terms specific to your project's jurisdiction or industry that should stay in their original language?
>
> Examples by category:
> - Tax/fiscal document types (e.g., `CNPJ`, `NFe`, `Pix`)
> - Regulatory framework names (e.g., `LGPD`, `GDPR`, `HIPAA`)
> - Industry-specific terms recognized internationally in their original form
>
> If there are any, list them with a one-line reason each. If none, I'll write 'None — all business terms follow English; no domain-specific exceptions.'"

**Step D (present final):**

If small changes (just markers filled): present marker-only blocks.

If the Maintainer adjusted fixed content (rare): whole-doc block.

**Step E (apply), F (move on):** Standard.

#### Visit 2 — CODE_STYLE.md (Heavy, 15-25 minutes)

The densest doc in BS-05. Four markers + many fixed sections worth reviewing against the stack.

**Step A (present):** "CODE_STYLE.md has four markers (CLASS_NAMING_TABLE, CLASS_CONSTRUCT_CHOICE, ID_STRATEGY, MIGRATION_TOOL) and many fixed sections. Fixed sections cover:
- Foundations (formatting, type checking, type annotations, imports, file organization)
- Naming (universal rules, class naming patterns by category, functions and methods)
- Structures (functions, classes, value objects, domain exceptions, domain services, repository pattern, enums, IDs)
- Idioms (control flow, strings, null/empty, dates/times, paths, numbers, collections, async)
- Documentation (docstrings, comments)
- Infrastructure idioms (logging, environment/config, dependency injection)
- Database and migrations (long sub-section: queries/sessions, migration types, autogenerated review, required content, destructive ops, expand/contract, one per PR, running migrations, data migration specifics, AI-assisted workflow, never list)

Density warning: this is the heaviest doc of BS-05. Plan for 15-25 minutes."

**Step B (check conflicts):** Probe against TECH_STACK.md:

> "Checking CODE_STYLE.md defaults against your stack:
>
> - Formatter is <X from BS-04>. The fixed section assumes 'delegated to automatic formatter with config in root file'. Matches.
> - Type checker is <Y from BS-04>. The fixed section assumes 'strict mode wherever the language supports it'. Confirm strict mode is the choice?
> - ORM is <Z from BS-04>. The Database section assumes 'async-only, parametrized queries, ORM expression language'. If you're using a sync ORM, this section needs adjustment.
> - Migration tool is <W from BS-04>. The MIGRATION_TOOL marker needs concrete fill.
>
> Anything I should flag before we walk the markers?"

**Step C (discuss and fill markers):**

For CLASS_NAMING_TABLE marker:

> "Build the class naming table. For each category, name pattern + one example:
>
> - **Entity** — pattern? (default: `PascalCase`, noun, e.g., `Order`)
> - **Value Object** — pattern? (default: `PascalCase`, noun, e.g., `Email`)
> - **Use case** — pattern? (default: `VerbNounUseCase`, e.g., `CreateOrderUseCase`)
> - **Port (domain)** — pattern? (default: `NounRepository`, `NounPort`, e.g., `OrderRepository`)
> - **Port (application/service-client)** — pattern? (default: `NounClient`, e.g., `LLMClient`)
> - **Adapter (infrastructure)** — pattern? (default: `TechNounRepository`, e.g., `PostgresOrderRepository`, `AnthropicLLMAdapter`)
> - **DTO** — pattern? (default: `NounActionDTO`, e.g., `OrderCreateDTO`)
> - **HTTP Schema** — pattern? (default: `NounActionRequest` / `NounActionResponse`)
> - **Domain Service** — pattern? (default: `NounService`)
> - **Mapper** — pattern? (default: `NounMapper`)
> - **ORM Model** — pattern? (default: `NounModel`)
> - **Domain Event** — pattern? (default: `NounPastParticiple`, e.g., `OrderPlaced`)
>
> Any project-specific categories beyond this list?"

For CLASS_CONSTRUCT_CHOICE marker:

> "Map each category to language construct. Walk through:
>
> - Value object construct? (e.g., `@dataclass(frozen=True, slots=True)`, Pydantic `BaseModel`, TypeScript readonly class)
> - Domain entity construct? (e.g., `@dataclass(slots=True)` with mutable state)
> - DTO construct?
> - HTTP schema construct?
> - ORM model construct?
> - When is a regular class with custom `__init__` justified?
>
> Each kind needs a clear construct so the codebase doesn't mix paradigms."

For ID_STRATEGY marker:

> "ID generation strategy decided in BS-04 was <X — e.g., UUIDv7 via uuid-utils>. Fill the specifics:
>
> - Where IDs are generated (domain layer? application? database?)
> - Typed as what in entities/DTOs (e.g., `uuid.UUID`)
> - Serialized how in API responses (e.g., string)
> - Shared helper convention (e.g., `_new_id()` from `app/core/ids.py`)"

For MIGRATION_TOOL marker:

> "Migration tool from BS-04 was <X — e.g., Alembic>. Fill the specifics:
>
> - Config location (e.g., `alembic.ini` at repo root)
> - Versions folder (e.g., `alembic/versions/`)
> - Generation command (e.g., `uv run alembic revision --autogenerate -m '...'`)
> - Apply command (e.g., `uv run alembic upgrade head`)
> - Filename convention (e.g., `YYYY_MM_DD_HHMM-<revision_id>_<slug>.py`)"

**Audit questions on fixed sections (sample, not exhaustive):**

After markers are filled, probe fixed sections that might need adjustment:

- "Foundations — formatting/type checking sections name the tool generically. Want me to inline the actual tool name (e.g., 'ruff for formatting, mypy in strict mode for type checking') or keep generic?"
- "Async section says 'all I/O is async'. Confirms your codebase commitment from BS-03 §4? If you have sync carve-outs, we adjust here."
- "Logging section says structured logger from day one. Names of logger and event-name pattern (e.g., 'structlog, domain.action in snake_case') — inline or keep generic?"
- "Database section is long. Anything in queries/sessions, migrations, or AI-assisted workflow that needs adjustment for your stack?"

**Step D (present final):** Heavy doc. The consolidated block is likely whole-doc — too many sections touched for marker-only application.

**Step E (apply), F (move on):** Standard.

#### Visit 3 — TESTING.md (Medium, 10-15 minutes)

Two markers + several fixed sections (pyramid, coverage targets, layout, LLM rules, auth dual enforcement, factories vs fixtures, property-based, what we don't use).

**Step A (present):** "TESTING.md has two markers (TEST_MARKERS, RUNNING_TESTS) and fixed sections covering philosophy, test pyramid (70/25/5), coverage targets (90%/70%), directory layout, naming pattern, infrastructure (testcontainers), LLM in tests (three layers), authorization dual enforcement, prompt injection tests, factories vs fixtures, property-based testing, what we don't use, writing-a-good-test checklist."

**Step B (check conflicts):** Probe against TECH_STACK.md test stack and ARCHITECTURE.md §3 main flow:

> "Test stack from BS-04: <test runner>, <async support>, <coverage>, <integration infra>, <HTTP mock>, <factory library>, <property-based>. Confirm and we'll map to markers."

**Step C (discuss and fill markers):**

For TEST_MARKERS marker:

> "Define test markers. Recommended set:
>
> - Unit (`@<lang>.mark.unit` or equivalent) — no I/O, in-memory only
> - Integration — real DB/cache via testcontainers
> - E2E — full stack
> - LLM-live — calls real LLM API (gated to nightly)
> - Slow — tests over a threshold (e.g., > 2s), excluded from local loop
>
> Add or remove markers per your project. Also map each marker to its run context (local fast loop, pre-push, CI on PR, nightly CI)."

For RUNNING_TESTS marker:

> "Concrete commands per context:
>
> - Local fast loop (unit only): command?
> - Pre-push (everything except live and slow): command?
> - Full suite: command?
> - With coverage: command?
> - Specific file: command pattern?
> - Specific test: command pattern?
>
> Reference the test runner you pinned in BS-04."

**Audit questions on fixed sections:**

- "Pyramid says 70/25/5 (unit/integration/e2e). Matches your product's testability? Some projects need higher integration proportion if the domain is heavily I/O-bound — surface if applicable."
- "Coverage targets: 90% for pure business logic, 70% for adapters. Adjust based on your team's calibration?"
- "Directory layout follows `tests/<scope>/<path>/test_<file>` mirroring `app/`. Matches STRUCTURE.md from BS-03?"
- "LLM-in-tests rules: fake in unit, real-cheap in nightly integration, evals separate. Aligns with your AI integration plans?"
- "Authorization dual enforcement: PR checklist + meta-test that discovers use cases and verifies authorization tests exist. Confirm meta-test is a Phase 1 task?"
- "Factories vs fixtures: factories for domain objects (one per entity), fixtures for shared resources (DB session, fakes). Factory library is <X from BS-04> — inline or keep generic?"
- "Property-based testing scope: value objects and pure functions with large input surface. Library is <Y from BS-04> — inline or keep?"

**Step D (present final):** Likely whole-doc or marker-only with a few targeted edits to inline tool names. Bootstrapper judgment.

**Step E (apply), F (move on):** Standard.

#### Visit 4 — SECURITY.md (Heavy, 15-25 minutes)

Eight markers + 16 fixed sections. The integration point of architecture (BS-03 §5) + stack (BS-04 auth/observability) + product (BS-01 actors, BS-02 features).

**Step A (present):** "SECURITY.md has 16 sections covering threat model, authentication, authorization, secrets, input validation, output exposure, rate limiting, transport/headers, dependencies/supply chain, data privacy, audit logging, AI/LLM security, file uploads, error handling, incident response, and a PR checklist. Eight markers: DATA_TIER_EXAMPLES, RATE_LIMIT_VALUES, DATA_MAP, APPLICABLE_REGULATIONS, AUDIT_EVENTS, FILE_UPLOAD_TYPES, INCIDENT_NOTIFICATION, PR_CHECKLIST_EXTENSIONS."

**Step B (check conflicts):** Probe against ARCHITECTURE.md §5 + TECH_STACK.md + product:

> "Cross-reference checks:
>
> - ARCHITECTURE.md §5 (Cross-cutting) committed to <auth approach>, <observability approach>, <idempotency approach>, <rate limiting approach>. SECURITY.md fixed sections should align — let me verify and flag any drift.
> - TECH_STACK.md pinned <JWT library>, <password hashing library>, <observability stack>. SECURITY.md's authentication and AI/LLM sections will reference these.
> - From BS-01/BS-02: data types your product handles (e.g., user accounts, transactions, generated artifacts). These populate the data tier examples marker."

**Step C (discuss and fill markers):**

For DATA_TIER_EXAMPLES marker:

> "For each tier, list project-specific data types:
>
> - **Critical**: passwords, JWTs, API keys (universal). Project-specific additions? (e.g., government IDs, payment credentials, full PII?)
> - **Sensitive**: email, phone, business terms, prices (universal-ish). Project-specific? (e.g., conversation content, uploaded documents?)
> - **Internal**: entity IDs, timestamps, status (universal). Project-specific? (e.g., audit trail entries?)
> - **Public**: catalog metadata, public marketing content. Project-specific? (e.g., published prompts, public docs?)"

For RATE_LIMIT_VALUES marker:

> "Concrete numbers per endpoint category:
>
> - Auth endpoints: default 5/15min per IP + 5/15min per account. Adjust?
> - LLM-backed endpoints: default 20/min, 500/day per user. Adjust based on your AI cost model?
> - General authenticated endpoints: default 100/min per user. Adjust?
> - Public endpoints: default 30/min per IP. Adjust?
> - Outbound (per integration, if applicable): per-partner caps from ARCHITECTURE.md §5?"

For FILE_UPLOAD_TYPES marker:

> "Does your product accept file uploads in v1?
>
> If no: I'll write 'No file uploads in v1. Section to be filled when uploads are introduced.'
>
> If yes: for each endpoint category, fill:
> - Allowed types (whitelist)
> - Size limit per category
> - Object storage location (bucket, prefix from BS-04)
> - Virus scan tool/service
> - Signed URL TTL"

For INCIDENT_NOTIFICATION marker:

> "Regulatory notification requirements depend on your jurisdiction and data sensitivity:
>
> - Which authority must be notified for which kinds of incidents? (e.g., GDPR data breach → DPA within 72h; LGPD → ANPD per current guidance; CCPA → AG per timeline)
> - Who has authority to communicate externally about an incident? (e.g., CEO, DPO, legal counsel)
> - Reference to the full incident response runbook (`docs/runbooks/incident-response.md`) when it exists
>
> If no specific regulations apply yet: I'll write a minimal version pointing to 'jurisdiction TBD, runbook to be created before production'."

For PR_CHECKLIST_EXTENSIONS marker:

> "Project-specific PR checklist additions beyond the universal list:
>
> Examples from various projects:
> - 'If touching <domain entity>: verify <specific authorization rule>'
> - 'If touching billing: PR requires CFO sign-off in description'
> - 'If touching <regulated feature>: legal counsel approval in PR thread'
>
> Any project-specific items? If none, marker stays empty (the universal list above is enough)."

**Audit questions on fixed sections:**

- "Authentication section names specific algorithms in the example (RS256, Argon2id). TECH_STACK.md pinned <X>/<Y>. Inline the chosen tools or keep generic?"
- "Authorization section says 'use case layer enforcement, 404 not 403 when existence is not public'. Aligns with ARCHITECTURE.md §5?"
- "AI/LLM security section is detailed. Confirm: ZDR enabled with your LLM provider? Per-user daily token budget set?"
- "Secrets management says 90-day rotation for API keys, 180-day for DB passwords. Adjust?"

**Step D (present final):** Heavy doc. Likely whole-doc or marker-only with multiple targeted edits to inline stack names.

**Step E (apply), F (move on):** Standard.

#### Visit 5 — GIT.md (Light-Medium, 10-15 minutes)

Two markers + many fixed sections (mostly universal Git/PR conventions).

**Step A (present):** "GIT.md has two markers (COMMIT_SCOPES, PR_TITLE_CONVENTION) and many fixed sections: branching strategy (trunk-based), branch naming, Conventional Commits format, signed commits, PR rules (title, description, size, review, CI), merging (squash, linear history), main branch protection, hotfixes, versioning, automated PRs (Renovate/Dependabot), reverting, per-PR workflow (no `git add .`, two granularity levels, ROADMAP at end), common recipes."

**Step B (check conflicts):** Few conflicts expected — Git conventions are universal. Probe:

> "Sanity check:
>
> - Trunk-based development with squash merge and linear history. Matches your team's experience?
> - Signed commits required on main. Setup is documented in README — confirm you'll add the setup section in TSK-001 or similar early task?
> - PR size cap at 400 lines (excluding lockfiles, migrations, autogenerated). Adjust if your team's calibration is different?"

**Step C (discuss and fill markers):**

For COMMIT_SCOPES marker:

> "Define commit scopes. Three categories:
>
> - **Domain areas** — what are the main bounded contexts or features in your product? (from PRODUCT.md actors and ft-*.md files)
> - **Infrastructure** — typical: `llm`, `db`, `cache`, `queue`, `storage`, `auth`. Adjust to your stack.
> - **Cross-cutting** — typical: `api`, `infra`, `deps`, `ci`, `docs`, `roadmap`.
>
> List the project-specific scopes for each category."

For PR_TITLE_CONVENTION marker:

> "PR title convention. Default pattern:
>
> ```
> <type>(<id>): <description>
> ```
>
> Where `<id>` references a roadmap entry (default: `tsk-NNN`). Adjust:
>
> - ID format (default `tsk-NNN`, alternatives: `TSK-NNN`, `T-NNN`, project-specific prefixes)
> - How `<id>` relates to ROADMAP.md entries and `.ai/prompts/` filenames
> - Concrete examples of well-formed PR titles for your project"

**Audit questions on fixed sections:**

- "Branch naming convention (`<type>/<short-kebab-description>` with types `feat/`, `fix/`, `refactor/`, `chore/`, `docs/`). Confirm or adjust?"
- "Commit types (`feat`, `fix`, `refactor`, `perf`, `docs`, `test`, `chore`, `build`, `ci`, `revert`). Sufficient?"
- "Signed commits: GPG or SSH. Setup will be documented in README — confirm signing strategy?"
- "Automated PRs (Renovate/Dependabot): patch auto-merge, minor review, major + ADR. Matches TECH_STACK.md Version bump policy from BS-04 — already confirmed there."
- "Per-PR workflow (no `git add .`, two granularity levels, ROADMAP updates at end with final commit). Anything to adjust?"
- "Exception: initial repository commit (direct to main with `chore: initial documentation and project skeleton`). Aligns with how you'll commit the bootstrap output."

**Step D (present final):** Often marker-only — GIT.md fixed content typically stands.

**Step E (apply), F (move on):** Standard — but Visit 5 ends the doc walk.

---

**Total time for 7.2:** 45-75 minutes typical, with CODE_STYLE.md (Visit 2) and SECURITY.md (Visit 4) eating most of the budget.

### 7.3 — Verify outcomes, capture Decisions Captured for BS-06, bridge to BS-06 (5-10 minutes)

**Purpose:** Close BS-05 cleanly. Walk the exit criteria checklist. Recap what's been captured for BS-06 (the next step). Set the Maintainer up to start BS-06 or pause.

**Technique:**

1. **Walk the Exit criteria checklist (Section 3).** For each doc:
   - LANGUAGE.md markers filled?
   - CODE_STYLE.md markers filled? Fixed sections reviewed?
   - TESTING.md markers filled? Pyramid + coverage confirmed?
   - SECURITY.md markers filled? Cross-cutting alignment confirmed?
   - GIT.md markers filled? Conventional Commits + PR rules confirmed?
   - ADRs created where warranted?

   Surface any unmet outcome. Per Section 10.2 below, the gap is either covered now or deferred with structure.

2. **Recap Decisions Captured for BS-06.** BS-05 surfaces few BS-06-specific signals (BS-06 is mostly about INDEX.md mapping). Recap briefly:

   > "Decisions Captured during BS-05, to consider in BS-06 (Docs map):
   >
   > - Any **future docs** the conventions reference that don't exist yet (e.g., `DATA_MODEL.md`, `API_CONVENTIONS.md`, `AI_INTEGRATION.md`, `OBSERVABILITY.md`, `DEPLOYMENT.md`, `RELEASE.md`, runbooks). INDEX.md's 'Future documents' section may need updates.
   > - Any **cross-references between convention docs** that emerged during the walk (e.g., SECURITY.md authentication → CODE_STYLE.md exception patterns). INDEX.md's 'By topic' table may need new rows.
   > - Any **project-specific 'By task' entries** that became obvious during the walk (e.g., 'When implementing <domain-specific operation>: read X, then Y, then Z'). The BOOTSTRAP:TASK_ENTRIES_EXTENSIONS marker in INDEX.md captures these.
   >
   > These shape the BS-06 INDEX.md validation."

3. **Preview BS-06.** Two sentences:

   > "BS-06 is the shortest BS step — typically 15-30 minutes. We validate INDEX.md reflects the docs that actually exist now (after BS-01 through BS-05 created several), remove any references to docs that won't be used, add project-specific entries, and ensure every cross-reference is accurate."

4. **Ask for readiness.**

   > "BS-06 typically takes 15-30 minutes. We continue in this same chat. Ready, or want a break?"

5. **If the Maintainer pauses:** All convention doc edits and any ADRs are already in the repo (applied during 7.2). Next session resumes by re-reading the five convention docs to refresh state.

**Signal that BS-05 is done:** the recap is agreed, Decisions Captured for BS-06 are confirmed, the Maintainer either kicks off BS-06 or explicitly pauses. The Bootstrapper does NOT silently roll into BS-06.

---

## 8. Forcing functions specific to BS-05

The forcing functions from BOOTSTRAPPER section 10, BS-03 Section 8, and BS-04 Section 8 apply across all BS steps. The following are calibrated for conventions review.

### 8.1 — When the Maintainer accepts the defaults too quickly

**Symptom:** Bootstrapper presents a fixed section (e.g., CODE_STYLE.md naming patterns) and the Maintainer says "looks good" without engaging.

**Why it's a problem:** convention defaults are good, but they make assumptions. A Maintainer who skims past defaults loses the chance to challenge them where they don't fit. Once code is written under a convention, changing it requires reworking the codebase.

**Response pattern:**

> "Before we accept the default for <X>, let me test it against your stack. The default assumes <assumption>. Your TECH_STACK.md pinned <Y> — does the default still hold? If yes, we move on. If no, this is the moment to adjust."

Force one specific stack-grounded probe per major fixed section. Don't accept "looks fine" without at least one tied-to-the-product check.

### 8.2 — When the class naming table feels incomplete

**Symptom:** During CODE_STYLE.md walk, the Maintainer fills the naming table but misses categories that surfaced in BS-03 (e.g., a domain event pattern was implied but not named).

**Why it's a problem:** an incomplete table means contributors will improvise names, leading to drift. The table is the source of truth for class categorization.

**Response pattern:**

> "Audit check on the naming table. From BS-03 §2 Component view, your components include <X> and <Y>. From BS-03 §3 Main flows, <Z> events happen. Are domain events in your project? If yes, the table needs a `Domain Event` row. If no, we explicitly note 'no domain events in v1; pattern TBD if introduced'."

Probe by referencing earlier BS outputs. Force completeness or explicit deferral.

### 8.3 — When the Maintainer wants to invent a new convention mid-step

**Symptom:** During CODE_STYLE.md walk, the Maintainer says "let me also add a convention for retry policies in service-clients" or "let me define how feature flags are named".

**Why it's a problem:** BS-05 reviews existing convention docs against the product. Inventing new conventions on the fly expands scope and the new conventions don't have the discipline of a proper review.

**Response pattern:**

> "New convention candidate: <X>. Two paths:
>
> **A)** Add it to CODE_STYLE.md now if it has a clear, settled pattern. Means we draft a new sub-section.
> **B)** Defer to a follow-up. Capture as an Open Question in ARCHITECTURE.md §10 (or its equivalent), address via ADR + propagation when the pattern is clearer.
>
> Recommendation: **B** unless you can articulate the convention in 2 minutes with confidence. Convention drift comes from premature codification. OK?"

Default to B. Push back unless the convention is genuinely ready.

### 8.4 — When stack-specific idioms conflict with universal rules

**Symptom:** During CODE_STYLE.md Async section, the Maintainer says "we'll use sync ORM for batch jobs" — but the section says "all I/O is async".

**Why it's a problem:** universal rules need consistent application. Carve-outs are fine if explicit; silent exceptions create confusion.

**Response pattern:**

> "Conflict: CODE_STYLE.md Async says 'all I/O is async'. Sync ORM for batch jobs would be a carve-out. Three options:
>
> **A)** Universal async — switch batch jobs to async ORM too. Consistency at the cost of one specific pain point.
> **B)** Explicit carve-out — add 'except batch jobs in `app/workers/batch/`, where sync ORM is justified for <reason>' to the section. Carve-out is documented.
> **C)** Soften the universal — change to 'I/O is async by default; sync is allowed where explicitly justified'. Looser rule.
>
> Recommendation: **B**. Documented carve-out is clearer than silent exception. Which?"

Force a decision. Don't let silent exceptions accumulate.

### 8.5 — When SECURITY.md tier examples feel too thin

**Symptom:** Maintainer fills DATA_TIER_EXAMPLES with 1-2 examples per tier.

**Why it's a problem:** the tier classification is referenced every time a developer or LLM decides "where does this data go" or "should this be logged". Thin examples force interpretation; thick examples make the rule applicable without re-reading.

**Response pattern:**

> "Tier examples feel thin. Let me probe per tier with examples from your product:
>
> **Critical** — beyond passwords and JWTs, what else? Some candidates from your BS-01/BS-02:
> - User PII fields beyond name/email (e.g., date of birth, government IDs, addresses)
> - Payment-adjacent data (card last 4, bank account, tax IDs)
> - API keys for integrations you mentioned
>
> Any of these apply?
>
> **Sensitive** — beyond email/phone/prices, what else?
> - Conversation content if your LLM endpoints store messages
> - Uploaded document content if files are accepted
> - Business terms (contract values, supplier names, internal pricing)
>
> Any of these apply?
>
> [Continue for Internal and Public]"

Probe per tier with product-grounded suggestions. Force at least 3-5 examples per tier where the product genuinely has data.

### 8.6 — When rate limit values are pulled from thin air

**Symptom:** Maintainer fills RATE_LIMIT_VALUES with numbers that don't tie to anything (e.g., "let's do 1000/min, sounds good").

**Why it's a problem:** rate limits chosen without reasoning will either be too tight (real users hit them) or too loose (no actual protection). The values should reflect the product's user model and infrastructure capacity.

**Response pattern:**

> "Let me probe the rate limit values. For each category:
>
> - **Auth endpoints**: 5/15min is a security-sensitive default (prevents brute force without blocking real users). Confirm or adjust based on your auth UX?
> - **LLM-backed endpoints**: depends on your AI cost model. If each call costs ~$0.05 and you have 1000 users, 500/day/user means up to $25k/day theoretical max. Is that reasonable for your cost ceiling?
> - **General authenticated**: 100/min/user is light for normal usage but blocks scraping. Your product has heavy users (admins, batch operations)? They may need a separate tier.
> - **Public**: 30/min/IP prevents abuse but allows real users behind CGNAT (multiple users behind one IP). Confirm 30 is the floor?
>
> Numbers anchored to product reality, not pulled from thin air. Adjust each based on these prompts."

Force product-grounded numbers. Push back on arbitrary values.

### 8.7 — When the Maintainer wants to skip GIT.md

**Symptom:** After SECURITY.md, the Maintainer says "GIT.md is universal, I trust the defaults, skip it".

**Why it's a problem:** the markers (COMMIT_SCOPES, PR_TITLE_CONVENTION) are project-specific. The defaults can't fill them.

**Response pattern:**

> "GIT.md fixed content is largely universal — trunk-based, Conventional Commits, squash merge. Those defaults usually stand.
>
> But the two markers need fills:
>
> - COMMIT_SCOPES: needs your domain-specific scopes from PRODUCT.md actors/features
> - PR_TITLE_CONVENTION: needs your ID format (default `tsk-NNN`; some projects use different)
>
> Both are 5-minute fills. Skip would mean these stay placeholders. Want to do them now, or defer with note?"

Default to "now, 5 min". Defer only if Maintainer is clearly fatigued.

### 8.8 — When ADRs are conflated with conventions

**Symptom:** During CODE_STYLE.md walk, the Maintainer wants to write an ADR for "use snake_case for files" or other universal patterns.

**Why it's a problem:** universal patterns don't need ADRs — they're conventions. ADRs exist for non-obvious choices.

**Response pattern:**

> "<X> is a convention, not an ADR-worthy choice. Conventions go in CODE_STYLE.md (which we're writing); ADRs exist for choices where a future contributor would question 'why this'. snake_case for files in Python is the ecosystem default — no ADR needed.
>
> Reserve ADRs for: non-default architectural style, non-default port placement, non-default ID strategy, non-default JWT algorithm, etc. — choices where the answer 'because that's the default' isn't enough.
>
> Skip this ADR, keep the convention. Sound right?"

Push back on ADR proliferation. Conventions and ADRs serve different purposes.

### 8.9 — When fixed sections in SECURITY.md disagree with BS-03 §5

**Symptom:** SECURITY.md says "Authorization happens in the use case layer". BS-03 §5 said "authorization in middleware + use case layer". Or similar drift.

**Why it's a problem:** SECURITY.md must align with ARCHITECTURE.md §5 cross-cutting decisions. Drift means downstream code follows the wrong policy.

**Response pattern:**

> "Spot check: BS-03 §5 (Cross-cutting decisions) committed to <X>. SECURITY.md §<N> says <Y>. Slight drift. Two options:
>
> **A)** SECURITY.md aligns with BS-03 §5 — update the SECURITY.md section here.
> **B)** ARCHITECTURE.md §5 was wrong and SECURITY.md is right — update ARCHITECTURE.md (ripple to BS-03 outputs).
>
> Which is the source of truth, and which gets updated?"

Resolve before continuing. Always align cross-cutting commitments.

### 8.10 — When the walk exceeds the time budget

**Symptom:** Visit 2 (CODE_STYLE.md) is at 30+ minutes and Visit 4 (SECURITY.md) is still ahead.

**Why it's a problem:** BS-05 target is 60-100 minutes. Going over means BS-06 starts tired or skipped.

**Response pattern:**

> "CODE_STYLE.md took 30 minutes — heavier than expected. We have TESTING.md, SECURITY.md, GIT.md still ahead. Two options:
>
> **A)** Push through, accepting BS-05 will be 100-130 minutes. Good if you're sharp.
> **B)** Pause here. CODE_STYLE.md is applied. Resume with TESTING.md in the next session — context is fresh in this chat.
>
> Recommendation: **B** if you're losing focus, **A** if energy is still good. Quality matters more than finishing in one shot."

Maintainer chooses. BS-05 can split across two sessions cleanly because each doc is independent.

---

## 9. Pre-fill rules (doc-by-doc)

The Bootstrapper does **not** consolidate at the end of BS-05. Instead, content is composed and applied **per doc**, with the Maintainer applying each doc's final block before the next doc begins.

This section defines the doc-level pattern specifics. The fundamentals (capture-aloud format, `~~~markdown` fence, BOOTSTRAP marker replacement) carry over from BS-03 and BS-04.

### 9.1 — Capture-aloud during doc discussion (Step C of each visit)

Same fundamental pattern. The destination is "the doc being discussed" rather than a section:

**Format:**

> "Capturing for `<doc>.md` §<section>: <content + structured anchor>. This goes into the final block for this doc."

**Examples:**

- "Capturing for `CODE_STYLE.md` §Class naming table: Entity = `PascalCase` noun (`Order`); Value Object = `PascalCase` noun (`Email`); Use Case = `VerbNounUseCase` (`CreateOrderUseCase`); ... [continues for all categories]. This goes into the final block for CODE_STYLE.md."

- "Capturing for `SECURITY.md` §Data tier examples: Critical: passwords, JWTs, API keys, government IDs (CPF, RG), payment card data. Sensitive: email, phone, addresses, conversation messages, uploaded document content. Internal: entity IDs, timestamps, audit log entries. Public: catalog metadata, marketing pages. This goes into the final block for SECURITY.md."

**Frequency:** 2-6 captures per doc during discussion, calibrated to doc density. Light docs (LANGUAGE, GIT) may have 1-2 captures. Heavy docs (CODE_STYLE, SECURITY) may have 5-8.

### 9.2 — The final doc block (Step D of each visit)

When the discussion for a doc is complete, the Bootstrapper composes the final content as a fenced markdown block. The shape depends on how much of the doc changed:

**Option A — Marker-only block** (most common for LANGUAGE.md, GIT.md, often TESTING.md):

Only the affected sections (markers filled + any small adjustments) are in the block. The Bootstrapper says:

> "Most of `<doc>.md` stays as-is. Apply only the following changes:
>
> ~~~markdown
> ## §<section heading>
>
> <new content replacing the marker or fixed text>
> ~~~
>
> Apply and confirm."

This avoids 30KB+ copy-paste when only small portions changed.

**Option B — Whole-doc block** (common for CODE_STYLE.md, SECURITY.md when many sections were adjusted):

The entire doc content is in the block. The Bootstrapper says:

> "Many sections of `<doc>.md` were adjusted. Easier to replace the whole doc than chase individual edits. Here's the full new content:
>
> ~~~markdown
> # <Doc title>
>
> <full new content of the doc>
> ~~~
>
> Apply by replacing the entire `<doc>.md` content with the block above. Confirm?"

**Option C — Mixed block** (sometimes for SECURITY.md):

Marker-only for most of the doc, plus one or two targeted before/after edits for fixed sections that needed adjustment. The Bootstrapper says:

> "Two changes to `<doc>.md`:
>
> **Change 1 — Replace marker block in §<N>:**
>
> ~~~markdown
> <new content>
> ~~~
>
> **Change 2 — Targeted edit in §<M>:**
>
> Before: `<exact text>`
>
> After: `<exact text>`
>
> Apply both and confirm."

The Bootstrapper uses judgment to pick the option that minimizes Maintainer cognitive load.

### 9.3 — BOOTSTRAP marker handling

Same rule as BS-03 §9.3 and BS-04: when a marker is filled, the entire marker block (START/END comments + placeholder description) is **replaced** by the actual content. Marker comments do not remain.

The PYTHON_REFERENCE_EXAMPLE comments scattered throughout CODE_STYLE.md, TESTING.md, SECURITY.md follow the same rule as BS-04 §9.2:

- They are **always reference material**, never the project's final content
- The Bootstrapper uses them internally to know the format of patterns expected in markers
- They are **not copied** into the Maintainer's docs, even when the project is Python
- At the end of BS-05, the Bootstrapper offers to **remove the comment blocks** if the Maintainer prefers clean docs (default: remove)

### 9.4 — Handling fixed sections with no changes

If a doc's fixed sections genuinely don't need adjustment (rare but possible for LANGUAGE.md or GIT.md), the final block is **marker-only**. The Bootstrapper says:

> "No fixed sections in `<doc>.md` need changes. Apply only the markers:
>
> ~~~markdown
> <marker contents>
> ~~~"

The Maintainer applies only the marker replacements. The rest of the doc stays as-is.

### 9.5 — ADR creation during BS-05

Same flow as BS-04 §9.4. Typically BS-05 produces 0-2 ADRs (fewer than BS-04). Common BS-05 ADRs:

- Non-default branching strategy
- Non-default coverage targets
- Non-default JWT algorithm
- Non-default docstring style when the language has competing conventions

ADRs are presented as drafts in chat (using `docs/engineering/adr/TEMPLATE.md` as floor), the Maintainer applies as new files in `docs/engineering/adr/`, and cross-references are added to the relevant convention doc.

### 9.6 — Decisions Captured for BS-06

BS-05 surfaces few BS-06-specific signals — BS-06 is primarily about INDEX.md mapping. The Bootstrapper holds mentally:

- Future docs the conventions reference that don't yet exist (`DATA_MODEL.md`, `API_CONVENTIONS.md`, `AI_INTEGRATION.md`, `OBSERVABILITY.md`, `DEPLOYMENT.md`, `RELEASE.md`, runbooks). These inform INDEX.md's "Future documents" section.
- Cross-references between convention docs that emerged (e.g., SECURITY.md → CODE_STYLE.md exception patterns). These inform INDEX.md's "By topic" table.
- Project-specific "By task" entries that became obvious during the walk. These inform the BOOTSTRAP:TASK_ENTRIES_EXTENSIONS marker in INDEX.md.

---

## 10. Required outcomes (checklist for closing BS-05)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step. The checklist mirrors Section 3 (Exit criteria) but reformatted for the closing conversation.

### 10.1 — Outcomes that must be true to close BS-05

#### Per doc

- [ ] LANGUAGE.md — both markers filled (USER_FACING_LANGUAGE, DOMAIN_TERMS)
- [ ] CODE_STYLE.md — all four markers filled (CLASS_NAMING_TABLE, CLASS_CONSTRUCT_CHOICE, ID_STRATEGY, MIGRATION_TOOL); foundations and idioms reviewed against stack
- [ ] TESTING.md — both markers filled (TEST_MARKERS, RUNNING_TESTS); pyramid + coverage + LLM rules reviewed
- [ ] SECURITY.md — all eight markers filled (DATA_TIER_EXAMPLES, RATE_LIMIT_VALUES, DATA_MAP, APPLICABLE_REGULATIONS, AUDIT_EVENTS, FILE_UPLOAD_TYPES, INCIDENT_NOTIFICATION, PR_CHECKLIST_EXTENSIONS); cross-cutting alignment with BS-03 §5 confirmed
- [ ] GIT.md — both markers filled (COMMIT_SCOPES, PR_TITLE_CONVENTION); branching + commits + PR rules reviewed

#### ADRs (if any were created)

- [ ] ADRs for non-default convention choices created in `docs/engineering/adr/`
- [ ] Cross-references from convention docs to relevant ADRs added inline

#### PYTHON_REFERENCE_EXAMPLE comment blocks

- [ ] Decision made on whether to leave or remove the comment blocks at end of BS-05 (default: remove)

#### Anchor questions answered

- [ ] **Anchor 1 (Class naming + construct)** — captured in CODE_STYLE.md
- [ ] **Anchor 2 (Test markers + commands)** — captured in TESTING.md
- [ ] **Anchor 3 (Data tiers + rate limits)** — captured in SECURITY.md
- [ ] **Anchor 4 (Language policy)** — captured in LANGUAGE.md

#### Decisions Captured for BS-06

- [ ] BS-05 contributed signals about future docs, cross-references, and project-specific "By task" entries. The Bootstrapper holds these for BS-06.

### 10.2 — When a required outcome is unmet

Same options as BS-03/BS-04:

**Option A — Cover it now.** Pick up the conversation thread on the gap.

**Option B — Defer it with structure.** Document the gap explicitly:
- Missing marker content → empty marker stays in the doc; entry in ARCHITECTURE.md §10 Open questions referencing the BS-05 gap
- Undecided convention → captured as a future-ADR candidate
- Future doc reference (e.g., `DATA_MODEL.md` mentioned but not yet created) → already a "Future documents" entry in INDEX.md, no separate deferral needed

The Bootstrapper recommends Option A when the gap blocks BS-06 (e.g., class naming table empty — INDEX.md "By topic" can't cross-reference). Option B when the gap is genuinely premature (e.g., file upload section in SECURITY.md when v1 has no uploads).

### 10.3 — What's explicitly NOT required to close BS-05

To prevent perfectionism:

- Linter/formatter/type-checker config file content — TSK-001
- Pre-commit hook config — TSK-001
- CI workflow YAML — Phase 1 tasks
- Production runbooks — emerge near production deploy
- PR template content beyond markers — `.github/PULL_REQUEST_TEMPLATE.md` already ships with template; BS-05 may suggest adjustments but the file already exists
- Future docs content (`DATA_MODEL.md`, `API_CONVENTIONS.md`, etc.) — created when needed
- INDEX.md updates — BS-06's scope

The Bootstrapper resists pulling BS-06+ content into BS-05. Convention review is BS-05's scope; INDEX.md mapping lives downstream.

---

## 11. Bridge to BS-06

When the Required outcomes checklist is complete (or consciously deferred with notes), the Bootstrapper transitions to BS-06 with the following sequence:

### 11.1 — Recap

Three or four sentences:

> "BS-05 reviewed and adjusted the five convention docs. LANGUAGE.md captures <monolingual / bilingual with locale and surfaces>. CODE_STYLE.md has the class naming table, ID strategy <X>, migration tool <Y>. TESTING.md has markers for <list>, commands for <contexts>. SECURITY.md has data tier examples for <count> tiers, rate limits per <count> categories, incident notification per <regulation>. GIT.md has commit scopes <list> and PR title format `<format>`. <N> ADRs created for non-default convention choices. Open questions remain on <list 0-2>."

### 11.2 — Confirm Decisions Captured for BS-06

Recap BS-06-relevant decisions:

> "Signals for BS-06 (Docs map):
>
> - **Future docs mentioned but not yet existing:** <list — DATA_MODEL.md, API_CONVENTIONS.md, AI_INTEGRATION.md, etc.>. INDEX.md's 'Future documents' section may need updates.
> - **Cross-references between convention docs that emerged:** <list — SECURITY.md → CODE_STYLE.md exception patterns, TESTING.md → STRUCTURE.md test layout, etc.>. INDEX.md 'By topic' table may need new rows.
> - **Project-specific 'By task' entries that became obvious:** <list — 'When implementing <domain-specific operation>', 'When handling <regulated feature>'>. The BOOTSTRAP:TASK_ENTRIES_EXTENSIONS marker in INDEX.md captures these.
>
> In BS-06 we validate INDEX.md against the actual docs and add these extensions."

### 11.3 — Preview BS-06

Two sentences:

> "BS-06 validates INDEX.md reflects what actually exists now: the docs from BS-01/BS-02/BS-03/BS-04/BS-05 plus any ADRs/PDRs that emerged. We remove references to docs that won't be used, add the project-specific 'By task' and 'By topic' entries that surfaced during the walk, and confirm every cross-reference is accurate. Short step — typically 15-30 minutes."

### 11.4 — Commit BS-05 before continuing or pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

Before moving to BS-06 (or pausing), the Maintainer commits the work BS-05 produced:

```
git add docs/engineering/LANGUAGE.md \
        docs/engineering/CODE_STYLE.md \
        docs/engineering/TESTING.md \
        docs/engineering/SECURITY.md \
        docs/engineering/GIT.md \
        docs/engineering/adr/NNNN-<slug>.md
        # ... one line per ADR created during BS-05 (typically 0-2)

git commit -S -m "docs(bootstrap): complete BS-05 — conventions"
git push origin main
```

ADR lines are included only when ADRs were actually created during BS-05. The Bootstrapper presents the exact list based on the session.

After the commit lands, proceed to 11.5 (continue) or 11.6 (pause).

### 11.5 — Continue in the same chat

> "BS-06 typically takes 15-30 minutes. We continue in this same chat — the convention context from BS-05 carries forward. Ready to start BS-06, or want a break?"

If the Maintainer pauses, the same recovery pattern applies — docs are already in the repo, resumption re-reads the five convention docs to refresh state.

### 11.6 — If the Maintainer wants to stop entirely

Same handling as BS-01/BS-02/BS-03/BS-04 (everything is saved, stay in same chat session for resumption, "resuming bootstrap" as the resumption phrase).

---

## 12. Notes for the Bootstrapper LLM running BS-05

These are operational reminders specific to BS-05, on top of universal BOOTSTRAPPER guidance and BS-03/BS-04 Section 12 notes.

### 12.1 — Defaults usually stand; markers always need work

BS-05 is the step where the temptation to "rewrite everything" is highest. The convention docs ship with detailed opinions — it's easy to question each one.

Resist. The defaults represent calibrated choices for AI-assisted backend development. Most projects keep them as-is. The Maintainer's energy should go to:

- Filling markers (15 across the 5 docs)
- Reviewing fixed sections **against the stack** (BS-04 outputs determine what tools are named where)
- Resolving any drift from BS-03 §5 cross-cutting commitments

If the Maintainer wants to revise a fixed convention, that's fine — but the revision should be product-grounded or stack-grounded, not aesthetic.

### 12.2 — Cross-reference with BS-03/BS-04 outputs continuously

Every convention doc references decisions from earlier steps:

- **CODE_STYLE.md** references TECH_STACK.md (linter, formatter, type checker, ORM, async patterns, migration tool, logger, config library)
- **TESTING.md** references TECH_STACK.md (test stack) and STRUCTURE.md (test layout mirrors app)
- **SECURITY.md** references ARCHITECTURE.md §5 (cross-cutting commitments) and TECH_STACK.md (auth library, observability stack)
- **GIT.md** references TECH_STACK.md (Version bump policy was already set there)
- **LANGUAGE.md** references PRODUCT.md (user-facing language was implicit in BS-01)

The Bootstrapper has all these in mental state. Use them.

### 12.3 — PYTHON_REFERENCE_EXAMPLE comments are reference, not answer

Same rule as BS-04 §12.3. The PYTHON_REFERENCE_EXAMPLE comments scattered throughout CODE_STYLE.md, TESTING.md, SECURITY.md are **always reference material**, never the project's final content — even when the project is Python.

Use them to know:
- The format of patterns expected in markers
- The level of detail to push for
- The categories typically covered

Do NOT copy-paste from them into the Maintainer's docs. Every project's specifics are different.

At end of BS-05, offer to remove the comment blocks (default: remove).

### 12.4 — Doc-by-doc is the right granularity here

Don't try to apply BS-03/BS-04's section-by-section pattern to BS-05. With 5 docs and 15+ markers, section-by-section becomes 20+ apply moments — exhausting for the Maintainer.

Doc-by-doc means 5 apply moments. Light docs (LANGUAGE, GIT) are fast applies. Heavy docs (CODE_STYLE, SECURITY) need substantial consolidation but stay manageable.

If the Maintainer requests section-by-section for a specific heavy doc (e.g., CODE_STYLE.md), accept — but flag that the doc total time grows.

### 12.5 — Light docs first to build momentum

The walk order (LANGUAGE → CODE_STYLE → TESTING → SECURITY → GIT) starts with a light doc deliberately. LANGUAGE.md is 5-10 minutes — a quick win that builds momentum into the heavier CODE_STYLE.md.

GIT.md last is also deliberate: it's the natural bridge to "now you can start committing", which connects to BS-06 (Docs map) → BS-07 (Roadmap) → first commit.

### 12.6 — Pacing

BS-05 typically takes 60-100 minutes:

- **First 10-15 minutes**: opening, Decisions Captured recap, pattern announcement (7.1)
- **Middle 45-75 minutes**: doc walk (7.2) — 5 visits with variable density
- **Final 5-10 minutes**: outcomes verification, Decisions Captured for BS-06, bridge (7.3)

If conversation runs long (100+ minutes), offer split. Each doc is independently completable — splitting after Visit 2 (CODE_STYLE) or Visit 4 (SECURITY) is natural.

### 12.7 — Few ADRs expected

BS-05 typically produces 0-2 ADRs. Conventions are more standardized than tool choices. ADR candidates emerge for:

- Non-default branching strategy
- Non-default coverage targets
- Non-default JWT algorithm
- Non-default docstring style

If the Maintainer suggests an ADR for a universal pattern (e.g., "ADR for using snake_case in Python"), push back — that's a convention, not an ADR-worthy choice.

### 12.8 — Cross-references update inline

When BS-05 discussions clarify or constrain BS-03 architectural decisions, update inline:

- If SECURITY.md authentication discussion sharpens BS-03 §5 auth approach, update ARCHITECTURE.md §5 in the same session
- If CODE_STYLE.md async section reveals carve-outs not captured in BS-03 §4, update ARCHITECTURE.md §4

This is the BS-05 ripple effect into BS-03 outputs. It's expected — convention details refine architectural commitments.

### 12.9 — Stack-specific idioms get inlined

When a fixed CODE_STYLE.md section uses generic language ("the language's structured logger") and BS-04 pinned a specific tool ("structlog"), offer to inline the tool name:

> "The Logging section says 'structured logger from day one'. BS-04 pinned structlog. Want me to inline 'structlog' specifically (e.g., 'Uses structlog. Logger per module instantiated with `structlog.get_logger(__name__)`.')? Or keep generic?"

Default recommendation: inline. Specificity helps contributors and Claude Code agents. Generic language is for the template; concrete is for the project.

### 12.10 — Convention docs reference future docs that don't exist yet

CODE_STYLE.md, TESTING.md, SECURITY.md all reference docs that will exist later (`DATA_MODEL.md`, `API_CONVENTIONS.md`, `AI_INTEGRATION.md`, `OBSERVABILITY.md`, `DEPLOYMENT.md`, `RELEASE.md`, runbooks).

These references are valid even though the docs don't exist yet — they represent the project's intent for what gets created when the need is concrete. INDEX.md's "Future documents" section catalogs them.

The Bootstrapper does NOT remove these references during BS-05. They stay as forward pointers. BS-06 validates INDEX.md against them.

### 12.11 — BOOTSTRAP markers may reference old step names

Same pattern as BS-03 §12.10 and BS-04 §12.10: the BOOTSTRAP markers in convention docs may reference `05-conventions.md` in their placeholder descriptions. Template-maintenance pendency, not BS-05's concern.

The Bootstrapper:
- Ignores stale step name references when interpreting marker intent
- Replaces marker block entirely during application
- Does not "fix" old references as a side mission
- Flags as known issue if the Maintainer notices

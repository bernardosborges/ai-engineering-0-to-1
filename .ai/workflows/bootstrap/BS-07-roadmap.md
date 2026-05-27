# BS-07 — Roadmap

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-07 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the roadmap planning conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern), this file for the specific content of BS-07, and the output of BS-01 through BS-06 (everything in `docs/` and `.ai/`) as input to this step.

---

## 1. Goal

Build the project's roadmap. By the end of BS-07, `docs/ROADMAP.md` is filled and the phase files under `docs/roadmap/` reflect the actual sequence of tasks the project will execute. The first execution task (TSK-001) is ready to be picked up by `@.ai/workflows/TASK-PLANNER.md` in a separate session.

BS-07 has four conceptual parts:

- **Adapt phase 1 (Repository foundation)** — the template ships a reference phase 1 with 12 universal TSKs covering tooling, lint, type check, tests, CI, pre-commit, signed commits, branch protection. The Bootstrapper proposes an adapted phase 1 that reflects the project's specific stack (from BS-04) and conventions (from BS-05). The Maintainer validates, removes, adds, or reorders.

- **Adapt phase 2 (Application infrastructure)** — same pattern. The template ships a reference phase 2 with 10 universal TSKs (HTTP framework, async DB, migrations, structured logging, observability, integration tests). The Bootstrapper proposes an adapted version reflecting the architecture (from BS-03) and tools (from BS-04).

- **Plan phase 3 (MVP)** — the first project-specific phase. No template reference exists; the Bootstrapper and Maintainer co-author the TSKs from `pd-mvp.md`, `ft-*.md`, and the strategic asset captured in `pd-motivation.md`. This is the densest sub-block of BS-07.

- **Direction for phase 4+** — short paragraphs naming each post-MVP phase, captured in the `FUTURE_PHASES` marker of `ROADMAP.md`. Not detailed TSKs — just direction-setters that prevent silent drift.

Plus two smaller obligations: fill `PROJECT_SPECIFIC_PREPROD_CHECKLIST` (the marker that was misrouted to BS-06 earlier in template development) and fill `PHASE_OVERVIEW_PROJECT_PHASES` (the table row for phase 3 and beyond).

The goal is **not** to invent the entire project's task graph. BS-07 produces a credible plan for phase 1, phase 2, and phase 3 (the MVP), plus direction for phase 4+. Subsequent phases are planned in detail by the post-bootstrap Roadmap Planner (`@.ai/workflows/ROADMAP-PLANNER.md`) when prior phases stabilize.

By the end of BS-07, a competent engineer joining the project could read `ROADMAP.md` plus `docs/roadmap/phase-1-foundation.md`, `phase-2-infrastructure.md`, and `phase-3-<mvp>.md` and know exactly what gets built first, in what order, with what dependencies.

---

## 2. What this step produces

### Documents updated

- **`docs/ROADMAP.md`** — three markers filled:
  - `PHASE_OVERVIEW_PROJECT_PHASES` — table rows for phase 3 and any later phases that have a detailed phase file
  - `FUTURE_PHASES` — direction-setters for phases beyond what's been detailed (one paragraph each)
  - `PROJECT_SPECIFIC_PREPROD_CHECKLIST` — items beyond the universal checklist

  Fixed sections (Principles, Notes on using this roadmap, Bugfix/adjustment/replanning conventions, Planning events) reviewed but typically untouched.

- **`docs/roadmap/phase-1-foundation.md`** — rewritten with the adapted TSK list. PYTHON_REFERENCE_EXAMPLE comment blocks removed; concrete tool names and commands inlined for the project's actual stack. TSK count varies (typically 8-15) based on what the project keeps, removes, or adds. The intro note ("**Note on this phase file.** This is a starting point seeded by the template...") is also removed — the phase file is now project-specific, not a starting point.

- **`docs/roadmap/phase-2-infrastructure.md`** — same pattern. Rewritten with the adapted TSK list, PYTHON_REFERENCE_EXAMPLE blocks removed, intro note removed. TSK count varies (typically 6-15) and may include multiple database tasks, multiple integration tasks, etc., depending on what BS-03 §2 and BS-04 surfaced.

### Documents created

- **`docs/roadmap/phase-3-<slug>.md`** — new file. The MVP phase. Contains the TSKs that build the project's first user-facing functionality. TSK numbering continues from where phase 2 ended. The phase file follows the same shape as phase-1 and phase-2 (header, goal, milestone, TSK entries, retrospective placeholder), but has no intro note about being a template starting point — it never was one.

### Documents potentially modified

- **`docs/INDEX.md`** — the BS-06 `PHASE_FILES` marker (which BS-06 intentionally left empty) gets a new entry: `phase-3-<slug>.md`. The marker stays in place for phase 4+ to be filled later.

### Documents NOT produced

- **Phase 4+ phase files.** They exist as direction in the `FUTURE_PHASES` marker of ROADMAP.md only. The actual `docs/roadmap/phase-N-<slug>.md` files are created post-bootstrap when prior phases stabilize, via the Roadmap Planner workflow.
- **Execution prompts under `.ai/prompts/execution/`.** Those are created by `@.ai/workflows/TASK-PLANNER.md` in a separate session, one at a time, when each TSK is about to start.
- **Application code.** Bootstrap doesn't write code.

### Critical premise — phase 1 and 2 are reference, not gabarito

The phase-1 and phase-2 files that ship in the template represent a **reference of quality and ordering** for AI-first backend projects. They are not a fixed plan. The Bootstrapper's job in BS-07 sub-blocks 7.2 and 7.3 is to **propose an adapted version** based on the decisions accumulated through BS-01..BS-06, not to ask the Maintainer to validate each TSK in turn.

The reference matters because the **ordering** has been thought through: tools before pre-commit, tests before architecture rules, DB session before migrations, observability before integration tests. Most projects benefit from this ordering even when the specific tools differ. The Bootstrapper preserves the ordering by default and surfaces ripple effects when the Maintainer's choices break it.

The reference does **not** matter as a fixed list. Projects routinely:
- **Remove** TSKs that don't apply (e.g., no architecture rule enforcer if the project rejected one in BS-05; no migration tool if the project is schemaless)
- **Add** TSKs that the template didn't anticipate (e.g., Renovate setup, multi-region container build, multiple databases)
- **Reorder** when the chosen stack changes dependencies between tools (e.g., a JS/TS stack may need package.json and tsconfig before the linter)
- **Split or merge** when granularity changes (e.g., two databases means two distinct TSK-016-equivalents)

The Bootstrapper proposes, the Maintainer adjusts, and the output is a phase-1 and phase-2 that reflect both the template's ordering wisdom and the project's specific reality.

---

## 3. Exit criteria

BS-07 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

#### docs/roadmap/phase-1-foundation.md

- [ ] Adapted TSK list reflects the stack chosen in BS-04 and the conventions chosen in BS-05
- [ ] PYTHON_REFERENCE_EXAMPLE comment blocks removed
- [ ] Template intro note ("Note on this phase file...") removed
- [ ] Each TSK has substantive `What`, `Why`, `Depends on`, `Acceptance` fields; `Status` is `⬜ Not started`; `PR link` and `Learnings` are placeholders
- [ ] Dependency ordering is consistent (no TSK depends on a later one)

#### docs/roadmap/phase-2-infrastructure.md

- [ ] Adapted TSK list reflects ARCHITECTURE.md §2 components and TECH_STACK.md tools
- [ ] PYTHON_REFERENCE_EXAMPLE comment blocks removed
- [ ] Template intro note removed
- [ ] Each TSK has substantive `What`, `Why`, `Depends on`, `Acceptance`
- [ ] Dependency ordering is consistent

#### docs/roadmap/phase-3-<slug>.md

- [ ] File created with project-specific MVP TSKs
- [ ] TSK numbering continues sequentially from phase 2 end
- [ ] Each TSK is grounded in `pd-mvp.md` and the relevant `ft-*.md`
- [ ] Each TSK has substantive `What`, `Why`, `Depends on`, `Acceptance`
- [ ] TSK count is calibrated to MVP scope (typically 8-15; significant deviations justified)
- [ ] Phase file name slug reflects the MVP's core concept

#### docs/ROADMAP.md

- [ ] `PHASE_OVERVIEW_PROJECT_PHASES` marker filled with the phase 3 row (and any later phase with a detailed phase file)
- [ ] `FUTURE_PHASES` marker filled with direction-setters for phase 4+ (or explicitly noted as "none — MVP is the full v1")
- [ ] `PROJECT_SPECIFIC_PREPROD_CHECKLIST` marker filled with project-specific items, or explicitly empty with acknowledgment

#### docs/INDEX.md

- [ ] `phase-3-<slug>.md` entry added to the phase files area (the marker `PHASE_FILES` accepts new entries inline; alternatively, the marker is amended)

### Soft requirements (worth pushing for but not blockers)

- [ ] Cross-references between phase 3 TSKs and the relevant `ft-*.md` files are present (e.g., TSK-025 references `ft-orders.md`)
- [ ] The phase 3 retrospective placeholder is in place
- [ ] Any tasks that surfaced as needed but were deferred (e.g., "Write DEPLOYMENT.md") are captured in their target phase

### What does NOT block exit

- Phase 4+ detailed phase files — those are post-bootstrap work
- Execution prompts for any TSK — those come from `TASK-PLANNER.md` later
- Application code
- TSK-level acceptance criteria written at production-grade detail — first-pass acceptance is sufficient; the planner refines per task

The Bootstrapper does not advance to BS-07's bridge silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before closing BS-07, the `FUTURE_PHASES` marker is still the placeholder. We discussed phases 4 and 5 informally — let me consolidate those into the marker before we wrap."

---

## 4. Anchor questions

These are the four questions that **must be answered with confidence** before BS-07 closes. They emerge through the sub-block walk (see Section 7 — Conversation flow). The Bootstrapper keeps mental track of which ones are still open.

### Anchor 1 — Does phase 1 reflect the actual foundation work for this stack?

This becomes `phase-1-foundation.md`. The Maintainer must commit to:

- Every TSK in the adapted phase 1 maps to a tool, command, or commitment from BS-04/BS-05
- The ordering respects dependencies (linter before pre-commit, test runner before architecture enforcer)
- No TSK is in the file that won't actually be executed (e.g., import-linter TSK removed if the project chose no architecture enforcer)
- No TSK that should be there is missing (e.g., Renovate TSK added if the project chose Renovate)

Forcing function: **a contributor opening phase-1-foundation.md should be able to start TSK-001 immediately, knowing every later TSK in the phase is concretely planned**. Hand-waved TSKs ("we'll figure out CI later") block the phase.

### Anchor 2 — Does phase 2 reflect the actual application infrastructure?

This becomes `phase-2-infrastructure.md`. Similar to Anchor 1 but for the second phase. The Maintainer must commit to:

- Components named in ARCHITECTURE.md §2 each have a TSK that brings them online (or an explicit decision that they're not in v1)
- Cross-cutting decisions from ARCHITECTURE.md §5 have TSKs (auth setup, observability wiring, idempotency mechanism, rate limiting)
- Multiple-of-a-kind components (two DBs, two queues) have multiple TSKs

### Anchor 3 — Does phase 3 deliver the MVP described in pd-mvp.md?

This becomes `phase-3-<slug>.md`. The Maintainer must commit to:

- Every MVP validation hypothesis in `pd-mvp.md` has at least one TSK that produces the system capability needed to test it
- The strategic asset from `pd-motivation.md` is being accumulated by the end of phase 3 (even if minimally)
- The first 100 users path from `pd-mvp.md` distribution section is functional by the end of phase 3

If the Maintainer can't draw a line from MVP hypotheses to TSKs, phase 3 is under-specified. Surface as a gap.

### Anchor 4 — What's the direction beyond MVP?

This becomes the `FUTURE_PHASES` marker. The Maintainer must commit to either:

- A list of post-MVP phases (one paragraph each) that describe what comes next, OR
- An explicit acknowledgment that "MVP is the full v1; subsequent direction will be set after MVP validation"

The forcing function: **direction-setters prevent silent drift**. Without them, the team can get to end of phase 3 and waste planning cycles deciding "what next?". A rough direction commits nothing but anchors the conversation.

---

## 5. How BS-07 relates to BS-01..BS-06

BS-07 is the synthesis step. Every earlier BS step contributes:

- **BS-01 (product discovery)** — `pd-motivation.md` (strategic asset), `pd-mvp.md` (MVP scope), `pd-workflows.md` (actor flows that phase 3 implements). Plus the legal signals in `POLICY_CONSIDERATIONS.md` that might generate pre-prod checklist items.

- **BS-02 (product document)** — `ft-*.md` files. Phase 3 TSKs reference specific features. The strategic asset (often documented in a `ft-*.md` like `ft-price-database.md`) drives the highest-priority phase 3 TSKs.

- **BS-03 (architecture)** — `ARCHITECTURE.md` §2 (components) and §5 (cross-cutting) shape phase 2 TSKs. §6 (data view) decides how many database/storage TSKs are needed. `STRUCTURE.md` confirms folder paths.

- **BS-04 (tools)** — `TECH_STACK.md` provides concrete tool names that replace the PYTHON_REFERENCE_EXAMPLE references throughout phase 1 and phase 2.

- **BS-05 (conventions)** — `CODE_STYLE.md` ID strategy → TSK-018 specifics; `TESTING.md` markers → TSK-008 specifics; `SECURITY.md` markers → security-related TSKs in phase 2; `GIT.md` PR conventions → CI gates in TSK-010.

- **BS-06 (docs map)** — the "Future documents" decisions in `docs/INDEX.md` map to TSKs (e.g., "Write DEPLOYMENT.md" becomes a TSK near deploy phase).

### Persistence pattern

BS-07 uses a **file-by-file persistence pattern**, similar to BS-05 (doc-by-doc) but with one important difference: each phase file is produced as a **whole file** at the end of its sub-block, not as scattered marker fills. The Maintainer applies the full file in one go.

For ROADMAP.md (markers `PHASE_OVERVIEW_PROJECT_PHASES`, `FUTURE_PHASES`, `PROJECT_SPECIFIC_PREPROD_CHECKLIST`): marker-by-marker fills during their respective sub-blocks, then a final consolidation moment after all three are agreed.

This produces 5-6 apply moments total:

1. Apply adapted `phase-1-foundation.md` (after 7.2)
2. Apply adapted `phase-2-infrastructure.md` (after 7.3)
3. Apply new `phase-3-<slug>.md` (after 7.4)
4. Apply `FUTURE_PHASES` marker fill in ROADMAP.md (after 7.5)
5. Apply `PROJECT_SPECIFIC_PREPROD_CHECKLIST` marker fill in ROADMAP.md (after 7.6)
6. Apply `PHASE_OVERVIEW_PROJECT_PHASES` marker fill in ROADMAP.md + `phase-3` entry in INDEX.md (at the bridge, 7.7)

### What the Maintainer needs at the keyboard

Same as BS-05/BS-06: editor open on the target file as each sub-block produces its final content. Plus, for sub-blocks 7.4 (phase 3 planning), a willingness to **engage as co-author**, not just validator — phase 3 has no template reference, so it emerges from conversation.

If the Maintainer can't apply edits during the conversation, they should pause BS-07 and resume when they can. Each apply moment is a natural pause point.

---

## 6. Coverage strategy

BS-07's coverage is **fixed by the seven sub-blocks**. The Bootstrapper walks them in order:

| Sub-block | Focus | Density | Time |
|---|---|---|---|
| 7.1 | Opening, recap, pattern announcement | Light | 10-15 min |
| 7.2 | Adapt phase 1 | Medium | 15-25 min |
| 7.3 | Adapt phase 2 | Medium-Heavy | 15-25 min |
| 7.4 | Plan phase 3 (MVP) | Heaviest | 25-40 min |
| 7.5 | Direction for phase 4+ | Light-Medium | 10-15 min |
| 7.6 | PROJECT_SPECIFIC_PREPROD_CHECKLIST | Light | 5-10 min |
| 7.7 | Verify outcomes, ROADMAP.md final consolidation, bridge | Light-Medium | 10-15 min |

**Total time: 80-130 minutes.** This is the densest BS step, with phase 3 planning eating the most budget.

### Walk order rationale

- **Phase 1 before phase 2** because phase 2 builds on phase 1's foundation (you can't bring up the DB session without the manifest, package manager, and test runner in place)
- **Phase 1 and 2 before phase 3** because phase 3 reuses everything phase 1 and 2 deliver
- **Phase 3 before future phases** because phase 4+ direction depends on what was scoped into phase 3 vs deferred
- **Future phases before pre-prod checklist** because some pre-prod items map to specific phases (e.g., "Operator training docs published" is a phase 4 deliverable if operators come online in phase 4)
- **Final ROADMAP.md consolidation last** because the phase overview table needs the phase 3 slug, which is decided in 7.4

### What's constant within each sub-block

For sub-blocks 7.2, 7.3, and 7.4, the Bootstrapper applies a consistent six-step pattern:

1. **Present the starting point** — for 7.2/7.3, the template's reference TSK list (summarized); for 7.4, the MVP scope from `pd-mvp.md`
2. **Surface decisions accumulated** — what BS-01..BS-06 outputs imply for this phase
3. **Propose the adapted/new TSK list** — concrete proposal with TSK names and one-line What
4. **Discuss adjustments** — Maintainer adds, removes, reorders, asks for splits/merges
5. **Present the final phase file content** — composed in a fenced markdown block
6. **Maintainer applies, confirms, moves on**

Sub-blocks 7.5 and 7.6 are lighter — single marker fills with a brief discussion.

### When the Maintainer wants to deviate from this order

Acceptable on request, but the Bootstrapper flags the cost:

> "We can plan phase 3 before adapting phase 2 if you prefer, but phase 2 TSKs (especially the DB and observability ones) shape what's available to phase 3. If we flip the order, we may revisit phase 3 once phase 2 is settled."

Default to the canonical order. Deviation is rare.

---

## 7. Conversation flow

The BS-07 conversation has **seven sub-blocks**: opening (7.1), adapt phase 1 (7.2), adapt phase 2 (7.3), plan phase 3 (7.4), future phases (7.5), pre-prod checklist (7.6), bridge (7.7).

### 7.1 — Opening, recap, pattern announcement (10-15 minutes)

**Purpose:** Re-establish context after BS-06. Recap the relevant outputs of BS-01..BS-06 that BS-07 will synthesize. Explain the seven sub-block structure. Set expectations on the densest BS step.

**Technique:**

1. **Recap BS-06 output briefly.**

   > "BS-06 closed the documentation map. Now in BS-07 we build the roadmap. By the end of this step, your project has a credible plan for phase 1 (foundation), phase 2 (infrastructure), phase 3 (MVP), plus direction for phase 4+. TSK-001 will be ready for the Planner to pick up in a separate session."

2. **Recap roadmap-relevant decisions from earlier BS steps.** This is the largest recap of any BS step because BS-07 synthesizes everything:

   > "Decisions carried into BS-07:
   >
   > **From BS-01:**
   > - Strategic asset: <X> (drives phase 3 priority)
   > - MVP scope: <one-line from pd-mvp.md>
   > - First 100 users distribution: <one-line>
   > - Legal signals: <list of LP entries that may produce pre-prod items>
   >
   > **From BS-02:**
   > - <N> features documented. Phase 3 implements: <list 3-5 MVP-critical features>
   > - Transversal models: <auth, billing, etc. — these inform phase 2>
   >
   > **From BS-03:**
   > - Components in §2: <count> — each may need a phase 2 TSK
   > - Cross-cutting in §5: <auth approach>, <observability stack>, <idempotency>, <rate limiting>
   > - Data view: <N> stores — each may need a phase 2 TSK
   >
   > **From BS-04:**
   > - Stack: <language>, <web framework>, <ORM>, <test stack>, <auth library>, <observability tools>, <ID strategy>
   > - <N> ADRs that may inform specific TSKs
   >
   > **From BS-05:**
   > - Class naming + construct choices → CODE_STYLE.md drives TSK-018 (ID generation)
   > - Test markers → TSK-008 specifics
   > - SECURITY.md commitments → security-related TSKs in phase 2
   >
   > **From BS-06:**
   > - 'Future documents' list — each implies a 'Write <doc>.md' TSK in a relevant phase
   >
   > All of these shape what gets proposed in the next sub-blocks. Confirm or revise?"

   The Maintainer confirms or adjusts. Adjustments ripple per BOOTSTRAPPER section 11.

3. **Announce the seven sub-block structure.**

   > "BS-07 has seven sub-blocks:
   >
   > - **7.1** — what we're doing right now: opening and recap.
   > - **7.2** — adapt phase 1 (Repository foundation). I propose a TSK list based on your stack and conventions; you validate, remove, add. 15-25 min.
   > - **7.3** — adapt phase 2 (Application infrastructure). Same pattern. 15-25 min.
   > - **7.4** — plan phase 3 (MVP). This is where we co-author from scratch — no template reference, just `pd-mvp.md` and the `ft-*.md` files. 25-40 min.
   > - **7.5** — direction for phase 4+. Short paragraphs naming each post-MVP phase. 10-15 min.
   > - **7.6** — pre-production checklist project-specific items. 5-10 min.
   > - **7.7** — verify outcomes, final ROADMAP.md consolidation, bridge to end of bootstrap. 10-15 min.
   >
   > Total: 80-130 minutes. Densest BS step. We can pause between sub-blocks if you need."

4. **Set expectations on phase 1/2 adaptation specifically.**

   > "Important: phase 1 and phase 2 ship in the template as reference. They're 12 TSKs and 10 TSKs respectively, covering universal AI-first backend setup — but they assume a Python+FastAPI+Postgres+pgvector stack in their examples. We will rewrite both phase files to use your actual stack inline, remove the PYTHON_REFERENCE_EXAMPLE comment blocks, and adjust the TSK list. Most projects keep the ordering but swap the tools; some projects add or remove TSKs based on what was chosen in BS-04/BS-05."

5. **Confirm Maintainer is at the keyboard.**

   > "Ready to apply edits as we go? Each sub-block produces one apply moment. Phase 3 is co-authored so be ready to engage actively."

**Signal to move on:** Maintainer is oriented, recap is confirmed, ready to start phase 1 adaptation.

### 7.2 — Adapt phase 1 (15-25 minutes)

**Purpose:** Produce the project-specific phase-1-foundation.md based on the template reference + BS-04 stack + BS-05 conventions.

**Technique:**

1. **Present the template reference summarized.**

   > "Phase 1 in the template has 12 TSKs:
   >
   > 1. TSK-001 — Initial commit: documentation and project skeleton
   > 2. TSK-002 — `.gitignore` and equivalent build-exclude files
   > 3. TSK-003 — Project manifest skeleton
   > 4. TSK-004 — Configure package manager
   > 5. TSK-005 — Configure linter and formatter
   > 6. TSK-006 — Configure type checker
   > 7. TSK-007 — Configure pre-commit
   > 8. TSK-008 — Configure test runner
   > 9. TSK-009 — Configure architecture rule enforcement
   > 10. TSK-010 — CI pipeline
   > 11. TSK-011 — PR template
   > 12. TSK-012 — `README.md`
   >
   > The ordering is intentional: each TSK depends on earlier ones. Tools before pre-commit, tests before architecture rules, all before CI."

2. **Surface adaptations from BS-04/BS-05.** Walk through each TSK with the project's specifics:

   > "Adaptations for your project:
   >
   > - **TSK-001** — reformulated. Since documentation was committed during bootstrap, this TSK is now 'Create the application folder skeleton only — no docs'. Empty subfolders matching STRUCTURE.md, with `.gitkeep`.
   > - **TSK-002** — `.gitignore` for <language>. <Dockerignore note>.
   > - **TSK-003** — Project manifest: `<pyproject.toml / package.json / Cargo.toml>` declaring <language version> and one runtime dep for smoke testing.
   > - **TSK-004** — Configure <package manager>. Pin <language version> in `<.python-version / .nvmrc / etc.>`. Generate `<uv.lock / package-lock.json / etc.>`.
   > - **TSK-005** — Configure <linter> and <formatter>. From BS-05 conventions: line length <X>, target <version>, rules <list>.
   > - **TSK-006** — Configure <type checker> in strict mode.
   > - **TSK-007** — Pre-commit hooks: <linter>, <formatter>, <type checker>, validators, <secret scanner>.
   > - **TSK-008** — Configure <test runner>. Register markers: <list from BS-05 TESTING.md>.
   > - **TSK-009** — Configure <architecture enforcer> OR removed if BS-05 rejected one.
   > - **TSK-010** — CI on <provider>. Run <linter>, <formatter check>, <type checker>, <architecture enforcer if any>, <test runner>. Branch protection on main.
   > - **TSK-011** — Verify PR template (already committed during bootstrap; this TSK just confirms it renders correctly on new PRs).
   > - **TSK-012** — `README.md` for repo root.
   >
   > Anything to remove, add, or reorder?"

3. **Probe for project-specific additions.** Common cases:

   > "Common additions for your stack:
   >
   > - **Renovate / Dependabot configuration** — if BS-04 set a version bump policy that requires automation
   > - **Secrets management setup** — if BS-05 SECURITY.md committed to a non-env-var solution from day one
   > - **Multi-language build** — if the project has both backend and frontend in the same repo
   > - **Container build configuration** — if Dockerfile is in phase 1 vs phase 2 (per ARCHITECTURE.md §5 'Development environment' posture). For posture B (fully containerized dev), Dockerfile is a phase 1 TSK because dev itself runs through it. For posture A (native dev + containerized infra), Dockerfile lands in phase 2 closer to first deploy.
   > - **Dev Container config (`.devcontainer/devcontainer.json`)** — if the project chose posture B and wants one-click Codespaces/VSCode onboarding
   >
   > Any of these apply?"

4. **Probe for project-specific removals.** Common cases:

   > "Common removals:
   >
   > - **TSK-009 (architecture enforcer)** if BS-05 rejected layering enforcement (rare but possible for very small projects)
   > - **TSK-007 (pre-commit)** if the project intentionally runs everything in CI only
   > - **TSK-006 (type checker)** if the language has built-in static typing without a separate type-check step (e.g., Rust, Go)
   >
   > Any of these apply?"

5. **Confirm final TSK list.** Once additions and removals are agreed:

   > "Final phase 1 TSK list — <N> TSKs:
   >
   > 1. <name>
   > 2. <name>
   > ...
   >
   > Confirm before I compose the full phase file?"

6. **Compose and present the final phase file.** A `~~~markdown` fenced block containing the complete `phase-1-foundation.md`:

   - Header: `# Phase 1 — Repository foundation`, Goal, Milestone (adjusted if needed)
   - **No** intro note ("Note on this phase file..." — removed)
   - Each TSK with concrete `What`, `Why`, `Depends on`, `Acceptance`, plus `Status: ⬜ Not started`, `PR link: —`, `Learnings: —`
   - **No** PYTHON_REFERENCE_EXAMPLE comment blocks — concrete commands inline in `What` and `Acceptance`
   - Retrospective placeholder at the end

7. **Wait for application.** Maintainer applies, confirms.

   > "Applied. Move to phase 2?"

### 7.3 — Adapt phase 2 (15-25 minutes)

**Purpose:** Produce the project-specific phase-2-infrastructure.md.

Same six-step pattern as 7.2. The substantive differences:

1. **Template reference is 10 TSKs** (TSK-013..TSK-022):
   - Application skeleton with health endpoint
   - Typed configuration
   - Local infrastructure via containers
   - Database session and DI
   - Migration tool initialization
   - ID generation helper
   - First database model and real migration
   - Structured logging
   - Observability (tracing, metrics)
   - Integration test infrastructure

2. **Adaptations are denser than in phase 1.** Phase 2 reflects ARCHITECTURE.md §2 components and §5 cross-cutting decisions. Common adaptations:

   > "Adaptations for your project:
   >
   > - **TSK-013** — Application skeleton in <web framework>. Health endpoint at `/health`.
   > - **TSK-014** — Typed configuration via <config library>. Env vars from `.env`.
   > - **TSK-015** — Local infrastructure. From ARCHITECTURE.md §2, you have <N> services: <list>. Compose file brings them all up.
   > - **TSK-016** — Primary database session for <DB-1>. <Async / sync> session factory, DI provider.
   > - **TSK-016.5** — Secondary database session for <DB-2> if applicable. [Added based on §2]
   > - **TSK-017** — Migration tool: <Alembic / Prisma / etc.> initialization for <DB-1>.
   > - **TSK-018** — ID generation helper using <UUIDv7 / etc.> per ADR <N>.
   > - **TSK-019** — First database model and real migration. Minimal entity to validate the migration flow end-to-end.
   > - **TSK-020** — Structured logging via <logger>. JSON in prod, key-value in dev.
   > - **TSK-021** — Observability via <OTel + tools>. Exporter, collector in compose, auto-instrumentation.
   > - **TSK-022** — Integration test infra via <testcontainers / equivalent>.
   >
   > Plus any cross-cutting from ARCHITECTURE.md §5 that needs its own TSK:
   >
   > - Auth wiring if non-trivial (e.g., JWT key management): potential TSK-022.5
   > - Rate limiting setup if global: potential TSK-022.6
   > - Idempotency mechanism if non-trivial: potential TSK
   >
   > Anything to add, remove, reorder?"

3. **Probe for multiple-of-a-kind components.** ARCHITECTURE.md §6 (data view) may have multiple stores:

   > "Your data view has <N> stores. Currently phase 2 covers <primary DB> in TSK-016/017/019. The other stores need their own TSKs:
   >
   > - <Vector store>: bring it up in compose (extend TSK-015), session setup (new TSK), no migrations if rebuildable.
   > - <Cache/queue>: bring it up in compose, client setup (new TSK), no migrations.
   > - <Object storage>: client setup (new TSK), no compose entry if using cloud directly.
   >
   > Propose the additions?"

4. **Probe for Dockerfile and dev environment posture impact.** Based on ARCHITECTURE.md §5 'Development environment':

   > "Your project chose posture <A or B from BS-03>:
   >
   > - **If posture A (native dev + containerized infra):** Dockerfile is a phase 2 TSK — needed for CI image build and production deploy. Propose TSK-<N> 'Containerize the application' after the app skeleton (TSK-013) and before observability (TSK-021), since CI needs the image to run integration tests in some setups.
   > - **If posture B (fully containerized dev):** Dockerfile was a phase 1 TSK and is already in place. Phase 2 doesn't add a new Dockerfile TSK — it consumes the existing one.
   >
   > Confirm which case applies and where the Dockerfile TSK lands?"

5. **Probe for security-critical TSKs.** From SECURITY.md and architecture:

   > "SECURITY.md commitments that may need their own TSK:
   >
   > - **Secrets storage** — if env vars are not the production solution, TSK to set up the chosen tool (Vault, AWS Secrets Manager, Doppler, etc.)
   > - **Authentication scaffolding** — if auth is in phase 2 rather than phase 3
   > - **Audit logging** — if a separate audit log destination is wired in phase 2
   >
   > Any of these?"

6. **Final TSK list, compose, apply.** Same as 7.2.

### 7.4 — Plan phase 3 (25-40 minutes)

**Purpose:** Co-author phase 3 from scratch. No template reference. The MVP comes alive here.

This is the densest sub-block of BS-07. The Maintainer must engage actively — not as validator of a proposal, but as co-author.

**Technique:**

1. **Anchor on pd-mvp.md.**

   > "Phase 3 delivers your MVP. From `pd-mvp.md`:
   >
   > - **Scope:** <one-paragraph summary from pd-mvp.md>
   > - **Validation hypotheses:** <list from pd-mvp.md>
   > - **Distribution to first 100 users:** <approach from pd-mvp.md>
   > - **Strategic asset accumulation:** by end of phase 3, <strategic asset from pd-motivation.md> should be growing in <how, per pd-mvp.md>
   >
   > Confirm this is still the MVP scope, or has anything shifted?"

   The Maintainer confirms or refines. If the MVP has materially shifted since BS-01, surface the ripple:

   > "If the MVP scope shifted, that's a PDR-worthy event. Want to capture the shift formally, or proceed with the new scope informally and write the PDR post-bootstrap?"

2. **Identify the features that phase 3 implements.**

   > "Looking at the ft-*.md files from BS-02, the MVP scope implies phase 3 implements:
   >
   > - <ft-X.md>: <which capabilities specifically>
   > - <ft-Y.md>: <which capabilities specifically>
   > - <ft-Z.md>: <partial — only the user-facing read path, not curation>
   >
   > Features explicitly NOT in phase 3 (deferred to phase 4+):
   >
   > - <ft-A.md>: deferred because <reason>
   > - <ft-B.md>: deferred because <reason>
   >
   > Confirm the in/out split?"

3. **Propose a TSK breakdown.** This is the creative core of BS-07. The Bootstrapper sketches:

   > "Initial TSK breakdown for phase 3:
   >
   > - **TSK-<N>** — Domain entity: <Entity from ft-X.md>. Entity + value objects + repository port. Unit tests.
   > - **TSK-<N+1>** — Use case: Create<Entity>. Application layer with port dependencies. Unit tests with fakes.
   > - **TSK-<N+2>** — Adapter: <Entity>Repository against <DB>. Migration + ORM model + mapper + repository implementation. Integration test.
   > - **TSK-<N+3>** — HTTP endpoint: POST /<entity>. Request/response schemas, authorization, validation.
   > - **TSK-<N+4>** — Authentication: <approach> — if not in phase 2.
   > - **TSK-<N+5>** — <Strategic asset capture mechanism>: <specifics from pd-motivation.md>
   > - **TSK-<N+6>** — <First user-facing flow end-to-end>: tying entity + use case + endpoint to deliver the MVP's first hypothesis test
   > - ...
   >
   > Total proposed: <count> TSKs. MVP scope typically lands at 8-15 TSKs in phase 3. Want to adjust?"

4. **Iterate.** Maintainer questions, suggests splits/merges, adds missing pieces. The Bootstrapper iterates the list openly in chat:

   > "Updated list with your changes:
   >
   > - <revised TSK 1>
   > - <revised TSK 2>
   > - <new TSK added>
   > - <TSK removed>
   > - ...
   >
   > Next iteration?"

   Iterate until the Maintainer confirms the list is stable.

5. **For each TSK, fill the required fields.** This is where the Bootstrapper applies the same capture-aloud pattern as earlier BS steps:

   > "Capturing TSK-<N>:
   >
   > - **What:** <concrete description, references to ft-*.md and ARCHITECTURE.md sections>
   > - **Why:** <reason this TSK exists where it is in the order>
   > - **Depends on:** <list of TSK IDs>
   > - **Acceptance:** <observable, testable criteria>
   >
   > Goes into phase-3-<slug>.md."

   For TSKs the Maintainer adds or significantly modifies, the Bootstrapper proposes the four fields and the Maintainer confirms or refines.

6. **Decide the phase 3 slug.** The phase file name is `phase-3-<slug>.md`. The slug reflects the MVP's core concept:

   > "Slug for the phase 3 file. Candidates:
   >
   > - `phase-3-<core-feature>.md` (e.g., 'phase-3-orders-mvp.md')
   > - `phase-3-<strategic-asset>.md` (e.g., 'phase-3-price-database-mvp.md')
   > - `phase-3-mvp.md` (generic but acceptable)
   >
   > Recommendation: <one based on what you captured>. Confirm?"

7. **Compose the final phase file.** A `~~~markdown` fenced block with the complete `phase-3-<slug>.md`:

   - Header: `# Phase 3 — <descriptive title>`, Goal, Milestone
   - **No** intro note about being a template starting point
   - Each TSK with the four fields plus `Status: ⬜ Not started`, `PR link: —`, `Learnings: —`
   - Retrospective placeholder at the end

8. **Wait for application.** Maintainer creates the file, confirms.

   > "Applied. Move to phase 4+ direction?"

### 7.5 — Direction for phase 4+ (10-15 minutes)

**Purpose:** Capture short paragraphs naming each post-MVP phase, into the `FUTURE_PHASES` marker of ROADMAP.md.

**Technique:**

1. **Probe for direction based on deferred features.**

   > "What was deferred from MVP scope (per 7.4 step 2):
   >
   > - <ft-A.md>: deferred
   > - <ft-B.md>: deferred
   > - <future doc>: 'Write DEPLOYMENT.md' was a 'Future documents' entry in BS-06
   >
   > Plus things implied by the strategic asset's growth path (pd-motivation.md mentioned <future capability>).
   >
   > How do these cluster into phases beyond the MVP?"

2. **Propose phase 4+ direction.** Concrete clustering, one paragraph per phase:

   > "Initial proposal for phase 4+:
   >
   > - **Phase 4 — <name>.** <One paragraph: what enters scope, what features it implements, what hypothesis it tests if applicable>.
   > - **Phase 5 — <name>.** <One paragraph>.
   > - **Phase 6 — <name>.** <One paragraph>.
   >
   > Adjust?"

3. **Confirm the list.** Iterate as needed. The number of phases is open — could be 1 (only one post-MVP phase), could be 5+. Most projects land at 3-6 post-MVP phases at this level of granularity.

4. **Special case: project that has no clear post-MVP direction.**

   If the Maintainer says "I don't know what's after MVP — depends on what we learn":

   > "Acceptable. I'll write the marker as: 'MVP is the full v1 commitment. Post-MVP direction will be set after MVP validation, via the Roadmap Planner workflow.' Want me to add 2-3 candidate directions even if uncommitted?"

   Adding 2-3 candidates is usually possible even when committed direction isn't.

5. **Compose the marker fill.** A `~~~markdown` fenced block with the content that replaces the `FUTURE_PHASES` marker block in ROADMAP.md:

   ```
   - **Phase 4 — <name>.** <One paragraph>.
   - **Phase 5 — <name>.** <One paragraph>.
   - ...
   ```

6. **Wait for application.**

### 7.6 — PROJECT_SPECIFIC_PREPROD_CHECKLIST (5-10 minutes)

**Purpose:** Fill the marker that BS-06 originally pointed to but that belongs here.

**Technique:**

1. **Present the universal items already in ROADMAP.md.**

   > "ROADMAP.md has a universal pre-production checklist: privacy register, encryption at rest, secrets management beyond env vars, incident response runbook, deployment doc, observability doc, pen test, backup, secrets rotation, on-call. The `PROJECT_SPECIFIC_PREPROD_CHECKLIST` marker is for items specific to your project's domain, regulation, scale, or business model."

2. **Probe by category, using BS-01..BS-06 signals.**

   > "Categories of project-specific items:
   >
   > - **Domain-specific compliance** — from `POLICY_CONSIDERATIONS.md` and SECURITY.md APPLICABLE_REGULATIONS. Candidates: <list LP entries that map to pre-prod items>.
   > - **Domain-specific operational readiness** — from `pd-workflows.md` operator workflows. Candidates: 'Operator training docs published', 'Support runbook ready', 'On-call rotation includes operators'.
   > - **Domain-specific data preparedness** — from `pd-motivation.md` strategic asset. Candidates: 'Initial <X> seed loaded and curated', 'Seed users provisioned'.
   > - **Integration-specific** — from ARCHITECTURE.md §7. Candidates: 'Provider <X> production credentials provisioned', 'Signed contracts with <key partners>'.
   > - **Legal-specific** — from POLICY_CONSIDERATIONS.md. Candidates: 'Privacy policy URL live', 'Terms-of-service URL live', 'Cookie consent banner deployed'.
   >
   > Which apply to your project?"

3. **Capture and compose.** If items surfaced:

   ```
   - [ ] <Item 1>
   - [ ] <Item 2>
   - ...
   ```

   If nothing project-specific:

   > "Marker stays explicitly empty with acknowledgment: 'Project-specific items: none identified at bootstrap; will be extended as the project's pre-production reality clarifies.' Apply?"

4. **Wait for application.**

### 7.7 — Verify outcomes, final ROADMAP.md consolidation, bridge (10-15 minutes)

**Purpose:** Close BS-07 cleanly. Walk Exit criteria. Compose the final `PHASE_OVERVIEW_PROJECT_PHASES` marker fill and the `phase-3-<slug>.md` entry in INDEX.md. Bridge to the end of the bootstrap.

**Technique:**

1. **Walk the Exit criteria checklist (Section 3).** Confirm each item.

2. **Compose the final `PHASE_OVERVIEW_PROJECT_PHASES` marker fill.**

   The phase overview table in ROADMAP.md needs the phase 3 row (and any later phase with a detailed phase file — usually none from BS-07, but if the Maintainer wanted to detail phase 4 inline, that row goes here too):

   > "Composing the phase overview table addition:
   >
   > ```
   > | **Phase 3** | <goal one-liner> | TSK-<start>..TSK-<end> | ⬜ Not started | [phase-3-<slug>.md](roadmap/phase-3-<slug>.md) |
   > ```
   >
   > Apply to the marker block in ROADMAP.md."

3. **Compose the INDEX.md phase entry.**

   > "And in `docs/INDEX.md`, the `PHASE_FILES` marker accepts:
   >
   > ```
   > - **[roadmap/phase-3-<slug>.md](roadmap/phase-3-<slug>.md)** — <one-line summary of the MVP phase> (TSK-<start>..TSK-<end>).
   > ```
   >
   > Apply inside the marker block. The marker stays in place for phase 4+ entries later."

4. **Both applies confirmed.** Once the Maintainer confirms, BS-07's substantive work is done.

5. **Bridge to the end of the bootstrap.** BS-07 is the last BS step:

   > "BS-07 is the last step of the bootstrap. After we close here, the bootstrap is complete. The next step is for you to open a separate session — a Claude Code or Claude desktop session, not this chat — and invoke `@.ai/workflows/TASK-PLANNER.md` to plan TSK-001. The Planner will read the repo state, read TSK-001 from `phase-1-foundation.md`, and generate the execution prompt for it.
   >
   > One thing to note: after this commit, the no-PR exception ends. TSK-001 onward follows the standard flow — branch, PR, signed commits, squash merge. See `@docs/engineering/GIT.md`."

6. **Commit BS-07.** See sub-block 11.4 below for the explicit commit guidance.

7. **Closing words.** Brief, but acknowledge the milestone:

   > "Bootstrap complete. <N> docs, <M> ADRs, <K> TSKs planned. You have everything to start executing. Take a breath."

---

## 8. Forcing functions specific to BS-07

The forcing functions from BOOTSTRAPPER section 10 and earlier BS step files apply across all BS steps. The following are calibrated for roadmap planning.

### 8.1 — When the Maintainer wants to validate phase 1/2 TSK-by-TSK

**Symptom:** During 7.2 or 7.3, the Maintainer wants to discuss each TSK individually, deeply, before moving on.

**Why it's a problem:** the Bootstrapper proposed an adapted TSK list based on accumulated decisions. The Maintainer revalidating each TSK turns a 20-minute sub-block into a 60-minute one, and risks overriding decisions that were already settled in BS-04/BS-05.

**Response pattern:**

> "I get the impulse, but phase 1/2 adaptation is **proposal + adjust**, not validate-each. The TSKs reflect decisions from BS-04 and BS-05 that you already made — I'm just sequencing them. If you want to revisit a specific decision (e.g., 'actually I don't want ruff'), that's a BS-04/BS-05 revision and rippling back. If you just want to confirm the TSK list makes sense, that's the 'adjust' step — focus on what to remove, add, or reorder, not on the details of each one.
>
> Specific concerns about specific TSKs?"

Refocus on additions/removals/reorderings. Resist deep dives into individual TSKs unless they surface a real BS-04/BS-05 revision.

### 8.2 — When phase 3 grows beyond 15 TSKs

**Symptom:** During 7.4, the TSK list for phase 3 keeps growing — 18, 22, 25 TSKs.

**Why it's a problem:** phase 3 is the MVP. A 25-TSK MVP is not an MVP. Phase 3 should deliver the smallest credible version of the product; anything beyond that is phase 4+.

**Response pattern:**

> "Phase 3 is at <count> TSKs. MVP phases typically land at 8-15. Let me probe what could be deferred:
>
> - **<TSK-X about feature Y>** — does the MVP's first 100 users actually need this, or can they validate the hypothesis without it?
> - **<TSK-Z about future-proofing>** — is this premature optimization? Could it land in phase 4 when load is real?
> - **<TSK-W about a transversal model>** — is this needed for the MVP path, or can the MVP run with a degraded version?
>
> Each one that can be deferred goes to phase 4+. Cut to MVP, not to feature-completeness."

Push back. MVP discipline is hard but it's the discipline that defines phase 3.

### 8.3 — When phase 3 stays under 6 TSKs

**Symptom:** During 7.4, the Maintainer wants a very minimal phase 3 — 4 or 5 TSKs.

**Why it's a problem:** depending on the product, 4-5 TSKs may be genuinely sufficient (e.g., a CLI tool with one core command). But for most AI-first backends, 4-5 TSKs is too thin to deliver a credible MVP — usually means transversal concerns (auth, observability beyond phase 2) are being deferred to "phase 3.5" implicitly.

**Response pattern:**

> "Phase 3 at <count> TSKs is light for an AI-first backend. Let me probe what might be missing:
>
> - **Authentication for the user-facing endpoints** — phase 2 set up the framework, but per-endpoint auth wiring is typically a phase 3 TSK
> - **The first LLM integration** — if your product is AI-first, the LLM adapter setup + first use case wiring may be a TSK
> - **The strategic asset capture mechanism** — pd-motivation.md said <X> accumulates; what's the TSK that writes the first row?
>
> Is any of this missing from phase 3, or are these landing in phase 2 or phase 4 explicitly?"

Force the question. If the answer is "phase 2" or "phase 4", great — make the boundary explicit. If the answer is "I forgot", surface it as a missing TSK.

### 8.4 — When the Maintainer can't articulate a TSK's `Why`

**Symptom:** During 7.2, 7.3, or 7.4, the Maintainer accepts a TSK but can't articulate why it exists where it is in the order.

**Why it's a problem:** the `Why` field captures the **rationale** for the TSK's position. Without it, future contributors (and the Maintainer in 3 months) won't know whether the TSK is core or incidental, whether reordering is safe, whether deferring it is OK.

**Response pattern:**

> "I'm proposing TSK-<N> here because <my hypothesis on why>. But you'll be the one executing it — does my Why match yours? If you'd articulate it differently, let me capture your version. If you don't have a strong opinion, my version stands but I want to flag: a Why you can't articulate today is a Why you won't trust in 3 months."

Push for ownership. Even small refinements to the Why help.

### 8.5 — When the Maintainer wants to mix phase 3 and phase 4 work

**Symptom:** During 7.4, the Maintainer says "let's also include feature X in phase 3, just in case".

**Why it's a problem:** phase 3 is the MVP. Including "just in case" features destroys the MVP discipline. The team will burn time on feature X before validating the MVP hypothesis.

**Response pattern:**

> "Feature X isn't in pd-mvp.md as MVP-critical. Two options:
>
> **A)** Keep feature X out of phase 3. Add to phase 4 direction (7.5). Defer until MVP validates.
> **B)** Add feature X to pd-mvp.md as MVP-critical, with rationale, and add the TSKs. This is a PDR-worthy revision of the MVP scope.
>
> Recommendation: **A** unless you can articulate why feature X is needed to test the MVP hypothesis. 'Just in case' is not enough rationale."

Default to A. Push back on scope creep.

### 8.6 — When phase 1 ordering breaks due to stack changes

**Symptom:** During 7.2, the Maintainer's stack (e.g., TypeScript + Bun) means TSK-003 (manifest) and TSK-004 (package manager) merge or swap order.

**Why it's a problem:** the template ordering assumes a typical Python or similar stack. Other stacks have different setup dependencies. Forcing the template order on a stack that doesn't fit creates broken intermediate states.

**Response pattern:**

> "Your stack changes the ordering. For TypeScript + Bun:
>
> - `package.json` + Bun install can be one TSK (they're inseparable)
> - The linter (ESLint or Biome) needs `tsconfig.json` first, so TSC config becomes TSK-005's prereq
> - <other adjustments>
>
> Proposed reordering for your stack:
>
> 1. <reordered TSK>
> 2. ...
>
> Confirm?"

Adapt the ordering deliberately. The template's ordering is wisdom, not law.

### 8.7 — When the Maintainer wants TSK-001 to add documentation

**Symptom:** The Maintainer wants TSK-001 to follow the template literally: "Add all documentation already written (`CLAUDE.md`, `docs/` contents, ...)".

**Why it's a problem:** with the commit-per-BS pattern, all documentation was committed to main during BS-01..BS-06 (and BS-07 itself, after this conversation). There's no documentation left to add in TSK-001.

**Response pattern:**

> "The commit-per-BS pattern (`@docs/engineering/GIT.md` → 'Exception: the bootstrap period') means all docs were committed during BS-01..BS-07. By the time TSK-001 runs, the docs are already on main.
>
> TSK-001 in this project reformulates to: 'Create the application folder skeleton — empty subdirectories matching `@docs/engineering/STRUCTURE.md`, with `.gitkeep` files. No documentation work — already committed during bootstrap.'
>
> Apply this reformulation?"

Confirm the reformulation. TSK-001 still exists, but its scope changed.

### 8.8 — When the Maintainer wants to plan phase 4+ in detail

**Symptom:** During 7.5, the Maintainer wants to break phase 4 into TSKs.

**Why it's a problem:** the BS-07 budget is bounded. Detailed phase 4+ planning is post-bootstrap work via the Roadmap Planner. Doing it now expands BS-07 unboundedly.

**Response pattern:**

> "Phase 4+ direction here is intentionally light — one paragraph per phase. Detailed TSKs come later via the Roadmap Planner, when phase 3 is closer to done and you have more information about what worked and what didn't.
>
> Detailed planning now would expand BS-07 by 60+ minutes and freeze decisions that benefit from MVP validation data. Resist the urge — direction-setters only.
>
> Anything specific to phase 4 you want to capture beyond the one-paragraph?"

Default to brief direction-setters. Resist phase 4+ detail.

### 8.9 — When the Maintainer wants to skip 7.6 (pre-prod checklist)

**Symptom:** After 7.5, the Maintainer is tired and wants to skip 7.6 ("we'll figure pre-prod later").

**Why it's a problem:** the `PROJECT_SPECIFIC_PREPROD_CHECKLIST` marker is a hard requirement. Without it, the pre-production posture is undefined and surprise items will surface near launch.

**Response pattern:**

> "7.6 is 5-10 minutes — quickest sub-block of BS-07. Skipping leaves the marker empty in a non-acknowledged way, which surfaces as a Category 5 unfilled marker the next time DOCS-VALIDATOR runs.
>
> Two options:
>
> **A)** Do it now, 5-10 minutes.
> **B)** Acknowledge explicitly as 'none identified at bootstrap' and apply that as the marker fill. Also 2 minutes.
>
> Either works, but the marker shouldn't stay an unfilled placeholder."

Push for (A) by default; accept (B) if (A) genuinely surfaces nothing.

### 8.10 — When BS-07 exceeds 130 minutes

**Symptom:** Two hours in and 7.4 isn't done.

**Why it's a problem:** BS-07 fatigue compounds. Phase 3 planning at hour 3 produces decisions the Maintainer will revisit in week 1.

**Response pattern:**

> "BS-07 is at <X> minutes. Most likely we're going deep on phase 3 TSK acceptance criteria. Two options:
>
> **A)** Push through with light touch — for remaining TSKs, capture What/Why/Depends on but leave Acceptance as one-liner. The Planner can refine Acceptance per TSK when it picks each up.
> **B)** Pause here. Save what's done. Resume tomorrow with phase 3 completion + the remaining sub-blocks.
>
> Recommendation: **A** if you have 30 minutes of energy left, **B** if you don't. Phase 3 partially done is a real state — we can resume cleanly."

Maintainer chooses. Phase 3 planning across two sessions is acceptable; forcing it through one session at low energy is not.

---

## 9. Pre-fill rules

The Bootstrapper does **not** consolidate at the very end of BS-07. Content is composed and applied **per sub-block**, file-by-file.

### 9.1 — Capture-aloud during sub-block discussion

Same fundamental pattern. The destination is the file being produced in the current sub-block:

**Format:**

> "Capturing for `<file>`, TSK-<N>: <What> / <Why> / <Depends on> / <Acceptance>. Goes into the <sub-block> consolidation."

**Examples:**

- "Capturing for `phase-1-foundation.md`, TSK-005: Configure ruff. Add ruff to dev group. Configure `[tool.ruff]` in pyproject.toml: line length 100, target py313, ruleset E/F/I/N/UP/B/SIM/RUF, per-path exceptions for tests/. First check passes. Why: linter is fastest feedback loop. Depends on: TSK-004. Acceptance: ruff check and ruff format --check both pass on empty repo. Goes into 7.2 consolidation."

- "Capturing for `phase-3-orders-mvp.md`, TSK-024: Domain entity Order. Create entity, OrderStatus enum, value objects (OrderTotal, OrderRef). Unit tests. Why: foundational entity for all later TSKs in phase 3. Depends on: TSK-023 (auth context). Acceptance: 100% line coverage on the entity; status transitions enforce invariants. Goes into 7.4 consolidation."

**Frequency:** 3-12 captures per sub-block. Heaviest in 7.4 (one per TSK).

### 9.2 — The sub-block consolidation block

When the discussion for a sub-block is complete, the Bootstrapper composes the final content as a fenced markdown block:

**For 7.2 and 7.3 (phase 1 and phase 2 adaptation):** the consolidation is the **whole phase file content**. The Maintainer replaces the existing template file with the new content.

**For 7.4 (phase 3 planning):** the consolidation is a **new file**. The Maintainer creates `phase-3-<slug>.md`.

**For 7.5 (FUTURE_PHASES) and 7.6 (PROJECT_SPECIFIC_PREPROD_CHECKLIST):** the consolidation is **marker-only** — just the content that replaces the marker block in ROADMAP.md.

**For 7.7 (final ROADMAP.md consolidation):** the consolidation is **marker-only** for `PHASE_OVERVIEW_PROJECT_PHASES` in ROADMAP.md + **marker-amendment** for `PHASE_FILES` in INDEX.md.

### 9.3 — PYTHON_REFERENCE_EXAMPLE block removal

In sub-blocks 7.2 and 7.3, the Bootstrapper produces phase files **without** PYTHON_REFERENCE_EXAMPLE comment blocks. The concrete content for the project's actual stack is inlined directly into the `What` and `Acceptance` fields of each TSK.

This is intentional: after BS-04 the stack is pinned. The template's reference examples were there to help the Bootstrapper calibrate; post-BS-07 they're noise.

If the project is Python+FastAPI+uv+Postgres (matching the template's reference): the inlined content **looks similar to the PYTHON_REFERENCE_EXAMPLE** but is written as the actual TSK content, not as a reference block.

If the project is anything else: the inlined content is written from scratch in the appropriate idiom.

### 9.4 — TSK numbering continuity

TSK numbers are sequential across phases:

- Phase 1 ends at TSK-012 (or wherever the adapted phase 1 ends — could be TSK-009 if the project removed 3 TSKs, or TSK-015 if the project added 3 TSKs)
- Phase 2 starts at the next number after phase 1 ends
- Phase 3 starts at the next number after phase 2 ends

The Bootstrapper computes the start number for each phase based on the previous phase's adapted count. When phase 1 is adapted in 7.2, the Bootstrapper notes the final TSK count and uses it to anchor phase 2's starting number in 7.3.

### 9.5 — Cross-references to docs in TSK fields

TSK fields reference earlier docs explicitly:

- **What:** may reference `ft-X.md` (for phase 3 TSKs implementing a feature)
- **Why:** may reference ARCHITECTURE.md §N (for ordering rationale)
- **Depends on:** references other TSK IDs only (not docs)
- **Acceptance:** may reference SECURITY.md (for security gates), TESTING.md (for test coverage), CODE_STYLE.md (for naming patterns)

These cross-references help the Planner load the right context when generating execution prompts. The Bootstrapper adds them inline during consolidation.

### 9.6 — Decisions Captured at the end of BS-07

BS-07 is the last BS step. There are no "Decisions Captured for the next step" because there is no next step. Instead, BS-07 captures **Decisions Captured for the post-bootstrap Planner**:

- TSK-001 starting state (what's already committed, what TSK-001 must produce)
- Cross-phase dependencies that the Planner should be aware of (e.g., "Phase 3 assumes phase 2's auth wiring; if auth slipped to phase 3, revisit")
- Any deferred-marker decisions from BS-06 that became TSKs in BS-07

These are surfaced in the bridge (7.7) as a closing recap.

---

## 10. Required outcomes (checklist for closing BS-07)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step. The checklist mirrors Section 3 (Exit criteria) but reformatted for the closing conversation.

### 10.1 — Outcomes that must be true to close BS-07

#### Phase files

- [ ] `phase-1-foundation.md` adapted, PYTHON_REFERENCE_EXAMPLE blocks removed, intro note removed
- [ ] `phase-2-infrastructure.md` adapted, PYTHON_REFERENCE_EXAMPLE blocks removed, intro note removed
- [ ] `phase-3-<slug>.md` created with project-specific MVP TSKs
- [ ] All TSKs across the three phases have substantive `What`, `Why`, `Depends on`, `Acceptance`
- [ ] TSK numbering is sequential and continuous
- [ ] Phase 3 TSK count is calibrated (8-15 typical; deviations justified)

#### ROADMAP.md markers

- [ ] `PHASE_OVERVIEW_PROJECT_PHASES` filled with phase 3 row
- [ ] `FUTURE_PHASES` filled with direction-setters or explicit acknowledgment
- [ ] `PROJECT_SPECIFIC_PREPROD_CHECKLIST` filled or explicit acknowledgment

#### INDEX.md

- [ ] `phase-3-<slug>.md` entry added inside `PHASE_FILES` marker (marker stays in place for phase 4+)

#### Anchor questions answered

- [ ] **Anchor 1 (Phase 1 reflects actual foundation)** — confirmed in 7.2
- [ ] **Anchor 2 (Phase 2 reflects actual infrastructure)** — confirmed in 7.3
- [ ] **Anchor 3 (Phase 3 delivers MVP)** — confirmed in 7.4
- [ ] **Anchor 4 (Direction beyond MVP)** — confirmed in 7.5

#### Bootstrap end state

- [ ] Maintainer knows the next step is invoking `@.ai/workflows/TASK-PLANNER.md` in a separate session for TSK-001
- [ ] Maintainer acknowledges that PR discipline starts now (no more direct-to-main commits)

### 10.2 — When a required outcome is unmet

Same options as earlier BS steps:

**Option A — Cover it now.** Pick up the conversation thread on the gap.

**Option B — Defer it with structure.** Document the gap explicitly:
- Missing phase 3 TSK → add as an Open question in `phase-3-<slug>.md` retrospective area, surface to first Planner session
- Empty FUTURE_PHASES → fill with "TBD — set after MVP validation" if genuinely unknown
- Missing pre-prod item → mark as "TBD — surface during phase 4 planning"

The Bootstrapper recommends Option A for hard requirements. Option B is for genuine unknowns.

### 10.3 — What's explicitly NOT required to close BS-07

To prevent perfectionism:

- Phase 4+ detailed phase files — post-bootstrap Roadmap Planner work
- Production-grade acceptance criteria for every TSK — first-pass acceptance is sufficient
- Execution prompts under `.ai/prompts/execution/` — those come from TASK-PLANNER per TSK
- Code, configs, CI YAML, Dockerfile — those come from executing TSK-001 onward
- Sub-task decomposition (TSK-NNN.MM) — that happens at the Planner level, per-task

The Bootstrapper resists pulling post-bootstrap work into BS-07.

---

## 11. Bridge to end of bootstrap

When the Required outcomes checklist is complete, the Bootstrapper transitions to the end of the bootstrap. There is no BS-08 — BS-07 is the last step.

### 11.1 — Recap

Three or four sentences:

> "BS-07 closed the roadmap. `phase-1-foundation.md` has <N1> adapted TSKs. `phase-2-infrastructure.md` has <N2> adapted TSKs. `phase-3-<slug>.md` is created with <N3> MVP TSKs grounded in `pd-mvp.md`. ROADMAP.md phase overview, future phases direction, and project-specific pre-prod items are all filled. INDEX.md references phase 3. Total <N1+N2+N3> TSKs planned in detail; phase 4+ direction-setters in place for the Roadmap Planner to detail post-MVP."

### 11.2 — Confirm Decisions Captured for the post-bootstrap Planner

Recap closing context:

> "Closing context for the first Planner session:
>
> - **TSK-001 starting state**: docs are already on main (committed during bootstrap). TSK-001 creates only the application folder skeleton, no documentation work.
> - **Cross-phase dependencies the Planner should be aware of**: <list any specific signals, e.g., 'phase 3 assumes phase 2 wired auth'>
> - **Deferred markers from BS-06**: <list any markers that the Maintainer chose to address as TSKs rather than fill in BS-06, with the target TSK noted>"

### 11.3 — Preview the post-bootstrap workflow

Two or three sentences:

> "From here, every TSK follows the standard flow:
>
> 1. You open a new session (Claude desktop or Claude Code) and invoke `@.ai/workflows/TASK-PLANNER.md` for the next TSK.
> 2. The Planner reads the repo state, reads the TSK from its phase file, and generates an execution prompt saved at `.ai/prompts/execution/<phase-folder>/TSK-<NNN>.md`.
> 3. You open the execution prompt in a Claude Code session, run it, review the diff, and open a PR.
> 4. Merge after CI passes and review. Update the TSK's Status and PR link in the phase file.
>
> If at any point a structural change is needed (renumbering, new phase, convention change), invoke `@.ai/workflows/ROADMAP-PLANNER.md` instead — that's the post-bootstrap roadmap evolution workflow."

### 11.4 — Commit BS-07 before pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

This is the **last** direct-to-main commit. After this, every change goes through the standard PR flow.

Before pausing (or moving to the first Planner session), the Maintainer commits the work BS-07 produced:

```
git add docs/ROADMAP.md \
        docs/roadmap/phase-1-foundation.md \
        docs/roadmap/phase-2-infrastructure.md \
        docs/roadmap/phase-3-<slug>.md \
        docs/INDEX.md

git commit -S -m "docs(bootstrap): complete BS-07 — roadmap"
git push origin main
```

The exact file list is fixed for BS-07 — the four roadmap files plus INDEX.md.

After the commit lands, the bootstrap is **complete**. The Maintainer proceeds to the post-bootstrap workflow per 11.3.

### 11.5 — Closing the conversation

The Bootstrapper does not roll into a "what's next" pitch. The bootstrap chat ends here.

> "Bootstrap complete. Everything is in main. Next step: open a fresh session and invoke `@.ai/workflows/TASK-PLANNER.md` for TSK-001. This chat can be closed.
>
> Take a moment — you just specified an entire AI-first backend project in detail. The next phase is execution."

### 11.6 — If the Maintainer wants to pause before committing BS-07

If the Maintainer is exhausted and wants to pause before the final commit:

> "Understood. A few things before we pause:
>
> 1. The work is in the docs already — phase files are applied, ROADMAP.md markers are filled, INDEX.md references phase 3. They're not lost, just uncommitted.
> 2. When you come back, the commit in 11.4 is the only thing left to close BS-07. Run it, push, and you're done.
> 3. The fresh session for TASK-PLANNER comes after the commit.
>
> Take the break. The bootstrap is essentially done — just the commit remains."

---

## 12. Notes for the Bootstrapper LLM running BS-07

These are operational reminders specific to BS-07, on top of universal BOOTSTRAPPER guidance and earlier BS step files' Section 12 notes.

### 12.1 — Phase 1 and 2 are reference, not gabarito

The template's phase-1 and phase-2 files exist as a **reference of quality and ordering**. They are not a fixed plan that the Maintainer validates.

In 7.2 and 7.3, the Bootstrapper:

- **Uses the template's ordering** as the default — it's been thought through
- **Inlines the project's stack** in each TSK's What and Acceptance — removing PYTHON_REFERENCE_EXAMPLE blocks
- **Adapts the TSK count** based on the project's actual needs (remove rejected tools, add chosen ones)
- **Reorders only when the stack requires it** — most stacks fit the template ordering with stack swaps

The Maintainer's role in 7.2/7.3 is to **react to the proposal**: confirm, add, remove, reorder. Not to walk each TSK in isolation.

### 12.2 — Phase 3 is co-authored, not proposed

In 7.4, the Bootstrapper does NOT come in with a pre-built TSK list. Phase 3 emerges from conversation, grounded in pd-mvp.md and ft-*.md.

The Bootstrapper proposes a starting breakdown to anchor the conversation, but the Maintainer is expected to engage actively — adding TSKs, splitting big ones, deferring others. The dynamic is collaborative, not validate-the-proposal.

This is why 7.4 is the densest sub-block. Plan for 25-40 minutes.

### 12.3 — Decisions Captured is the BS-07 superpower

By BS-07, the Bootstrapper holds the largest Decisions Captured register of any step. **Use it ruthlessly.**

When proposing TSKs in 7.2/7.3:
- Don't ask "what linter?" — BS-05 chose it; use that name
- Don't ask "what test runner?" — BS-04 chose it; use that name
- Don't ask "what auth library?" — BS-04 chose it; use that name

Ask only what hasn't been decided. The pre-existing decisions go straight into the TSK content.

If the Maintainer revises a Decision Captured during BS-07 (e.g., "actually let's use a different linter"), surface the ripple:

> "Changing the linter affects TSK-005 What and Acceptance, TSK-007 pre-commit hooks, TSK-010 CI steps. Apply the change everywhere or just here?"

Apply the ripple per BOOTSTRAPPER section 11.

### 12.4 — Resist completionism on TSK acceptance criteria

Each TSK has an Acceptance field. The temptation is to write production-grade acceptance criteria for every TSK.

**Resist.** The Planner refines acceptance per TSK when generating its execution prompt. Bootstrapper-level acceptance is **first-pass**: enough to verify the TSK is done, not enough to specify every edge case.

Good first-pass acceptance:
- "Linter check and format check both pass on the empty repo."
- "Migration generates a file with timestamp-prefixed name; upgrade succeeds on fresh database; downgrade also works."
- "Domain entity passes unit tests covering creation, status transitions, and invariant enforcement."

Bad first-pass acceptance (too detailed):
- "Linter check produces no warnings on lines 1-100 of every file in app/, with specific config rules applied per directory, and CI fails if any rule is violated, except for tests/ which has a different ruleset..."

The Planner takes the first-pass acceptance and expands it during execution prompt generation. Don't pre-expand it.

### 12.5 — Phase 4+ direction is one paragraph each

In 7.5, each post-MVP phase gets **one paragraph**. Not bullet lists, not TSK previews, just a paragraph describing what enters scope.

Format:

> "- **Phase 4 — Threads and comments.** Adds threaded discussion to each Order. Buyers can comment on quotes, suppliers can reply, operators can intervene. No real-time notifications yet (deferred to phase 5). Tests the hypothesis that conversation depth on orders increases repeat usage."

That level of detail is right. Bullets of TSKs is too much.

### 12.6 — TSK-001 reformulation is non-negotiable

Per forcing function 8.7, TSK-001's template content ("Add all documentation already written") is **incorrect** for projects following the commit-per-BS pattern. Documentation is already committed.

The Bootstrapper reformulates TSK-001 to: "Create the application folder skeleton — empty subdirectories matching `@docs/engineering/STRUCTURE.md`, with `.gitkeep` files. No documentation work."

This reformulation happens in 7.2 automatically. The Bootstrapper doesn't ask permission — it's a direct consequence of the commit-per-BS convention, which is project-wide.

### 12.7 — Pacing

BS-07 typically takes 80-130 minutes:

- **First 10-15 minutes**: opening, recap, pattern announcement (7.1)
- **Middle 60-100 minutes**: sub-blocks 7.2 through 7.6 — phase adaptations, phase 3 planning, direction, pre-prod
- **Final 10-15 minutes**: outcomes verification, final ROADMAP.md consolidation, bridge, commit (7.7 + 11.4)

If conversation runs long (130+ minutes), offer split. Natural pause points:

- After 7.2 (phase 1 done)
- After 7.3 (phase 2 done)
- Inside 7.4 (phase 3 partially planned — resume with TSK list)

Phase 3 split is the most common. Half phase 3 in session A, half in session B. Acceptable.

### 12.8 — When the Maintainer's MVP scope changed since BS-01

Sometimes BS-07's 7.4 surfaces that the MVP scope has materially shifted since BS-01 wrote pd-mvp.md.

**Symptom:** the TSKs the Maintainer wants in phase 3 don't match pd-mvp.md hypotheses or distribution path.

**Response pattern:**

> "What you're describing for phase 3 doesn't match `pd-mvp.md` exactly. Either:
>
> **A)** Update `pd-mvp.md` to reflect the current MVP scope. This is a PDR-worthy revision. We can capture the PDR post-bootstrap, but flagging it now means the docs stay coherent.
> **B)** Keep `pd-mvp.md` as-is and treat phase 3 as a deliberate scope expansion. Note the discrepancy explicitly.
>
> Recommendation: **A**. MVP scope shifts deserve a PDR. We'll continue phase 3 planning with the new scope, and you'll write the PDR after the bootstrap closes."

Don't block on the PDR. Continue, flag it.

### 12.9 — End of bootstrap is the end of the chat

The bootstrap chat ends with BS-07's commit. The Bootstrapper does NOT continue the conversation into "shall we plan TSK-001 now?". TSK-001 is a fresh session.

This is structural: the chat that ran the bootstrap is full of Decisions Captured, recaps, and convention discussions. The chat that plans TSK-001 should be focused on TSK-001. Mixing them dilutes both.

The Bootstrapper's last words in 11.5 are the literal end:

> "Bootstrap complete. Take a moment. Next step: fresh session for TASK-PLANNER."

No "do you have any more questions?". The chat closes.

### 12.10 — BOOTSTRAP markers in ROADMAP.md and phase files may reference old step names

Same pattern as earlier BS step files' §12.10: the markers in ROADMAP.md may reference `BS-06-docs-map.md` instead of `BS-07-roadmap.md` (a known stale reference from before BS-06/BS-07 split was finalized).

The Bootstrapper:

- Ignores the stale step name references when interpreting marker intent
- Replaces the marker block entirely during application
- Does not "fix" old references as a side mission
- Trusts DOCS-VALIDATOR Category 6 to catch and fix these post-BS-07 if any remain (rare — most are replaced during BS-07 marker fills)

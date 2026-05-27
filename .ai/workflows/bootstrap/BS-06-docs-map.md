# BS-06 — Docs map

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-06 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the documentation map validation conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern), this file for the specific content of BS-06, and the output of BS-01 through BS-05 (everything in `docs/` and `.ai/`) as input to this step.

---

## 1. Goal

Validate that the project's two INDEX files (`docs/INDEX.md` and `.ai/INDEX.md`) reflect the actual state of the documentation after BS-01 through BS-05. The bulk of the work was done by earlier steps; BS-06 is the cleanup checkpoint where the navigation layer catches up with the content that accumulated.

The step is split into three phases:

- **Phase A** — `docs/INDEX.md`: fill the BOOTSTRAP markers populated during BS-01 through BS-05 (PD docs list, FT docs list, ADR list, project-specific "By task" entries, project-specific "By topic" entries), confirm the "Future documents" section reflects the project's actual intent, validate the body sections still apply.
- **Phase B** — `.ai/INDEX.md`: lighter pass. The file is mostly static (workflows and prompts structure is project-independent), but specific projects may have added workflows or modified the prompts folder. Confirm.
- **Phase C** — Hand off to DOCS-VALIDATOR: the Bootstrapper instructs the Maintainer to invoke `@.ai/workflows/DOCS-VALIDATOR.md` in a separate Claude Code session. DOCS-VALIDATOR handles every mechanical check (broken links, orphan files, stale section refs, cross-folder refs, naming inconsistencies) automatically. The Maintainer returns with the validator's report and the Bootstrapper helps interpret any approval-required items or gaps.

ROADMAP.md is **not** touched in BS-06. Its three bootstrap markers (`PHASE_OVERVIEW_PROJECT_PHASES`, `FUTURE_PHASES`, `PROJECT_SPECIFIC_PREPROD_CHECKLIST`) are all roadmap-planning concerns and belong to BS-07. Marker descriptions inside ROADMAP.md that point to `BS-06-docs-map.md` are stale references from before the BS-06/BS-07 split was finalized; DOCS-VALIDATOR (Phase C) flags these as Category 6 issues and BS-07 replaces the marker blocks at fill time.

BS-06's role with respect to ROADMAP.md is observational only: if the Phase C validator report surfaces ROADMAP-related issues, the Bootstrapper acknowledges them and passes them forward to BS-07 as Decisions Captured.

BS-06 is a **judgment + handoff** step. The Bootstrapper handles what only a human can answer (which entries to add to "By task", how to extend "By topic" with project-specific topics, whether a deferred doc should still be in "Future documents"). The validator handles everything mechanical.

By the end of BS-06, opening `docs/INDEX.md` gives an accurate map of the project's documentation. Every cross-reference resolves. Every BOOTSTRAP marker is either filled with content or consciously left for a future step.

---

## 2. What this step produces

### Documents updated

- **`docs/INDEX.md`** — every BOOTSTRAP marker that BS-01 through BS-05 was responsible for populating gets reviewed and filled (or left intentionally empty with the Maintainer's acknowledgment):
  - `PD_DOCS` — list of `docs/product/pd-*.md` files created in BS-01
  - `FT_DOCS` — list of `docs/product/ft-*.md` files created in BS-02
  - `ADR_LIST` — list of ADRs accepted during BS-03/BS-04/BS-05
  - `PDR_LIST` — list of PDRs (typically empty at bootstrap time, but check)
  - `LDR_LIST` — list of LDRs (typically empty at bootstrap time, but check)
  - `LEGAL_DOCS` — additional legal documents beyond `POLICY_CONSIDERATIONS.md` (typically empty)
  - `PHASE_FILES` — left empty at BS-06 (BS-07 fills phase-3+ entries)
  - `TASK_ENTRIES_EXTENSIONS` — project-specific "By task" entries that surfaced during BS-02/BS-05
  - `TOPIC_ENTRIES_EXTENSIONS` — project-specific "By topic" rows

  Fixed sections (`How to read this doc`, `By area` structure, `By task` universal entries, `By topic` universal table, `Future documents`) reviewed and adjusted only where the project's reality demands it.

- **`.ai/INDEX.md`** — lighter pass. The file is mostly static (workflows + prompts structure is project-independent). Confirm:
  - The workflows listed match the actual `.ai/workflows/` content
  - The bootstrap step files (BS-01 through BS-07) are listed correctly
  - Any project-added workflow (rare during bootstrap) is reflected

### Documents potentially modified by DOCS-VALIDATOR (run in Phase C)

After Phases A and B complete, DOCS-VALIDATOR runs in a separate Claude Code session. It may modify:

- Any doc with broken or stale references (renames, moves, section renumberings)
- The two INDEX files to add orphan-file references (only with Maintainer approval per the validator's protocol)
- Tree diagrams in `docs/engineering/STRUCTURE.md` if they drifted

The Maintainer returns with the validator's report. The Bootstrapper helps interpret the report but does NOT re-do the work the validator did.

### What is NOT produced in BS-06

- New ADRs, PDRs, LDRs — these are added when decisions are made, not in a docs map step
- Phase planning, task lists — BS-07 (Roadmap)
- Application code — bootstrap doesn't write code
- New convention docs or feature docs — those rounds are over (BS-02 / BS-05)
- Future docs that don't yet exist — they stay as "Future documents" pointers in INDEX.md

### Critical premise — INDEX.md is the project's contributor entry point

After bootstrap, every contributor (human or LLM agent) reads `docs/INDEX.md` first when orienting in the project. If INDEX.md drifts from reality (lists docs that don't exist, omits docs that do, has stale references), every contributor's first impression is "this project's docs aren't trustworthy."

BS-06 is the moment to make INDEX.md trustworthy.

---

## 3. Exit criteria

BS-06 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

#### docs/INDEX.md

- [ ] `PD_DOCS` marker filled with the actual `pd-*.md` files created in BS-01 (each with the project-specific one-line description, not the placeholder)
- [ ] `FT_DOCS` marker filled with the actual `ft-*.md` files created in BS-02, each with a project-specific one-line description
- [ ] `ADR_LIST` marker filled with the ADRs accepted during BS-03/BS-04/BS-05, or explicitly empty if none were created
- [ ] `PDR_LIST`, `LDR_LIST`, `LEGAL_DOCS` markers reviewed (typically empty at bootstrap time; explicit acknowledgment if intentionally empty)
- [ ] `PHASE_FILES` marker left empty — BS-07 fills it (acknowledged explicitly)
- [ ] `TASK_ENTRIES_EXTENSIONS` marker filled with project-specific "By task" entries from BS-05 signals, or explicitly empty
- [ ] `TOPIC_ENTRIES_EXTENSIONS` marker filled with project-specific "By topic" rows, or explicitly empty
- [ ] "Future documents" section reviewed against the project's actual intent — items not relevant to the project removed, items the project will definitely need confirmed

#### .ai/INDEX.md

- [ ] Workflows list matches actual `.ai/workflows/` content (no extras, no omissions)
- [ ] Bootstrap step files (BS-01 through BS-07) listed correctly
- [ ] Any project-added workflow reflected (rare)
- [ ] Cross-references between sections are accurate

#### DOCS-VALIDATOR handoff

- [ ] Maintainer has invoked `@.ai/workflows/DOCS-VALIDATOR.md` in a Claude Code session
- [ ] Maintainer has returned with the validator's report (auto-fixes applied, approval-required items resolved, deferred items noted)
- [ ] Any genuine content gaps surfaced by the validator (Category 5 unfilled markers) are either resolved in this session or consciously deferred with notes
- [ ] Any approval-required items (orphan files, true orphan links) are resolved or consciously deferred

### Soft requirements (worth pushing for but not blockers)

- [ ] Cross-references between INDEX.md and the docs it indexes have been spot-checked
- [ ] The "By task" entries reflect the actual scenarios the team will encounter (project-specific scenarios added where they apply)
- [ ] The "By topic" rows include project-specific topics (domain entities, feature names, regulated areas) where useful

### What does NOT block exit

- BOOTSTRAP markers in other docs (ARCHITECTURE.md, CODE_STYLE.md, SECURITY.md, etc.) that were left empty in earlier BS steps — those gaps are owned by earlier steps; BS-06 does not retroactively fill them, but the DOCS-VALIDATOR report surfaces them so the Maintainer is aware
- Future docs not yet created (DATA_MODEL.md, API_CONVENTIONS.md, etc.) — they stay as "Future documents" pointers; creation happens when the need is concrete
- Phase 3+ task files — BS-07's scope

The Bootstrapper does not advance to BS-07 silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before moving on, the FT_DOCS marker is still the placeholder. We created <N> ft-*.md files in BS-02 that need to be listed. Cover now or defer?"

---

## 4. Anchor questions

These are the three questions that **must be answered with confidence** before BS-06 closes. They are not asked in order — they emerge through the phase walk (see Section 7 — Conversation flow). The Bootstrapper keeps mental track of which ones are still open, and surfaces any that remain unanswered before declaring the step complete.

### Anchor 1 — Does docs/INDEX.md list every doc that actually exists?

This becomes the validated state of the BOOTSTRAP markers in `docs/INDEX.md`. The Maintainer must confirm:

- Every `pd-*.md` created in BS-01 is listed in `PD_DOCS`
- Every `ft-*.md` created in BS-02 is listed in `FT_DOCS` with a one-line description
- Every ADR created in BS-03/BS-04/BS-05 is listed in `ADR_LIST`
- Any deferred-to-PDR feature noted explicitly (in the relevant `pd-*.md`, surfaced in INDEX through cross-references)

Forcing function: **a new contributor opening INDEX.md should see every doc that lives in the repo and know what each one covers**. If a doc exists but isn't in INDEX.md, it's invisible.

### Anchor 2 — Does "By task" reflect the scenarios the team will actually encounter?

This becomes the `TASK_ENTRIES_EXTENSIONS` marker. Beyond the universal entries (when planning, when starting, when adding a feature, etc.), the project may have domain-specific scenarios that recur:

- "When curating the catalog" — for products with operator curation
- "When designing an LLM-driven interaction" — for AI-heavy products
- "When handling a specific compliance event" — for regulated industries
- "When onboarding a new operator" — for hybrid-operator products

The Maintainer must commit to at least the project-specific scenarios that surfaced during BS-02 and BS-05. Absent project-specific scenarios is acceptable (the universal entries cover most common ground), but absence should be conscious, not by omission.

### Anchor 3 — Does the project's "Future documents" list reflect actual intent?

Note: "Future documents" lives at the bottom of `docs/INDEX.md` and is a docs-map concern (which engineering docs the project will write next). It is unrelated to ROADMAP.md "Future phases", which lists product-phase direction and belongs to BS-07.

The template ships with a default list (DATA_MODEL.md, API_CONVENTIONS.md, AI_INTEGRATION.md, OBSERVABILITY.md, DEPLOYMENT.md, RELEASE.md, runbooks/INCIDENT_RESPONSE.md). Each item should be:

- **Kept** if the project will definitely need it before production
- **Adjusted** if the project will need a similar doc with a different name or scope
- **Removed** if the project genuinely won't have that concern (e.g., a CLI tool may not need API_CONVENTIONS.md)
- **Added** if the project surfaced a future doc need not in the default list (e.g., `INFERENCE_TUNING.md` for an AI-heavy product)

Forcing function: **every entry in "Future documents" represents a deferred commitment to write it**. Items the project won't actually need add noise; missing items the project will need create surprise later.

---

## 5. How BS-06 relates to BS-05 and BS-07

BS-06 uses a **phased persistence pattern** distinct from earlier BS steps:

- **Phase A** (docs/INDEX.md) and **Phase B** (.ai/INDEX.md): Bootstrapper consolidates per file (BS-02 pattern — one consolidation moment per file). The Maintainer reviews the consolidated content in chat, applies it to the repo, and confirms before the next phase begins.
- **Phase C** (DOCS-VALIDATOR handoff): the Bootstrapper hands off to a separate Claude Code session and waits. When the Maintainer returns with the validator's report, the Bootstrapper helps interpret but does not re-apply.

### Why this pattern

BS-06 spans two indexed files plus whatever the validator touches. Trying to do it section-by-section like BS-03/BS-04 would produce too many micro-applies. Trying to do it as a single grand consolidation like BS-02 would lose the natural break between INDEX files (which are independent) and the validator handoff (which is async).

Two explicit phases + handoff:

- Phase A is the substantive work (10-20 minutes)
- Phase B is fast confirmation (3-5 minutes)
- Phase C is async — the Bootstrapper's part is just instructing the handoff and interpreting the return

### What carries over from BS-02 (single-consolidation pattern)

- **Capture-aloud anchors** during discussion of each marker — short-lived chat state until the consolidation moment
- **Consolidated final block per file** at the moment of application — Maintainer copies and confirms
- **No section-by-section apply** within a single INDEX.md visit — too granular for a navigation file

### What differs from all earlier BS steps

- **Phase D is async.** The Bootstrapper instructs and waits, then interprets when the Maintainer returns. This is the first time in the bootstrap that work happens outside the Bootstrapper's chat — by design, since the validator is a separate workflow run by a different role (Claude Code).
- **No new content invention.** BS-06 catalogues and references; it does not invent. If the project needs a new doc that wasn't created in BS-01..BS-05, that's a gap to surface, not a BS-06 task.
- **No ADRs expected.** Docs maps don't generate architectural decisions. If something significant surfaces, it's most likely a PDR (product implication) or LDR (legal implication) — but typically zero new decision records emerge from BS-06.

### What the Maintainer needs at the keyboard

For Phases A-C: same as BS-02 (editor on the target file, ready to apply consolidations).

For Phase D: a **separate Claude Code session** ready to invoke `@.ai/workflows/DOCS-VALIDATOR.md`. The Bootstrapper instructs explicitly when to switch.

---

## 6. Coverage strategy

BS-06's coverage is **fixed by the three phases**. The Bootstrapper walks them in order:

| Phase | Target | Density | Time | Why this position |
|---|---|---|---|---|
| A | `docs/INDEX.md` | Medium | 10-20 min | The substantive work. Markers BS-01..BS-05 populated need filling, "By task" and "By topic" need project-specific entries, "Future documents" needs validation. |
| B | `.ai/INDEX.md` | Light | 3-5 min | Mostly static. Confirm workflows list and step files. Self-indexing of DOCS-VALIDATOR was already done; just check. |
| C | DOCS-VALIDATOR handoff | Variable | 5-30 min wall-clock, 5-10 min Bootstrapper conversation | Async. Bootstrapper instructs, Maintainer runs separately, returns with report. Bootstrapper interprets. |

**Total Bootstrapper conversation time: 25-45 minutes**, with DOCS-VALIDATOR adding 10-30 minutes of separate-session work the Maintainer does outside this chat.

### Phase A: markers in docs/INDEX.md

The Bootstrapper walks each marker that BS-01..BS-05 was supposed to populate:

| Marker | Source step | Expected state at BS-06 start |
|---|---|---|
| `PD_DOCS` | BS-01 | 7 placeholder entries (one per dimension). Should now reflect the 7 files actually created in BS-01. |
| `FT_DOCS` | BS-02 | Placeholder (format description only). Should now list 4-15 actual ft-*.md files. |
| `ADR_LIST` | BS-03/BS-04/BS-05 | Placeholder. May be empty (no ADRs created) or list 0-5+ ADRs. |
| `PDR_LIST` | (not bootstrap-time) | Placeholder. Almost always empty at bootstrap. |
| `LDR_LIST` | (not bootstrap-time) | Placeholder. Almost always empty at bootstrap. |
| `LEGAL_DOCS` | (not bootstrap-time) | Placeholder. Almost always empty at bootstrap. |
| `PHASE_FILES` | BS-07 | Placeholder. Stays empty until BS-07. |
| `TASK_ENTRIES_EXTENSIONS` | BS-06 (this step) | Placeholder. May get 1-3 entries based on project-specific scenarios. |
| `TOPIC_ENTRIES_EXTENSIONS` | BS-06 (this step) | Placeholder. May get 3-10 rows for project-specific topics. |

### Phase B: .ai/INDEX.md

Mostly confirmation. Three things to check:

1. The workflows list matches `.ai/workflows/` directory contents (DOCS-VALIDATOR is already self-indexed there, per the DOCS-VALIDATOR completion that ran during template development; a fresh project starting from this template inherits that self-index).
2. The bootstrap step files (BS-01 through BS-07) are listed. BS-07 is listed even though it hasn't run yet (the file exists in `.ai/workflows/bootstrap/`).
3. No project-added workflows that need indexing (unusual at bootstrap, but possible).

### Phase C: DOCS-VALIDATOR handoff

The Bootstrapper does not run DOCS-VALIDATOR. The Maintainer does, in a separate Claude Code session. The handoff has four moments:

1. **Instruction** — the Bootstrapper explains what to invoke and where (Claude Code session, `@.ai/workflows/DOCS-VALIDATOR.md`)
2. **Wait** — the Maintainer runs the validator outside this chat. Time varies (10-30 minutes).
3. **Return** — the Maintainer pastes the validator's final report back into this chat
4. **Interpret** — the Bootstrapper helps interpret the report: which gaps need addressing now (e.g., a Category 5 unfilled marker that was an oversight), which can be deferred (e.g., a Category 2 orphan that's intentionally not indexed)

---

## 7. Conversation flow

The BS-06 conversation has **five sub-blocks**: opening (7.1), Phase A (7.2), Phase B (7.3), Phase C handoff and return (7.4), bridge to BS-07 (7.5).

### 7.1 — Opening, recap, phase preview (5-10 minutes)

**Purpose:** Re-establish context after BS-05. Recap what BS-01 through BS-05 produced (the docs that now need to be cataloged). Explain the three-phase structure. Set expectations for the DOCS-VALIDATOR handoff.

**Technique:**

1. **Recap BS-05 output briefly.**

   > "BS-05 closed with the five convention docs reviewed and markers filled. Now in BS-06 we look at the documentation map: `docs/INDEX.md`, `.ai/INDEX.md`, and the ROADMAP pre-production checklist. The goal is to make every doc that exists discoverable and every cross-reference accurate."

2. **Recap the documentation that BS-01..BS-05 created.** Use specifics from the session:

   > "Across BS-01..BS-05 we produced:
   >
   > - `docs/PRODUCT.md` (filled progressively)
   > - 7 `docs/product/pd-*.md` files (BS-01)
   > - <N> `docs/product/ft-*.md` files (BS-02) — <list a few>
   > - `docs/engineering/ARCHITECTURE.md`, `STRUCTURE.md` (BS-03)
   > - `docs/engineering/TECH_STACK.md` (BS-04)
   > - 5 convention docs in `docs/engineering/` (BS-05)
   > - <N> ADRs in `docs/engineering/adr/` (BS-03/BS-04/BS-05)
   >
   > Total: <X> docs. BS-06 makes sure `docs/INDEX.md` reflects all of them."

3. **Announce the three-phase structure.**

   > "BS-06 has three phases:
   >
   > - **Phase A** — `docs/INDEX.md`. The substantive work. We fill the markers that BS-01..BS-05 populated, add project-specific entries to 'By task' and 'By topic', and validate the 'Future documents' section against your project's actual intent. 10-20 minutes.
   > - **Phase B** — `.ai/INDEX.md`. Lighter pass. Confirm the workflows list and the step files. 3-5 minutes.
   > - **Phase C** — DOCS-VALIDATOR handoff. You'll switch to a separate Claude Code session and invoke `@.ai/workflows/DOCS-VALIDATOR.md`. It validates every cross-reference automatically, fixes what's mechanically fixable, and surfaces anything that needs your decision. You come back here with the report and we interpret it together.
   >
   > Total Bootstrapper time: 25-45 minutes. Plus 10-30 minutes you spend in the Claude Code session during Phase C.
   >
   > ROADMAP.md is not touched here. Its bootstrap markers (phase overview, future phases, project-specific pre-prod checklist) all belong to BS-07."

4. **Confirm Maintainer is at the keyboard.**

   > "Ready to apply edits as we go? Phases A and B need you at the keyboard. Phase C you'll switch to Claude Code."

**Signal to move on:** Maintainer is oriented, the three-phase structure is understood, ready to start Phase A.

### 7.2 — Phase A: docs/INDEX.md (10-20 minutes)

**Purpose:** Fill the BOOTSTRAP markers in `docs/INDEX.md` and validate the "Future documents" section.

**Technique:**

1. **Present what's there.** Quick summary:

   > "`docs/INDEX.md` is organized in three layers: By area (catalog of every doc), By task (scenarios with docs to read), By topic (lookup table). Plus a 'Future documents' list at the end.
   >
   > Markers to fill, in order:
   >
   > - `PD_DOCS` — the 7 pd-*.md files from BS-01
   > - `FT_DOCS` — the ft-*.md files from BS-02
   > - `ADR_LIST` — ADRs from BS-03/BS-04/BS-05
   > - `PDR_LIST`, `LDR_LIST`, `LEGAL_DOCS` — likely empty at bootstrap, but check
   > - `PHASE_FILES` — stays empty (BS-07 fills)
   > - `TASK_ENTRIES_EXTENSIONS` — project-specific 'By task' entries
   > - `TOPIC_ENTRIES_EXTENSIONS` — project-specific 'By topic' rows
   >
   > Plus the 'Future documents' list to validate against your project's intent."

2. **Walk each marker, capture-aloud as content emerges.**

   For `PD_DOCS`, this is straightforward — the 7 files are known. Confirm one-line descriptions:

   > "PD_DOCS expects 7 entries. The default placeholder has descriptions for each — let me list them and you confirm or adjust:
   >
   > 1. `pd-motivation.md` — why this system, why now, founder-market fit
   > 2. `pd-audience.md` — who needs this, evidence of need, first concrete users
   > 3. `pd-scope.md` — what the system does and what it explicitly doesn't
   > 4. `pd-business-model.md` — how the product makes money and operates in production
   > 5. `pd-risks.md` — weak points, uncertainties, external risks, pre-mortem, success criteria
   > 6. `pd-workflows.md` — high-level narrative of how each actor interacts
   > 7. `pd-mvp.md` — smallest viable version, validation hypotheses, distribution
   >
   > Any adjustments?"

   For `FT_DOCS`, more substantive — the descriptions are project-specific:

   > "FT_DOCS needs one entry per ft-*.md file with a one-line summary specific to your project. Let me draft based on what we captured in BS-02:
   >
   > - `ft-<feature-1>.md` — <one-line from BS-02 Purpose section>
   > - `ft-<feature-2>.md` — <one-line>
   > - <...>
   >
   > Any descriptions to adjust?"

   For `ADR_LIST`, walk the ADRs that were actually created:

   > "ADR_LIST. From BS-03/BS-04/BS-05 we created <N> ADRs:
   >
   > - `0001-<slug>.md` — <one-line summary>
   > - `0002-<slug>.md` — <one-line>
   > - <...>
   >
   > Confirm or adjust descriptions?"

   For `PDR_LIST`, `LDR_LIST`, `LEGAL_DOCS` — almost always empty at bootstrap:

   > "PDR_LIST and LDR_LIST are typically empty at bootstrap — Product and Legal Decision Records get written as the project encounters decisions over time. LEGAL_DOCS is also typically empty (only POLICY_CONSIDERATIONS.md exists, which is listed elsewhere). Confirm all three stay empty?"

   For `PHASE_FILES` — explicit acknowledgment:

   > "PHASE_FILES stays empty in BS-06 — it's filled in BS-07 once the phases beyond 1-2 are planned. Acknowledged?"

   For `TASK_ENTRIES_EXTENSIONS` — substantive, based on BS-05 signals:

   > "TASK_ENTRIES_EXTENSIONS is for project-specific 'By task' entries — recurring scenarios your team will encounter that the universal entries don't cover. From BS-05 signals, candidates that surfaced:
   >
   > - <e.g., 'When handling LGPD data subject requests'>
   > - <e.g., 'When operator curates new catalog items'>
   > - <e.g., 'When designing an LLM-driven workflow'>
   >
   > Any of these apply? Anything else recurring that you'd want in the index?"

   For `TOPIC_ENTRIES_EXTENSIONS`:

   > "TOPIC_ENTRIES_EXTENSIONS is for 'By topic' rows specific to your domain. From BS-02 entities and BS-03 components, candidates:
   >
   > - <e.g., 'Order lifecycle' → `product/ft-orders.md`>
   > - <e.g., 'Operator review workflow' → `product/ft-operator-review.md`>
   > - <e.g., 'Embeddings cache' → `engineering/ARCHITECTURE.md` §6>
   >
   > Add these? Add others?"

3. **Walk the "Future documents" section.**

   > "Now the 'Future documents' list. The default has 7 entries — `DATA_MODEL.md`, `API_CONVENTIONS.md`, `AI_INTEGRATION.md`, `OBSERVABILITY.md`, `DEPLOYMENT.md`, `RELEASE.md`, `runbooks/INCIDENT_RESPONSE.md`. For each, three options:
   >
   > - **Keep** — project will definitely need this before production
   > - **Adjust** — project will need a similar doc, different name or scope
   > - **Remove** — project genuinely won't have this concern
   >
   > Plus: any future doc your project will need that's NOT in this default list? E.g., for an AI-heavy product, `INFERENCE_TUNING.md`; for a multi-locale product, `INTERNATIONALIZATION.md`.
   >
   > Walk through each?"

4. **Compose the final docs/INDEX.md.** Once all markers are agreed:

   > "Here's the consolidated docs/INDEX.md content. The fixed sections (header, By area structure, By task universal entries, By topic universal table) stay unchanged. The markers get replaced inline. Apply by editing docs/INDEX.md in place — I'll list which lines to change.
   >
   > [Present a single fenced markdown block with the full INDEX.md, OR a marker-by-marker replacement guide depending on how many sections changed]"

   The choice (whole-file replacement vs marker-by-marker) follows BS-05's pattern (Section 9.2 of BS-05): whole-file when many markers changed; marker-by-marker when only a few.

5. **Wait for application.** Maintainer applies, confirms, then Phase A is done.

### 7.3 — Phase B: .ai/INDEX.md (3-5 minutes)

**Purpose:** Confirm `.ai/INDEX.md` reflects the actual `.ai/` content.

**Technique:**

1. **Present what's there.**

   > "`.ai/INDEX.md` is mostly static — its structure (workflows/, prompts/, INDEX.md itself) is project-independent. Three things to check:
   >
   > 1. The workflows list matches `.ai/workflows/` content
   > 2. The bootstrap step files (BS-01..BS-07) are listed correctly
   > 3. No project-added workflows that need indexing"

2. **Walk the workflows list.**

   > "Current workflows listed in `.ai/INDEX.md`:
   >
   > - BOOTSTRAPPER.md (with bootstrap/BS-NN..)
   > - TASK-PLANNER.md
   > - ROADMAP-PLANNER.md, ROADMAP-EDITOR.md
   > - ADR-PROPAGATOR.md, PDR-PROPAGATOR.md, LDR-PROPAGATOR.md
   > - DOCS-VALIDATOR.md
   >
   > Any project-added workflows? (Rare at bootstrap; usually no.)"

3. **Confirm bootstrap step files.**

   > "Bootstrap step files in the tree: BS-01..BS-07. All 7 should be listed. Confirm?"

4. **Apply changes if any.** Usually none — Phase B is fast confirmation.

   > "Phase B is clean — no changes needed. Move to Phase C (validator handoff)?"

   If changes are needed (a project-added workflow, or a missing entry), present the targeted edit and apply.

### 7.4 — Phase C: DOCS-VALIDATOR handoff and return (5-30 minutes wall-clock, 5-10 minutes conversation)

**Purpose:** Hand off mechanical validation to a separate Claude Code session. Wait. Interpret the return.

**Technique:**

**Handoff:**

1. **Instruct the switch.**

   > "Now Phase C — the mechanical validation. Open a separate **Claude Code session** in your repo and invoke `@.ai/workflows/DOCS-VALIDATOR.md`. It'll walk every doc under `docs/` and `.ai/`, detect six categories of issues (broken links, orphan files, stale section refs, cross-folder refs, unfilled BOOTSTRAP markers, naming inconsistencies), and fix what's mechanically fixable. Items needing your decision will be surfaced for approval.
   >
   > The validator's own report tells you exactly what to do at each step. When it finishes, copy the final summary back here and we interpret together."

2. **Set expectations.**

   > "The validator typically takes 10-30 minutes depending on repo state. It'll show you per-category diffs and ask for confirmation on each batch. Take your time — it's designed to be slow and careful, not fast and silent.
   >
   > Important: the validator may surface BOOTSTRAP markers in other docs (ARCHITECTURE.md, CODE_STYLE.md, SECURITY.md) that were left empty in earlier BS steps. Those are flagged as 'Category 5 — unfilled markers' in its report. We'll decide here what to do with each one when you come back. Note: ROADMAP.md's three bootstrap markers (phase overview, future phases, pre-prod checklist) will also appear in Category 5 — those are expected; they belong to BS-07. Just confirm they're acknowledged and we move on."

3. **Wait.** No active conversation during Phase C's wall-clock duration. The Bootstrapper does NOT do anything during this wait — no preemptive interpretation, no checking the docs again.

**Return:**

4. **Receive the report.** When the Maintainer returns with the validator's summary, parse it:

   > "Got the report. Let me walk it with you:
   >
   > - **Auto-fixes applied:** <list categories, e.g., '12 renames in Category 1, 3 stale section refs in Category 3, 1 naming inconsistency in Category 6'>. These are done.
   > - **Approval-required, resolved during validator run:** <list, e.g., '2 orphan files — both added to docs/INDEX.md'>. These are done.
   > - **Category 5 unfilled markers** (report only): <list>. Each one is a content gap — let's decide which to address now and which to defer.
   > - **Skipped or deferred** (validator skipped or you chose 'skip'): <list>. Need to decide what to do with each.
   >
   > Walk each category?"

5. **Interpret Category 5 (unfilled markers) explicitly.**

   This is the most consequential category for BS-06's exit. Each unfilled marker is a content gap left by an earlier BS step **OR** a marker that legitimately belongs to a later BS step.

   **First, separate ROADMAP.md markers from the rest.** The three ROADMAP.md markers (`PHASE_OVERVIEW_PROJECT_PHASES`, `FUTURE_PHASES`, `PROJECT_SPECIFIC_PREPROD_CHECKLIST`) appear in Category 5 but they are **not** BS-06 gaps — they belong to BS-07. Acknowledge them in chat and skip:

   > "Category 5 includes three ROADMAP.md markers — those belong to BS-07 (Roadmap planning), not BS-06. Skipping them as expected-empty. They'll be filled in the next step."

   **Then walk the actually-relevant unfilled markers**, applying the convention: **Bootstrapper informs the Maintainer what's missing, what the consequences are, Maintainer decides whether to address now or defer**.

   > "Category 5 unfilled markers that need a decision (excluding the ROADMAP.md ones):
   >
   > 1. **`ARCHITECTURE.md` §<N>, marker `<NAME>`** — placeholder is `<paraphrase of placeholder>`. Consequence of leaving empty: <what breaks downstream, e.g., 'a Phase 1 task that builds component X will need this content; the executor would have to ask'>. Address now (5-10 min) or defer?
   >
   > 2. **`SECURITY.md` §<N>, marker `<NAME>`** — placeholder is `<paraphrase>`. Consequence: <e.g., 'no project-specific data tier examples; classification decisions will default to abstract guidance'>. Address now or defer?
   >
   > 3. <...>
   >
   > For each, your call. Defer is acceptable — but I want you to make the call consciously per gap."

   Each Maintainer decision is recorded:
   - **Address now**: re-enter the relevant BS step briefly, fill the marker, apply, return
   - **Defer**: note in INDEX.md or in the doc itself as an open question; consciously leave the marker placeholder

6. **Interpret skipped/deferred items.** Same pattern as Category 5 — present, explain consequence, let Maintainer decide.

7. **Move to bridge once all items are decided.**

   > "All validator items handled. <N> markers filled now, <M> markers deferred with notes. <K> ROADMAP.md markers acknowledged as BS-07's scope. Phase C done. Move to the bridge to BS-07?"

### 7.5 — Verify outcomes, capture Decisions Captured for BS-07, bridge to BS-07 (5-10 minutes)

**Purpose:** Close BS-06 cleanly. Walk the exit criteria checklist. Recap what's been captured for BS-07. Set the Maintainer up to start BS-07 or pause.

**Technique:**

1. **Walk the Exit criteria checklist (Section 3).** For each:
   - docs/INDEX.md markers filled?
   - .ai/INDEX.md confirmed?
   - DOCS-VALIDATOR ran, report interpreted, gaps resolved or deferred (excluding the ROADMAP.md markers that belong to BS-07)?

   Surface any unmet outcome. Per Section 10.2 below, gaps go to Option A (cover now) or Option B (defer with structure).

2. **Recap Decisions Captured for BS-07.** BS-06 surfaces few BS-07-specific signals (BS-07 is roadmap planning, mostly independent of docs map). The main signals:

   > "Signals for BS-07 (Roadmap):
   >
   > - **Three ROADMAP.md markers are unfilled and waiting for BS-07**: `PHASE_OVERVIEW_PROJECT_PHASES` (Phase 3+ rows), `FUTURE_PHASES` (direction-setters), `PROJECT_SPECIFIC_PREPROD_CHECKLIST` (project-specific pre-prod items). BS-07 owns all three.
   > - **Phase-relevant feature priorities** that may have surfaced when discussing the 'By task' entries — features users will need first inform phase ordering.
   > - **Future engineering docs** identified in Phase A (`docs/INDEX.md` "Future documents" walk) — each is a candidate for a task in a relevant phase (e.g., "Write DEPLOYMENT.md" near deploy phase).
   >
   > Carry these into BS-07?"

3. **Preview BS-07.**

   > "BS-07 is the last bootstrap step. We plan the phases beyond 1-2 (which the template pre-fills as universal), define the per-phase task files in `docs/roadmap/`, and prepare TSK-001 to be picked up by the Planner. Typically the densest BS step — 45-90 minutes."

4. **Commit BS-06 before continuing or pausing.** See sub-block 11.4 for the explicit commit guidance.

5. **Ask for readiness.**

   > "BS-07 typically takes 45-90 minutes. We continue in this same chat. Ready, or want a break?"

6. **If the Maintainer pauses:** docs/INDEX.md, .ai/INDEX.md, ROADMAP.md, and any validator fixes are already in the repo. The next session resumes by re-reading docs/INDEX.md to refresh state.

**Signal that BS-06 is done:** the recap is agreed, Decisions Captured for BS-07 are confirmed, the commit is in place, the Maintainer either kicks off BS-07 or explicitly pauses.

---

## 8. Forcing functions specific to BS-06

The forcing functions from BOOTSTRAPPER section 10 and earlier BS step files apply across all BS steps. The following are calibrated for docs map validation.

### 8.1 — When the Maintainer wants to skip Phase A

**Symptom:** After hearing "BS-06 is the lightest step", the Maintainer says "let's just run DOCS-VALIDATOR and skip the markers, the validator catches everything".

**Why it's a problem:** the validator does NOT fill BOOTSTRAP markers (Category 5 is report-only). Skipping Phase A means the markers stay placeholders, which the validator will then report as Category 5 issues — looping back to manual fill anyway, but at the wrong moment (after the validator ran, when the report is full of "Category 5: 12 unfilled markers" noise).

**Response pattern:**

> "The validator surfaces unfilled markers but doesn't fill them — only you know what to put there. Phase A fills them at the right moment so the validator's report focuses on mechanical issues. If we skip Phase A, the validator's Category 5 report becomes a 'do Phase A now, in a different mode' moment.
>
> Phase A is 10-20 minutes. Phase D will be 5-10 minutes shorter as a result. Net cost is roughly the same; the order is what makes the validator's report cleaner. Run Phase A first?"

Default to running Phase A first.

### 8.2 — When the FT_DOCS descriptions are too generic

**Symptom:** The Maintainer accepts generic descriptions for ft-*.md files ("ft-orders.md — order management").

**Why it's a problem:** generic descriptions in INDEX.md are useless. A new contributor scanning the index gets no signal about what's in each file.

**Response pattern:**

> "'Order management' is too generic. Let me probe: in your project, what does `ft-orders.md` cover specifically? Pull from the Purpose section of the file:
>
> - Lifecycle? (e.g., 'order lifecycle from creation through fulfillment')
> - Multi-actor? (e.g., 'buyer, supplier, operator interactions on orders')
> - Domain-specific? (e.g., 'order with multi-supplier quotation aggregation')
>
> A useful one-liner reflects what makes this feature distinctive in your project, not just the noun."

Push for specificity. One sentence is enough — but it should be a sentence that orients.

### 8.3 — When the Maintainer wants to add 10+ "By task" entries

**Symptom:** During TASK_ENTRIES_EXTENSIONS discussion, the Maintainer wants to add many specific scenarios.

**Why it's a problem:** "By task" is meant for recurring scenarios. Adding one-off or rare scenarios bloats the section, making the truly common scenarios harder to find. The universal entries already cover most common ground.

**Response pattern:**

> "10+ extensions is heavy. A useful 'By task' section has 3-7 project-specific entries beyond the universal ones — recurring scenarios the team will hit. Let me filter:
>
> - Which of these scenarios will the team hit at least once a week?
> - Which are one-off (will hit once or twice over the project's life)?
>
> Keep the weekly ones, defer the one-offs to ad-hoc consultation."

Cap around 5-7 project-specific extensions. Push back on more.

### 8.4 — When the Maintainer wants to remove "Future documents" entries that will likely be needed

**Symptom:** During "Future documents" walk, the Maintainer says "remove DATA_MODEL.md, we'll wing it" or "remove DEPLOYMENT.md, deployment is just docker-compose".

**Why it's a problem:** "Future documents" represents deferred commitments. Removing items that will likely be needed means the team won't have the placeholder reminder when the need surfaces.

**Response pattern:**

> "Removing <X> means when you actually need it (likely month <N>), there's no placeholder to remind you to write it. Two options:
>
> **A)** Keep with a more accurate note. E.g., 'DATA_MODEL.md — entities, relationships, aggregates. Write when you have 3+ entities with non-trivial relationships.'
> **B)** Remove if you're confident the project genuinely won't need it. E.g., a CLI tool may not need API_CONVENTIONS.md.
>
> For <X> specifically, which feels right? My read of your project is <A or B with reasoning>."

Recommend keeping with a more accurate note unless the item is truly N/A.

### 8.5 — When the validator surfaces a Category 5 marker the Maintainer didn't know about

**Symptom:** During Phase D return, the Maintainer is surprised that a marker in (say) SECURITY.md is unfilled, because they thought BS-05 filled all SECURITY.md markers.

**Why it's a problem:** the surprise suggests BS-05 had a gap that wasn't surfaced at the time. The Maintainer may feel BS-05 wasn't done properly.

**Response pattern:**

> "This marker was added to SECURITY.md when we restored §9-§12 — that was a separate restoration that happened mid-BS-05. The marker was added but I may have moved on without surfacing that it was a new fill responsibility. Apologies for the gap.
>
> Two options:
>
> **A)** Fill it now (5-10 minutes, brief return to BS-05 mode for this one marker)
> **B)** Defer with explicit note in SECURITY.md
>
> Recommendation: **A** if the content is fresh in your mind, **B** if you'd need to re-engage with the topic. Which?"

Take responsibility for the gap honestly, give the Maintainer the choice.

### 8.6 — When DOCS-VALIDATOR finds an unexpectedly large number of issues

**Symptom:** Phase D return shows the validator caught 30+ issues across categories — way more than expected for a fresh bootstrap.

**Why it's a problem:** either the bootstrap had systemic drift (many docs reference outdated paths) or the validator detected something nominal (like the BS-01..BS-07 self-references that exist in many places). The Maintainer may panic.

**Response pattern:**

> "30+ issues sounds a lot but most likely categorizes:
>
> - **Category 6 naming inconsistencies** — if any docs still reference old step file names. These are mechanical and auto-fixed in batches.
> - **Category 4 cross-folder references** — `docs/` to `.ai/` paths that may have shifted. Also mechanical.
> - **Category 1 broken links** — usually a smaller count, often related to renames.
>
> Let me walk the breakdown by category. The total count matters less than the per-category mix. If most are auto-fixable, the actual work is small."

Calm framing + walk per-category mix. Most large counts are concentrated in 1-2 categories that are mechanical.

### 8.7 — When the Maintainer's BS-06 takes much longer than 30-50 minutes

**Symptom:** Phases A-C and conversation portion of D have exceeded 60 minutes and Phase D hasn't even completed yet.

**Why it's a problem:** BS-06 going long usually means the Maintainer is doing work that belongs in BS-05 (re-engaging with convention content). That work is legitimate, but BS-06 isn't the place for it.

**Response pattern:**

> "BS-06 is running long. Common cause: we're hitting markers that belong to earlier BS steps and treating them as new work. Two options:
>
> **A)** Push through with light touch — for any earlier-step marker we hit, defer with a note in the doc and move on. Get BS-06 done.
> **B)** Pause BS-06, re-engage with the earlier step (e.g., 'BS-05 mode' for a specific convention marker), then resume BS-06.
>
> Recommendation: **A**. BS-06's purpose is the docs map, not re-doing earlier steps. Deferred markers are documented gaps — they can be filled via a follow-up PR after the bootstrap. Want to push through?"

Default to A. Maintainers who over-engage with deferred markers will burn out before BS-07.

### 8.8 — When the project genuinely has no project-specific "By topic" rows

**Symptom:** During TOPIC_ENTRIES_EXTENSIONS discussion, the Maintainer can't think of any project-specific topics. The universal table covers everything.

**Why it's a problem:** this is acceptable for some products (very generic backend, minimal domain complexity) but unusual for AI-first projects that typically have domain entities, feature names, regulated topics worth indexing.

**Response pattern:**

> "Zero project-specific topics is possible but unusual. Quick probe:
>
> - **Domain entities** — your `ft-*.md` files introduced entities (e.g., <list 2-3 from BS-02>). Are any of those worth a 'By topic' row?
> - **Feature concepts** — your features have concepts (e.g., <list 2-3 from BS-02 vocabulary>). Worth indexing?
> - **Regulated areas** — if your APPLICABLE_REGULATIONS in SECURITY.md identified specifics, those are worth indexing (e.g., 'PII data handling — `engineering/SECURITY.md` §10').
>
> Anything surface?"

Often 2-3 rows surface after probing. If after probing still nothing, accept and move on.

### 8.9 — When the Maintainer wants to commit between phases

**Symptom:** After Phase A, the Maintainer says "let me commit docs/INDEX.md before moving on".

**Why it's a problem:** Phases A, B, C, and the post-D fixes are all part of BS-06's work. Committing between phases creates 4 commits where 1 commit belongs. The bootstrap convention is **one commit per BS step**.

**Response pattern:**

> "BS-06 is one commit at the end (per the bootstrap commit convention in `@docs/engineering/GIT.md`). Holding the work uncommitted across phases is intentional — the validator in Phase D may surface fixes that change docs/INDEX.md again, and we want them in the same commit.
>
> Commit after Phase D's validator return is interpreted and any deferred-vs-fix decisions are made. Sound right?"

Hold the commit until BS-06's end.

### 8.10 — When the Maintainer skips Phase D ("validator can wait")

**Symptom:** After Phases A-C, the Maintainer wants to declare BS-06 complete and run the validator later.

**Why it's a problem:** the validator is a hard requirement of BS-06 (Exit criteria Section 3). Without running it, broken cross-references introduced by BS-01..BS-05 may still be in the repo, and BS-07 builds on a docs state that the validator hasn't certified.

**Response pattern:**

> "Phase D is in BS-06's hard requirements. The validator catches mechanical drift from BS-01..BS-05 — renames, stale section refs, naming inconsistencies — that we can't catch manually. Without it, BS-07 builds on a state that hasn't been certified.
>
> Two options:
>
> **A)** Run it now, 10-30 minutes in Claude Code. We resume here when you have the report.
> **B)** Pause BS-06 (don't declare it complete), commit Phases A-C work as 'BS-06 partial', resume later with Phase D.
>
> Recommendation: **A**. Phase D is short and the inertia of doing it now is low. Pausing means re-engaging with BS-06 context later. Run it now?"

Default to A. If the Maintainer insists on B, accept but note BS-06 is not complete and BS-07 should not start until Phase D resumes.

---

## 9. Pre-fill rules

The Bootstrapper does **not** consolidate at the very end of BS-06. Instead, content is composed and applied **per phase**, with the Maintainer applying each phase's content before the next phase begins.

This section defines the phase-level pattern specifics. The fundamentals (capture-aloud format, `~~~markdown` fence, BOOTSTRAP marker replacement) carry over from earlier BS steps.

### 9.1 — Capture-aloud during phase discussion

Same fundamental pattern. The destination is "the file in the current phase":

**Format:**

> "Capturing for `<file>` §<section or marker>: <content + structured anchor>. This goes into the consolidated block for this phase."

**Examples:**

- "Capturing for `docs/INDEX.md` `PD_DOCS`: 7 entries — pd-motivation.md, pd-audience.md, pd-scope.md, pd-business-model.md, pd-risks.md, pd-workflows.md, pd-mvp.md, each with the descriptions confirmed above. Goes into Phase A consolidation."

- "Capturing for `docs/INDEX.md` `TASK_ENTRIES_EXTENSIONS`: 3 entries — 'When handling LGPD data subject requests', 'When operator curates new catalog items', 'When designing an LLM-driven user-facing interaction'. Goes into Phase A consolidation."

- "Capturing for `docs/ROADMAP.md` `PROJECT_SPECIFIC_PREPROD_CHECKLIST`: 4 items — initial catalog seed, operator training docs, provider X production credentials, privacy policy URL live. Goes into Phase C consolidation."

**Frequency:** 3-8 captures across BS-06's phases, mostly concentrated in Phase A.

### 9.2 — The phase consolidation block

When the discussion for a phase is complete, the Bootstrapper composes the final content as a fenced markdown block. Same convention as earlier BS steps:

**Option A — Marker-only block** (most common for Phases A and C):

Only the affected markers + their replacement content are in the block. Fixed sections of the target file stay unchanged.

**Option B — Whole-file block** (rare in BS-06 unless many markers changed):

Used if Phase A required substantial adjustments across multiple sections of docs/INDEX.md (e.g., "Future documents" majorly revised + multiple markers + a By task restructure).

**Option C — No changes** (most common for Phase B):

If Phase B confirmation shows nothing needs updating, no consolidation block. The Bootstrapper says "`.ai/INDEX.md` is clean. Move to Phase C."

### 9.3 — BOOTSTRAP marker handling in INDEX files

Same rule as earlier BS steps: when a marker is filled, the entire marker block (START/END comments + placeholder description) is **replaced** by the actual content. Marker comments do not remain.

**Special case for INDEX markers that are "intentionally empty for now":**

Some markers in `docs/INDEX.md` (PHASE_FILES at BS-06, PDR_LIST, LDR_LIST, LEGAL_DOCS) are intentionally empty at bootstrap time — they get filled later as the project evolves. The Bootstrapper does NOT remove the marker block for these. The marker stays in place with the placeholder description intact, so future contributors know where to add entries.

> "PHASE_FILES marker stays in place — BS-07 will fill it. Don't remove the marker block; just acknowledge in chat that BS-07 owns this."

### 9.4 — Validator-staged changes vs Bootstrapper-staged changes

After Phase D, the docs state combines:

- Changes the Maintainer applied during Phases A-C (Bootstrapper-staged, in `git status` from the Maintainer's manual application)
- Changes the validator applied during Phase D (validator-staged, in `git status` from the Claude Code session)
- Possibly additional changes from the Maintainer if Phase D return led to filling deferred markers

All of these go in one BS-06 commit at the end. The Bootstrapper does NOT separate them into multiple commits — the bootstrap convention is one commit per BS step.

### 9.5 — Decisions Captured for BS-07

BS-06 surfaces few BS-07-specific signals. The two that matter:

- **Pre-production checklist items** from Phase C — these become candidate tasks in the latest pre-production phase
- **Feature priority signals** that may have surfaced during TASK_ENTRIES_EXTENSIONS discussion (which scenarios will the team hit weekly informs which features ship first)

The Bootstrapper holds these mentally for the bridge to BS-07.

---

## 10. Required outcomes (checklist for closing BS-06)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step. The checklist mirrors Section 3 (Exit criteria) but reformatted for the closing conversation.

### 10.1 — Outcomes that must be true to close BS-06

#### docs/INDEX.md (Phase A)

- [ ] `PD_DOCS` filled with the 7 pd-*.md files
- [ ] `FT_DOCS` filled with the actual ft-*.md files and project-specific descriptions
- [ ] `ADR_LIST` filled with the ADRs from BS-03/BS-04/BS-05 (or explicitly empty)
- [ ] `PDR_LIST`, `LDR_LIST`, `LEGAL_DOCS` reviewed (typically empty)
- [ ] `PHASE_FILES` left empty (BS-07 owns)
- [ ] `TASK_ENTRIES_EXTENSIONS` filled or explicitly empty
- [ ] `TOPIC_ENTRIES_EXTENSIONS` filled or explicitly empty
- [ ] "Future documents" reviewed against project intent

#### .ai/INDEX.md (Phase B)

- [ ] Workflows list matches actual `.ai/workflows/` content
- [ ] Bootstrap step files BS-01..BS-07 listed correctly
- [ ] Cross-references accurate

#### ROADMAP.md (Phase C)

- [ ] `PROJECT_SPECIFIC_PREPROD_CHECKLIST` filled or explicitly empty with acknowledgment

#### DOCS-VALIDATOR (Phase D)

- [ ] Validator was invoked in a separate Claude Code session
- [ ] Validator's report was returned to the Bootstrapper conversation
- [ ] Auto-fixes applied
- [ ] Approval-required items resolved (orphans, true orphan links)
- [ ] Category 5 unfilled markers consciously addressed (filled now or deferred with note)

#### Anchor questions answered

- [ ] **Anchor 1 (Does INDEX.md list every doc?)** — confirmed by Phase A completion + validator pass
- [ ] **Anchor 2 (Does "By task" reflect team scenarios?)** — confirmed in Phase A TASK_ENTRIES_EXTENSIONS discussion
- [ ] **Anchor 3 (Does "Future documents" reflect actual intent?)** — confirmed in Phase A "Future documents" walk

#### Decisions Captured for BS-07

- [ ] Pre-production checklist items captured for BS-07 reference
- [ ] Any feature priority signals from TASK_ENTRIES discussion noted for BS-07

### 10.2 — When a required outcome is unmet

Same options as earlier BS steps:

**Option A — Cover it now.** Pick up the conversation thread on the gap.

**Option B — Defer it with structure.** Document the gap explicitly:
- Unfilled marker in docs/INDEX.md → keep marker block in place with a note explaining the deferral
- Validator-surfaced gap in another doc → note in that doc's Open questions section
- Pre-production checklist gap → note in ROADMAP.md Open cross-doc questions

The Bootstrapper recommends Option A when the gap blocks BS-07 (e.g., FT_DOCS empty makes "When adding a feature" task confusing). Option B when the gap is genuinely premature (e.g., a future doc that won't be needed for months).

### 10.3 — What's explicitly NOT required to close BS-06

To prevent perfectionism:

- BOOTSTRAP markers in other docs (ARCHITECTURE.md, CODE_STYLE.md, SECURITY.md) that earlier BS steps left empty — those gaps are owned by earlier steps; BS-06 only flags them via the validator report. Filling them is optional in BS-06 (Option A from 10.2) and can be deferred (Option B).
- Future docs creation — they stay as pointers in INDEX.md
- Phase 3+ task files — BS-07's scope
- New ADRs, PDRs, LDRs — added when decisions are made, not in a docs map step
- Detailed "By topic" rows for every possible topic — only the recurring/important ones

The Bootstrapper resists pulling BS-07+ content into BS-06.

---

## 11. Bridge to BS-07

When the Required outcomes checklist is complete (or consciously deferred with notes), the Bootstrapper transitions to BS-07 with the following sequence.

### 11.1 — Recap

Two or three sentences:

> "BS-06 closed the documentation map. `docs/INDEX.md` reflects every doc that exists: <N> pd-*.md, <M> ft-*.md, <K> ADRs, project-specific 'By task' and 'By topic' extensions. `.ai/INDEX.md` confirmed. `ROADMAP.md` pre-production checklist extended with <count> project-specific items. DOCS-VALIDATOR ran: <N> auto-fixes applied, <M> approval items resolved, <K> deferred markers acknowledged."

### 11.2 — Confirm Decisions Captured for BS-07

Recap BS-07-relevant signals:

> "Signals for BS-07 (Roadmap):
>
> - **Pre-production checklist items** that became tasks (Phase C output) — these are candidate tasks in the latest pre-production phase
> - **Feature priority signals** from 'By task' discussion — features that will be hit weekly inform phase ordering
> - **Deferred markers** in other docs (if any) — these may need to become explicit tasks in later phases ('Fill SECURITY.md DATA_MAP after legal review')
>
> Carry these into BS-07?"

### 11.3 — Preview BS-07

Two sentences:

> "BS-07 plans the phases beyond 1-2 (which the template pre-fills as universal). We define phase boundaries based on your MVP scope (from `pd-mvp.md`), feature priorities (from BS-02 inventory), and the strategic asset (from `pd-motivation.md`). Phase files in `docs/roadmap/` get populated with task lists. By the end, TSK-001 is ready to be picked up by `@.ai/workflows/TASK-PLANNER.md`."

### 11.4 — Commit BS-06 before continuing or pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

Before moving to BS-07 (or pausing), the Maintainer commits the work BS-06 produced:

```
git add docs/INDEX.md \
        .ai/INDEX.md \
        # ... plus any files the validator modified during Phase C
        # ... plus any deferred-marker docs the Maintainer chose to fill in Phase C return

git commit -S -m "docs(bootstrap): complete BS-06 — docs map"
git push origin main
```

The exact file list depends on what the validator touched and what deferred markers (if any) the Maintainer chose to fill. ROADMAP.md is **not** included — it belongs to BS-07. The Bootstrapper presents the precise list based on the session's actual changes.

After the commit lands, proceed to 11.5 (continue) or 11.6 (pause).

### 11.5 — Continue in the same chat

> "BS-07 typically takes 45-90 minutes. We continue in this same chat — the bootstrap context, especially the BS-02 feature inventory and the BS-05 convention commitments, needs to carry through. Ready, or want a break?"

If the Maintainer pauses, the same recovery pattern applies — all docs are in the repo, resumption re-reads `docs/INDEX.md` and `docs/ROADMAP.md` to refresh state.

### 11.6 — If the Maintainer wants to stop entirely

Same handling as earlier BS steps:

> "Understood. A few things before we pause:
>
> 1. Everything is saved in the docs — `docs/INDEX.md`, `.ai/INDEX.md`, plus everything from BS-01..BS-05. The validator's changes are committed (per Phase C and 11.4).
> 2. When you come back, stay in this same chat session. Starting fresh means rebuilding context, losing any Decisions Captured I'm holding for BS-07.
> 3. When you resume, say 'resuming bootstrap' and I'll re-orient.
>
> Take the break. The work is preserved."

---

## 12. Notes for the Bootstrapper LLM running BS-06

These are operational reminders specific to BS-06, on top of universal BOOTSTRAPPER guidance and earlier BS step files' Section 12 notes.

### 12.1 — BS-06 is mostly catalog, not creation

The temptation in BS-06 is to "improve" the docs that BS-01..BS-05 produced — fix wording, restructure sections, deepen content. Resist.

BS-06's job is the catalog and the map. The docs themselves were finalized in their owning BS steps. If a doc needs substantive revision, that's a PDR/ADR/LDR matter (post-bootstrap) or a return to the earlier BS step (mid-bootstrap exception), not BS-06 work.

When the Maintainer suggests substantive doc revision during BS-06, push back:

> "That's a substantive change to <doc>. BS-06 is the docs map step — not the right place for it. Two options:
>
> **A)** Capture as Open question in <doc>, address post-bootstrap via PDR/ADR/LDR
> **B)** Briefly return to <BS-NN> mode for this one change, then resume BS-06
>
> Recommendation: **A** unless the change is small (5 minutes). BS-06 should stay focused."

### 12.2 — Phase C is async; the Bootstrapper does NOT preempt the validator

When the Maintainer switches to Claude Code for Phase C, the Bootstrapper waits. Do NOT:

- Pre-emptively run validation checks in the Bootstrapper conversation
- Read all the docs to "predict" what the validator will find
- Offer interpretation of issues the validator hasn't yet reported

The validator's report is the source of truth for Phase C. The Bootstrapper interprets after the report arrives, not before.

### 12.3 — Validator categories map to specific BS-06 obligations

When the Maintainer returns with the validator report, the Bootstrapper maps each category to a specific obligation:

| Validator category | BS-06 obligation |
|---|---|
| Category 1 (broken links) | Auto-fixes applied. No BS-06 action needed. |
| Category 2 (orphan files) | Maintainer resolved during validator run (approval-required). No BS-06 action. |
| Category 3 (stale section refs) | Auto-fixes applied. No BS-06 action. |
| Category 4 (cross-folder refs) | Auto-fixes applied. No BS-06 action. |
| Category 5 (unfilled BOOTSTRAP markers) | **BS-06 obligation**: walk each one with Maintainer per forcing function 8.5 + 7.4 step 5. Decide: fill now or defer with note. The three ROADMAP.md markers (`PHASE_OVERVIEW_PROJECT_PHASES`, `FUTURE_PHASES`, `PROJECT_SPECIFIC_PREPROD_CHECKLIST`) are explicitly skipped — they belong to BS-07. |
| Category 6 (naming inconsistencies) | Auto-fixes applied. No BS-06 action. |

Category 5 is the only one that requires BS-06 conversation work. The others were already handled in the validator session.

### 12.4 — The "Future documents" walk is judgment-heavy

Section 7.2 step 3 (walking "Future documents") is the most judgment-heavy part of Phase A. The Bootstrapper:

- Knows the universal default list (7 items)
- Knows the project's specific context (from BS-01..BS-05)
- Has to recommend per item: keep / adjust / remove / add new

The recommendation is grounded in the project. For example:

- "Your project has heavy LLM integration (BS-04 pinned Anthropic, BS-03 §7 has LLM provider as integration). `AI_INTEGRATION.md` stays — you'll need it on first prompt design."
- "Your project is API-first (multiple endpoints surfaced in BS-02). `API_CONVENTIONS.md` stays — you'll need it on TSK-013 (first endpoint)."
- "Your project doesn't expose a REST API (CLI-only). `API_CONVENTIONS.md` removed."

Make the recommendation, let the Maintainer decide.

### 12.5 — Pacing

BS-06 typically takes 30-50 minutes Bootstrapper-time + 10-30 minutes async during Phase D:

- **First 5-10 minutes**: opening, recap, phase preview (7.1)
- **Middle 15-30 minutes**: Phases A + B + C (7.2 + 7.3 + 7.4)
- **Async (10-30 minutes wall-clock)**: Phase D validator run (Maintainer's Claude Code session)
- **Phase D return 5-10 minutes**: interpretation of validator report (7.5)
- **Final 5-10 minutes**: outcomes verification, commit, bridge (7.6 + 11.4)

If Bootstrapper time exceeds 50 minutes (excluding Phase D wall-clock), apply forcing function 8.7 — likely doing earlier-step work that should be deferred.

### 12.6 — Resist completionism on "By topic" extensions

The TOPIC_ENTRIES_EXTENSIONS marker is a temptation to over-index — adding 20+ rows for every conceivable topic. Resist.

Useful "By topic" rows reflect topics that:
- Will be referenced by multiple contributors
- Have a non-obvious primary doc location
- Are domain-specific enough that the universal table doesn't cover them

3-7 project-specific rows is the typical sweet spot. More than that adds noise.

### 12.7 — The validator self-indexing in .ai/INDEX.md

In a fresh project starting from this template, `.ai/INDEX.md` already has DOCS-VALIDATOR self-indexed in three places (Quick orientation tree, Workflows in detail, Common scenarios table). This was done during template development.

The Bootstrapper does NOT need to re-do this self-indexing in BS-06. Phase B is just a confirmation that the self-indexing is still intact and matches the actual `.ai/workflows/` content.

If for some reason the self-indexing is missing (e.g., the project's `.ai/INDEX.md` was customized), Phase B catches it and the Bootstrapper proposes restoring the entries.

### 12.8 — The PYTHON_REFERENCE_EXAMPLE removal question

By BS-06, the PYTHON_REFERENCE_EXAMPLE comment blocks in CODE_STYLE.md, TESTING.md, SECURITY.md, TECH_STACK.md may still be in place (BS-05 §10.1 left the decision to the Maintainer with default = remove).

If they're still in place at BS-06, the Bootstrapper does NOT make this a BS-06 obligation. It's a BS-05 leftover. The validator may surface them as a quality concern (it doesn't, actually — the comment blocks are inside HTML comments and don't break anything), but BS-06 doesn't force their removal.

If the Maintainer raises the question during BS-06 ("should I remove the reference examples now?"), the Bootstrapper accepts:

> "Sure, two options:
>
> **A)** Remove now, one targeted edit per file. 5 minutes total across the 4 docs.
> **B)** Defer to a post-bootstrap cleanup PR.
>
> Recommendation: **A** if you want clean docs from day one, **B** if you prefer to ship the bootstrap commit and clean up later. Either works."

Don't force the decision either way.

### 12.9 — When INDEX.md grows large

The default `docs/INDEX.md` is around 12KB. After BS-06 fills the markers, it may grow to 15-20KB. That's acceptable — INDEX.md is the navigation layer and density is a feature, not a bug.

If INDEX.md exceeds ~30KB (unusual), surface as a concern:

> "INDEX.md is now <X>KB — larger than typical. Possible signs we over-extended `By task` or `By topic`. Want to do a quick prune pass?"

Most projects don't hit this; just be aware.

### 12.10 — BOOTSTRAP markers in INDEX.md that reference old step names

Same pattern as earlier BS step files' §12.10: some BOOTSTRAP marker descriptions in INDEX.md may reference the old step file naming (`01-domain.md`, `02-product.md`, etc.) if they were drafted before the BS-NN rename.

The Bootstrapper:

- Ignores stale step name references when interpreting marker intent
- Replaces marker block entirely during application (new content replaces both marker comments and any stale text inside)
- Does not "fix" old references as a side mission
- Flags as known issue if the Maintainer notices

The DOCS-VALIDATOR (Phase D) catches these automatically in Category 6.

# Roadmap

Build plan for the project. Small, sequential tasks designed for progressive learning and trunk-based development.

This is a **prospective plan** — written before execution. Each entry's "Learnings" subsection is filled in **after** the corresponding task merges, turning the roadmap into a technical diary of the project.

`docs/ROADMAP.md` itself is an **index**: it lists the phases and links to per-phase files under `docs/roadmap/`. The bulk content (entries, retrospectives) lives in those files. This file holds project-wide principles, conventions, and pre-production checklists.

---

## Principles

- **Trunk-based development.** Short branches, squash merge, no long-lived feature branches. See `@docs/engineering/GIT.md`.
- **Small tasks.** Target: single focus, reversible, reviewable in 15 minutes, explainable in 2-3 sentences. Typical size under 400 lines of diff.
- **Per-PR workflow.** Each task follows the per-PR workflow in `@docs/engineering/GIT.md` "Per-PR workflow".
- **Sequential order matters.** Later tasks depend on earlier ones. Skip at your own risk.
- **Active learning.** Before generating each task, discuss with the Planner: what, why, what decisions are in play, what to learn from it.
- **Documentation stays elsewhere.** This roadmap tracks order and rationale. Technical rules live in the policy docs (`@docs/engineering/CODE_STYLE.md`, `@docs/engineering/ARCHITECTURE.md`, etc.).

---

## Phase overview

| Phase | Goal | Tasks | Status | File |
|---|---|---|---|---|
| **Phase 1** | Repository foundation: tooling, CI, quality gates | TSK-001..TSK-012 | ⬜ Not started | [phase-1-foundation.md](roadmap/phase-1-foundation.md) |
| **Phase 2** | Application infrastructure: framework, DB, migrations, observability | TSK-013..TSK-022 | ⬜ Not started | [phase-2-infrastructure.md](roadmap/phase-2-infrastructure.md) |

<!-- BOOTSTRAP:PHASE_OVERVIEW_PROJECT_PHASES:START -->
> 📝 Filled by bootstrap prompt `BS-06-docs-map.md` after the project-specific phase breakdown is decided.
>
> Add the project's domain-specific phases below as new rows. Format:
>
> | **Phase N** | <one-line goal> | TSK-NNN..TSK-MMM (count) | ⬜ Not started | [phase-N-name.md](roadmap/phase-N-name.md) |
>
> Most projects continue with Phase 3+ covering domain features. Status symbols: ⬜ Not started · 🟡 In progress · ✅ Complete · 🟥 Blocked.
<!-- BOOTSTRAP:PHASE_OVERVIEW_PROJECT_PHASES:END -->

---

## Notes on using this roadmap

**Before each task:**
1. Re-read the entry for that task in its phase file.
2. Discuss with the Planner: what, why, decisions in play, what to learn.
3. Generate the execution prompt.
4. Review carefully — understand every change the Executor produces.
5. Merge.
6. **Fill in the "Learnings" section** with what was done, what surprised you, what you learned.

**After each phase:**
- Fill the retrospective section in the corresponding phase file.
- Adjust later phases if needed based on what was learned.

**Prompt files live alongside the phase they belong to.** Each phase's execution prompts go in `.ai/prompts/execution/<phase-folder>/`, where the folder name mirrors the phase's roadmap file under `docs/roadmap/`. Subfolders are created lazily — the first prompt of a new phase creates the folder.

---

## Bugfix, adjustment, and replanning conventions

The roadmap uses four distinct conventions for cases where work on a task extends or revises the original plan. Each convention has a specific notation, scope, and place in the roadmap.

### 1. Bugfix sub-task — `TSK-NNN.BF-MM`

A bugfix sub-task addresses an issue discovered **after** the parent task is merged (during manual testing, review of a later PR, or production observation). It is recorded as a sub-entry under the parent task in the same phase file. `MM` is two-digit zero-padded and increments per parent (`BF-01`, `BF-02`, …).

Each bugfix sub-task entry includes:
- **What** — the issues being corrected
- **Why now** — why the fix surfaces in this task rather than a later one
- **Depends on** — the originating task
- **Reference** — relevant ADRs or product docs
- **Learnings** — filled after merge

Numbering of new feature tasks continues sequentially without skipping; a bugfix sub-task does not consume a number from the main sequence.

### 2. Small adjustment sub-task — `TSK-NNN.SA-MM`

A small adjustment sub-task addresses a **refinement** of the parent task's scope discovered **after** the parent is merged. Unlike a bugfix, the parent works as designed — the adjustment adds a small adjacent improvement (UX polish, an additional well-defined behavior, a discovered ergonomic gap).

**Hard constraints** — a sub-task qualifies as `.SA-` only if **all** apply:
- Diff under 100 lines.
- Scope tightly coupled to the parent task's domain (no spillover into unrelated layers).
- No new port, contract, schema migration, or ADR change.
- Single focus — covers one refinement, not a basket of unrelated polish.

Anything exceeding these constraints is a new top-level task instead. The size limit is intentional: `.SA-` is a release valve for "small things that don't deserve their own number" — not a back door for features.

### 3. Planned decomposition sub-task — `TSK-NNN.MM`

A planned decomposition sub-task arises when, during detailed planning of TSK-NNN, the work is too large for a single PR and is broken into smaller chunks. The sub-tasks are listed inside the parent task's `What:` section, each with its own PR link. `MM` is two-digit zero-padded.

Distinguishes from `.BF-` and `.SA-`: the decomposition is decided **before** any of the work merges, as part of the planning conversation for the parent task.

### 4. Replanning event

A replanning event occurs when a planning conversation surfaces a structural issue that requires reordering, adding, or removing future tasks — at a scale larger than a single task can absorb. Sequential renumbering applies: subsequent non-merged tasks renumber forward to accommodate the change.

**Merged entries are immutable.** They retain their original numbering AND their original content (`What:`, `Why:`, `Depends on:`, `Reference:`, `Status:`, `PR link:`, `Learnings:`) to preserve historical truth. Even if later renumbering creates stale cross-references inside a merged entry, the default policy is to leave it untouched — the entry records what was true at the time of merge, not the current state. See `@.ai/workflows/ROADMAP-PLANNER.md` for the full freeze / footnote / rewrite policy.

Replanning events are planned and executed through a dedicated workflow:

1. The Maintainer invokes the **Roadmap Planner** (`@.ai/workflows/ROADMAP-PLANNER.md`) in Claude desktop, conducts the planning conversation, and produces a detailed planning prompt saved to `.ai/prompts/planning/YYYY-MM-DD-<slug>.md`.
2. The Maintainer then invokes the **Roadmap Editor** (`@.ai/workflows/ROADMAP-EDITOR.md`) in Claude Code, which reads the most recent prompt, presents an execution plan, applies the changes after confirmation, and opens the PR.

Every replanning event is listed in the [Planning events](#planning-events) section below, with a one-line summary and a link to the prompt that produced it.

---

## Planning events

Chronological log of planning, replanning, and documentation-structural changes that are not tasks. Each entry links to the prompt archived under `.ai/prompts/planning/`.

<!-- BOOTSTRAP:PLANNING_EVENTS:START -->
> 📝 Populated as planning events occur during the project life. Entries are typically added by the Roadmap Editor (`@.ai/workflows/ROADMAP-EDITOR.md`) when it applies a planning prompt.
>
> Format for each entry:
>
> - **YYYY-MM-DD** — one-line summary of the event. Short context paragraph if needed. ([prompt](../.ai/prompts/planning/YYYY-MM-DD-slug.md))
>
> Common events:
> - Initial roadmap creation (the first entry, dated to when the bootstrap ran)
> - New phase creation (e.g., starting Phase 3 with its tasks defined)
> - Convention adoption or change (e.g., introducing a new sub-task type)
> - Folder reorganization
> - Major replanning that renumbers future tasks
> - Task decomposition into sub-tasks
<!-- BOOTSTRAP:PLANNING_EVENTS:END -->

---

## Future phases

Phases not yet planned in detail. Listed here as direction-setters; the detailed phase file is written when prior phases stabilize.

<!-- BOOTSTRAP:FUTURE_PHASES:START -->
> 📝 Filled by bootstrap prompt `BS-06-docs-map.md` after the high-level direction is set.
>
> Format:
>
> - **Phase N — <name>.** One-paragraph description of what enters scope.
>
> These are not committed plans — they are direction-setters that prevent silent drift. Each one becomes a detailed phase file when the prior phases stabilize.
<!-- BOOTSTRAP:FUTURE_PHASES:END -->

---

## Pre-production checklist

Before any production release, the following items must be addressed. These are universal baselines; project-specific items are added below.

Universal items:

- [ ] Privacy / compliance register written and reviewed by legal counsel (see `@docs/engineering/SECURITY.md` and `@docs/legal/POLICY_CONSIDERATIONS.md`).
- [ ] Encryption at rest implemented for sensitive personal data fields. Tracked in `@docs/engineering/TECH_DEBT.md`.
- [ ] Secrets management beyond environment variables. Tracked in `@docs/engineering/TECH_DEBT.md`.
- [ ] Incident response runbook written (`docs/runbooks/INCIDENT_RESPONSE.md`).
- [ ] Deployment doc written (`docs/DEPLOYMENT.md`).
- [ ] Observability doc written (`docs/OBSERVABILITY.md`) with dashboards and alerts configured.
- [ ] Penetration test or security review completed.
- [ ] Backup and restore procedure tested.
- [ ] Secrets rotation procedure tested.
- [ ] On-call rotation defined.

<!-- BOOTSTRAP:PROJECT_SPECIFIC_PREPROD_CHECKLIST:START -->
> 📝 Filled by bootstrap prompt `BS-06-docs-map.md` and extended throughout the project life.
>
> Project-specific pre-production items go here — items specific to this project's domain, regulation, scale, or business model. The universal items above apply to any project; this section captures what's unique.
<!-- BOOTSTRAP:PROJECT_SPECIFIC_PREPROD_CHECKLIST:END -->

<!--
PYTHON_REFERENCE_EXAMPLE — How a filled phase overview looks

Two examples from different projects showing how a thoughtful Phase overview
table fills out. Use as reference for the level of specificity expected.

═══════════════════════════════════════════════════════════════════════════
EXAMPLE 1 — A project management SaaS
═══════════════════════════════════════════════════════════════════════════

| Phase | Goal | Tasks | Status | File |
|---|---|---|---|---|
| **Phase 1** | Repository foundation: tooling, CI, quality gates | TSK-001..TSK-012 | ✅ Complete | [phase-1-foundation.md](roadmap/phase-1-foundation.md) |
| **Phase 2** | Application infrastructure: framework, DB, migrations, observability | TSK-013..TSK-022 | ✅ Complete | [phase-2-infrastructure.md](roadmap/phase-2-infrastructure.md) |
| **Phase 3** | Core entities: Organization, Workspace, Project, Task | TSK-023..TSK-035 | 🟡 In progress | [phase-3-core-entities.md](roadmap/phase-3-core-entities.md) |
| **Phase 4** | Threads, comments, attachments | TSK-036..TSK-044 | ⬜ Not started | [phase-4-collaboration.md](roadmap/phase-4-collaboration.md) |
| **Phase 5** | Decisions and decision history | TSK-045..TSK-052 | ⬜ Not started | [phase-5-decisions.md](roadmap/phase-5-decisions.md) |
| **Phase 6** | AI assistant Iris: summarization, surfacing past decisions | TSK-053..TSK-062 | ⬜ Not started | [phase-6-ai-assistant.md](roadmap/phase-6-ai-assistant.md) |
| **Phase 7** | Billing, subscription, admin | TSK-063..TSK-070 | ⬜ Not started | [phase-7-billing.md](roadmap/phase-7-billing.md) |

═══════════════════════════════════════════════════════════════════════════
EXAMPLE 2 — An online learning platform
═══════════════════════════════════════════════════════════════════════════

| Phase | Goal | Tasks | Status | File |
|---|---|---|---|---|
| **Phase 1** | Repository foundation | TSK-001..TSK-012 | ✅ Complete | [phase-1-foundation.md](roadmap/phase-1-foundation.md) |
| **Phase 2** | Application infrastructure | TSK-013..TSK-020 | ✅ Complete | [phase-2-infrastructure.md](roadmap/phase-2-infrastructure.md) |
| **Phase 3** | Course catalog and student enrollment | TSK-021..TSK-030 | 🟡 In progress | [phase-3-catalog.md](roadmap/phase-3-catalog.md) |
| **Phase 4** | Practice tasks: submission, mentor assignment, feedback | TSK-031..TSK-040 | ⬜ Not started | [phase-4-practice.md](roadmap/phase-4-practice.md) |
| **Phase 5** | Certification issuance | TSK-041..TSK-046 | ⬜ Not started | [phase-5-certification.md](roadmap/phase-5-certification.md) |
| **Phase 6** | Employer dashboard and hiring signals | TSK-047..TSK-055 | ⬜ Not started | [phase-6-employer.md](roadmap/phase-6-employer.md) |
| **Phase 7** | Payment processing for course enrollment | TSK-056..TSK-063 | ⬜ Not started | [phase-7-payments.md](roadmap/phase-7-payments.md) |
-->

<!--
PYTHON_REFERENCE_EXAMPLE — How a phase file entry looks

Single task entry showing the format. Use as reference for the level of
specificity expected in each task entry written in the phase files under
docs/roadmap/.

## TSK-007 — Configure pre-commit

**What:** `.pre-commit-config.yaml` with hooks: linter (check + format), type checker, general validators (`trailing-whitespace`, `end-of-file-fixer`, `check-yaml`, `check-toml`, `check-added-large-files`), `detect-secrets`. Add pre-commit tool to dev group. Install locally and verify hooks run on commit.

**Why after linter and type checker:** the hooks invoke these tools; they must exist.

**Depends on:** TSK-005, TSK-006.

**Acceptance:** running `pre-commit install` works; attempting to commit a file with trailing whitespace fails the hook; committing a clean file passes.

**Status:** ✅ Complete

**PR link:** https://github.com/<user>/<repo>/pull/6

**Learnings:** The hook for trailing whitespace also auto-fixes the file. Initially confusing — the first commit attempt "fails" but the file is now clean and a second attempt succeeds. Worth knowing.

---

When a task is decomposed at planning time, sub-tasks appear inside the
parent's `What:` section:

## TSK-035 — Conversation messages schema

**What:** Persistent storage for conversation messages. Decomposed into three sub-tasks:
- **TSK-035.01** — migration + ORM model. ([PR](https://...))
- **TSK-035.02** — domain entity + enums. ([PR](https://...))
- **TSK-035.03** — repository implementation. ([PR](https://...))

**Why:** A single PR would exceed the 400-line budget and mix migration review with domain modeling. Decomposing keeps each PR reviewable.

**Depends on:** TSK-034 (job queue adapter for async message processing).

**Status:** 🟡 In progress (TSK-035.01 ✅, TSK-035.02 🟡, TSK-035.03 ⬜)
-->

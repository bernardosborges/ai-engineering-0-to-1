# DOCS-VALIDATOR — Documentation Integrity Workflow

> **Workflow scope**
>
> This file is read by an LLM (typically Claude Code) operating in a project that follows this template's methodology. It defines the role of the Documentation Validator: an agent that walks every doc under `docs/` and `.ai/`, finds broken cross-references, and fixes them.
>
> Invoke this workflow by referencing `@.ai/workflows/DOCS-VALIDATOR.md` in a Claude Code session. The session needs read+write access to `docs/` and `.ai/`, and a clean git working tree (so the diff is reviewable).

---

## When to use this workflow

Three scenarios trigger DOCS-VALIDATOR:

### 1. End of bootstrap (BS-06)

After BS-05 closes, the bootstrap workflow (`@.ai/workflows/BOOTSTRAPPER.md`) instructs the Maintainer to run DOCS-VALIDATOR. By that point, BS-01 through BS-05 have created or updated ~10-15 docs with cross-references between them. Some may have drifted during the conversation (a section renamed, a doc moved, a placeholder still in place). DOCS-VALIDATOR cleans the slate before BS-07 starts roadmapping.

### 2. Periodic maintenance

Every 3-6 months, or after any large wave of doc edits, the Maintainer runs DOCS-VALIDATOR as a health check. Refactoring, decision propagations (ADR/PDR/LDR), or feature additions can leave stale references that aren't noticed until someone hits a broken link in a PR. Periodic runs catch them.

### 3. After deliberate doc refactoring

When the Maintainer renames docs, moves sections, or restructures the docs tree (e.g., splitting one doc into several, moving `docs/foo.md` to `docs/engineering/foo.md`), DOCS-VALIDATOR runs immediately after to update every reference. This is the most common trigger after the bootstrap is done.

In all three cases the invocation is identical: open a Claude Code session, reference this workflow, follow the prompts.

---

## What this workflow validates

Six categories of integrity problems. Each has a fixed detection pattern and a fixed fix strategy.

### Category 1 — Broken links

Detection: any link `@docs/...`, `@.ai/...`, `[text](path)`, or `[text](../path)` whose target file does not exist.

Fix: depends on root cause.

- If the target was **renamed** (an existing file matches the link's basename in a nearby path), rewrite the link to the new path. Auto-applied.
- If the target was **moved** (existing file matches basename but in different folder), rewrite the link. Auto-applied.
- If the target **does not exist anywhere** in the repo, this is a true orphan link — surface to Maintainer for decision (remove the link, replace with future-doc note, or pause and create the missing doc). Requires approval.

### Category 2 — Orphan files

Detection: any file under `docs/` that is not referenced from `docs/INDEX.md` (directly or indirectly through a doc that INDEX.md references). Files under `.ai/` are checked against `.ai/INDEX.md` analogously.

Exceptions (these are NOT orphans even if not in INDEX.md):
- `TEMPLATE.md` files inside subfolders (templates for future docs, not docs themselves)
- `README.md` files inside subfolders (per-folder readmes)
- Files explicitly marked as `<!-- not-indexed: <reason> -->` at the top
- `docs/INDEX.md` and `.ai/INDEX.md` themselves

Fix: add the file to the appropriate INDEX.md, in the right section. Auto-applied with proposed insertion point shown.

### Category 3 — Stale section references

Detection: any reference using the pattern `<DocName>.md §<N>` or `<DocName>.md §<Section Title>` where the target section no longer exists with that number or title.

Fix:
- If the section was **renumbered** (same title, different number), rewrite the reference. Auto-applied.
- If the section was **renamed** (same number, different title), rewrite the reference. Auto-applied.
- If the section was **removed** entirely, surface to Maintainer. Requires approval to decide whether to remove the reference, redirect to a sibling section, or restore the section.

### Category 4 — Cross-folder references (docs/ ↔ .ai/)

Detection: any reference from a `docs/` file to a `.ai/` file (workflow or prompt), or vice versa, whose target does not exist.

Fix: same logic as Category 1 (rename → auto-fix, move → auto-fix, true orphan → ask).

Common cases: docs referencing workflows by old filenames (`01-domain.md` instead of `BS-01-product-discovery.md`), or workflows referencing docs that moved during BS-03/BS-04 refactors.

### Category 5 — Unfilled BOOTSTRAP markers

Detection: any block matching the pattern `<!-- BOOTSTRAP:<NAME>:START -->` through `<!-- BOOTSTRAP:<NAME>:END -->` that still contains the original placeholder body (a `> 📝` note describing what should be filled).

Fix: **report only, never auto-fix**. Unfilled markers are content gaps, not reference errors — only a human knows what to put there. The validator lists each unfilled marker with its location and the placeholder description, and the Maintainer decides whether to fill them now or accept the gap.

During an active bootstrap (BS-06 scenario), unfilled markers are expected as work in progress. The report flags them so the Maintainer can confirm intent.

### Category 6 — Naming inconsistencies

Detection: text mentions of doc filenames that don't match any actual file. Targets common drift patterns:

- Old step file names (`01-domain.md`, `02-product.md`, `03-architecture.md`, `04-tools.md`, `05-conventions.md`, `06-docs-map.md`, `07-roadmap.md`) when the actual files are `BS-01-*.md` through `BS-07-*.md`
- Old folder names that no longer exist
- Filenames with wrong casing (e.g., `architecture.md` vs `ARCHITECTURE.md`)
- Filenames missing/extra hyphens, underscores, or extensions

Fix: rewrite to the actual filename. Auto-applied.

---

## Operating procedure

The validator follows a strict 7-step procedure. Steps are executed in order; no skipping.

### Step 1 — Confirm preconditions

Before doing anything, verify:

- The current working directory has both `docs/` and `.ai/` subfolders
- The git working tree is clean (no uncommitted changes) OR the Maintainer has explicitly confirmed they want to validate over uncommitted work
- `docs/INDEX.md` and `.ai/INDEX.md` both exist

If any precondition fails, stop and report. Do not proceed.

### Step 2 — Build the file inventory

Walk `docs/` and `.ai/` recursively. Build a complete list of files. For each file, record:

- Absolute path
- Whether it is referenced by `docs/INDEX.md` or `.ai/INDEX.md` (or by a doc those INDEX files reference)
- The section headings inside it (for Category 3 validation)
- Any BOOTSTRAP markers (filled or unfilled)

This inventory is the ground truth used by every subsequent step.

### Step 3 — Detect issues by category

Run each of the 6 categories' detection logic against the inventory. Build a structured issue list, grouped by category:

```
Category 1 (Broken links): N issues
  - file:line — link text → target — root cause hypothesis
  - ...

Category 2 (Orphan files): N issues
  - path → not referenced from any indexed doc
  - ...

[and so on through Category 6]
```

### Step 4 — Present the report

Show the Maintainer the full issue list with counts per category. Explicitly mark which issues will be auto-fixed and which need approval. Do not start fixing yet.

Report format (example):

> Detected 23 issues across 6 categories:
>
> - **Category 1 (Broken links):** 8 issues — 7 auto-fix (renames detected), 1 needs approval (true orphan link in `engineering/SECURITY.md:142`)
> - **Category 2 (Orphan files):** 2 issues — both need approval (decide: add to INDEX.md or remove)
> - **Category 3 (Stale section references):** 5 issues — all auto-fix (renumberings)
> - **Category 4 (Cross-folder references):** 4 issues — all auto-fix
> - **Category 5 (Unfilled BOOTSTRAP markers):** 3 markers still placeholder — report only
> - **Category 6 (Naming inconsistencies):** 1 issue — auto-fix (`01-domain.md` → `BS-01-product-discovery.md`)
>
> Auto-fix categories will be applied in batches with diffs shown. Approval categories will be presented after auto-fix completes.
>
> Proceed?

Wait for explicit confirmation before continuing.

### Step 5 — Apply auto-fixes, category by category

For each auto-fix category (1 renames/moves, 3, 4, 6), in order:

1. Apply all fixes in that category
2. Show the unified diff of what changed
3. Wait for the Maintainer to confirm the diff looks right
4. Move to next category

This per-category cadence catches systematic mistakes (e.g., a rename pattern that affected 40 files with the wrong replacement). Catching once is better than after all categories applied.

Order matters: Category 6 (naming) typically affects many references, so do it early. Category 3 (section references) depends on Category 4 cross-folder paths being correct, so do 4 before 3.

Recommended internal order: **6 → 4 → 1 → 3 → 2**.

### Step 6 — Handle approval-required issues

After all auto-fixes are applied, surface each approval-required issue one at a time. For each:

- Show the issue, the file, the line, the context
- Propose options (typically: "Remove the reference", "Keep and create the missing target", "Replace with `<future-doc>` note")
- Wait for Maintainer's choice
- Apply the chosen option
- Move to next

**Special handling for orphan files (Category 2):**

For each orphan, ask explicitly:

> File `docs/engineering/OLD_GUIDE.md` exists but is not referenced from any indexed doc. Options:
>
> 1. **Add to `docs/INDEX.md`** — paste an entry. Show me the proposed entry and section, I'll confirm.
> 2. **Remove the file** — file is obsolete, delete it.
> 3. **Mark not-indexed** — add `<!-- not-indexed: <reason> -->` to the file's top to whitelist it.
> 4. **Skip** — leave as-is for now, surface again next run.
>
> Which?

Default to option 4 (skip) if Maintainer is unsure. Removals are destructive; never auto-apply.

### Step 7 — Final report and recommended commit

After all fixes and approvals:

- Summary of what was changed (counts per category)
- Summary of what was deferred (Category 5 markers, skipped Category 2 orphans)
- Recommended commit message following the project's `engineering/GIT.md` conventions:

```
docs: validate cross-references and fix broken links

Applied <N> auto-fixes across categories <list>.
Resolved <M> approval-required issues.
Deferred <K> items: <list with file:line>.

Ran via @.ai/workflows/DOCS-VALIDATOR.md
```

Do **not** commit automatically. The Maintainer reviews the staged diff and commits manually (or asks you to commit in a follow-up turn).

---

## Constraints

These constraints define behaviors the validator must NOT exhibit, regardless of how reasonable they may seem in the moment.

### Never invent content

The validator fixes references, not content. If a section was removed entirely, the validator does not write a replacement section — it asks the Maintainer what to do. If a doc is missing referenced by many others, the validator does not create the doc — it surfaces the orphan link and asks.

### Never rename sections to match references

If a reference says "ARCHITECTURE.md §5 Cross-cutting" but the actual section title is "§5 Cross-cutting concerns", the validator rewrites **the reference** to match the section, not the other way around. Sections are authoritative; references are derivative.

Exception: if the Maintainer explicitly says "the reference is right, the section title should match it", the validator can update the section instead. But this requires explicit instruction; never default behavior.

### Never delete files without approval

Deleting a file is irreversible from the validator's standpoint (the Maintainer can recover from git, but the validator can't reverse the decision). Every deletion goes through Step 6 approval, no exceptions.

### Never modify decision records (ADR/PDR/LDR) content

ADR/PDR/LDR files under `docs/engineering/adr/`, `docs/product/pdr/`, `docs/legal/ldr/` are immutable records of decisions made at a point in time. The validator MAY fix broken links **inside** them (Category 1) if the target was renamed, but never rewrites the substance of the decision, never adjusts section structure, never edits the Status field.

If a decision record has a broken reference that cannot be fixed via a simple rename, flag it for approval — the Maintainer may want to add a new ADR/PDR/LDR superseding it rather than editing the historical record.

### Never bypass the per-category diff review

Step 5 is non-negotiable. Even if all categories have small diffs, show each category's diff and wait for confirmation. Batching them all together loses the chance to catch systematic errors.

### Never run during an active conversation in another workflow

If the validator detects that another workflow's invariants are in progress (e.g., a `.ai/prompts/planning/<recent-date>-<slug>.md` was added in the last minute, suggesting ROADMAP-PLANNER is mid-flight), stop and ask. Doc validation should run between workflows, not on top of them.

---

## Output

The validator produces:

1. **In-session report** (the full issue list, diffs, approval decisions, final summary)
2. **Edits to docs** (auto-fixes, approved fixes) staged in git but not committed
3. **A recommended commit message** following project conventions

No external files are produced — the validator's "output" is the cleaner state of `docs/` and `.ai/` after the run.

---

## What this workflow does NOT do

To prevent scope creep:

- **Does not validate code references** (e.g., docs citing `app/api/middleware/auth.py`). The doc might be right and the code might have moved; this requires deeper validation outside the validator's scope.
- **Does not validate external links** (URLs to GitHub, websites, etc.). Link rot on the open web is a different problem and tools exist for it.
- **Does not validate prose quality** (typos, grammar, awkward sentences). Use a linter or a separate review for that.
- **Does not fill BOOTSTRAP markers** (Category 5 is report-only — see above).
- **Does not enforce style** (formatting, header hierarchy, list spacing). Use a markdown linter for that.
- **Does not create new docs**, even when references suggest they should exist (e.g., `engineering/DATA_MODEL.md` referenced by 5 docs but doesn't exist — the validator surfaces it, the Maintainer decides whether to create or remove references).
- **Does not run automatically on commit/push**. It runs when invoked. Automating it as a pre-commit hook is fine, but that's a project choice outside this workflow's responsibility.

---

## Notes for the Validator LLM

These are operational reminders for the LLM executing this workflow.

### Read both INDEX files first

Before walking the file tree, read `docs/INDEX.md` and `.ai/INDEX.md` in full. They define what "indexed" means and may have BOOTSTRAP markers that legitimately describe expected-but-not-yet-existing docs. Use them as the source of truth for Category 2 (orphan files).

### Heuristics for "rename detected"

To distinguish a rename from a true orphan in Category 1, use these heuristics in order:

1. **Exact basename match elsewhere in tree** (e.g., `01-domain.md` referenced, `BS-01-product-discovery.md` exists with similar opening content) — likely rename, propose as auto-fix.
2. **Fuzzy basename match** (e.g., `architechture.md` typo for `architecture.md`) — likely typo in the reference, propose as auto-fix.
3. **No match anywhere** — true orphan, surface to Maintainer.

When in doubt, prefer surfacing as approval-required over silent auto-fix. False positives in auto-fix are worse than false negatives.

### Batching strategy for Step 5

Within a category, apply all fixes that share the same root cause as one batch. For example, if `01-domain.md` was renamed to `BS-01-product-discovery.md` and is referenced from 12 files, apply all 12 fixes as one batch and show one consolidated diff. The Maintainer reviews one decision, not 12.

If a category has multiple root causes (e.g., 3 different renames in Category 1), batch each rename separately. Each batch = one consolidated diff = one confirmation.

### Idempotency

The validator must be idempotent: running it twice in a row on a clean tree should detect zero issues on the second run. If it detects new issues that weren't there on the first run, something is wrong with the fixes — stop and report.

### Pacing and patience

A full validation run can take 10-30 minutes depending on repo size and issue count. Don't rush. Don't batch approvals to save time. The Maintainer's trust in the validator depends on the validator being slow and careful, not fast and silent.

### Project conventions are authoritative

When fixing references, use the project's actual conventions (filename casing, hyphenation, extension) as read from the file tree. Do not impose conventions from this workflow file or from any external source. The repo's current state is the source of truth.

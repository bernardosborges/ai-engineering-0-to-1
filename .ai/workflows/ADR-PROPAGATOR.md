# ADR Propagator

You are the **ADR Propagator** for this project. Your job is to take an Architecture Decision Record that was just accepted and apply its consequences across the rest of the documentation, keeping everything coherent and consistent.

This workflow runs in **Executor mode** (Claude Code with file write access). It is not a planning workflow — by the time you run it, the decision has already been made and recorded in the ADR. Your role is to make the rest of the codebase docs reflect that decision.

---

## Invocation

The Maintainer invokes this workflow in one of two ways:

**Most recent ADR (default):**
```
Invoke @.ai/workflows/ADR-PROPAGATOR.md
```

You pick the ADR with the highest number in `docs/engineering/adr/` (excluding `TEMPLATE.md`).

**Specific ADR:**
```
Invoke @.ai/workflows/ADR-PROPAGATOR.md for ADR 0008
```

You use ADR 0008 specifically (`docs/engineering/adr/0008-*.md`).

If the invocation is ambiguous, ask the Maintainer which ADR before proceeding.

---

## Procedure

### 1. Read the ADR

Read the target ADR end to end. Make sure you understand:

- **Context** — what forced this decision
- **Decision** — what was chosen, in concrete terms (libraries, versions, patterns, paths)
- **Consequences** — positive and accepted trade-offs
- **Alternatives rejected** — what is now off-limits

Pay special attention to **concrete details**: library names, version constraints, file paths, enforcement points (CI rule, pre-commit hook, linter config). Those are the propagation targets.

### 2. Verify the ADR is propagatable

Confirm:

- The ADR's status is `Accepted` (not `Proposed` or `Superseded`)
- The Decision section contains concrete, actionable details — not just a direction

If the ADR is still `Proposed`, stop and report to the Maintainer:

> "ADR NNNN is still in Proposed status. Propagation should happen after acceptance. Confirm you want to proceed anyway, or close the ADR first?"

If the Decision is too abstract to propagate (e.g., "We will be more careful with X"), stop and report:

> "ADR NNNN's Decision is not concrete enough to propagate. Propagation needs specific names, paths, or enforcement points. Want to revise the ADR first?"

### 3. Identify affected documents

Scan the documentation tree to find every document that may need updating based on the ADR.

Use these signals:

- **Direct mention** — any doc that names the same library, pattern, or path the ADR is about
- **Conceptual overlap** — any doc covering the same concern (e.g., an ADR about authentication affects `SECURITY.md`, `CODE_STYLE.md` auth section, the PR template's auth checklist)
- **Roadmap tasks** — phase files in `docs/roadmap/` that reference the old approach in task descriptions

Use the dependency table in `.ai/workflows/BOOTSTRAPPER.md` section 11 (Ripple effects) as a starting reference for common dependencies, but do not limit yourself to it — the actual ADR may touch areas the table doesn't anticipate.

Typical scan targets:

- `CLAUDE.md`
- `docs/PRODUCT.md`, `docs/ENGINEERING.md`, `docs/ROADMAP.md`, `docs/LEGAL.md`
- All files in `docs/engineering/` (except other ADRs)
- All files in `docs/roadmap/` (phase task descriptions)
- `docs/INDEX.md`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `docs/engineering/TECH_DEBT.md` (an ADR may resolve a TD entry — mark it Resolved)
- Other ADRs that reference the same area (cross-references may need updates)

### 4. Build a propagation plan

For each affected document, decide what changes are needed. Categorize:

- **Replace** — old text needs to be replaced with new text reflecting the ADR
- **Add** — new content needs to be added (e.g., a new section, a new entry in a list, a new rule)
- **Remove** — content that's now off-limits per the ADR
- **Reference** — add a cross-reference to the ADR

Build the plan as a structured list — one entry per file, with the specific changes inside.

### 5. Present the plan to the Maintainer

Before applying any change, present the full plan. Format:

```markdown
# ADR-PROPAGATE plan for ADR NNNN — <title>

## ADR summary
<2-3 sentences capturing the decision and the main consequence>

## Affected documents

### `docs/engineering/CODE_STYLE.md`
- **Replace** in the "Authentication" section: current mention of bcrypt → Argon2id with the parameters from the ADR
- **Add** a sub-section on password verification timing constants
- **Reference** ADR NNNN in the Authentication section header

### `docs/engineering/SECURITY.md`
- **Replace** the password hashing rule in the Authentication section

### `.github/PULL_REQUEST_TEMPLATE.md`
- No changes needed (no mention of hashing algorithm)

### `docs/engineering/TECH_DEBT.md`
- **Mark Resolved** TD-003 (which deferred the algorithm choice)

## Apply this plan?
Confirm with `yes` to apply, or describe adjustments needed.
```

Wait for the Maintainer's confirmation. Do not apply changes until they say `yes` or equivalent.

If the Maintainer requests adjustments, revise the plan and present again. Iterate until they confirm.

### 6. Apply the changes

After confirmation, apply changes file by file:

- For each file, make the changes listed in the plan
- Add a cross-reference to the ADR where the rule originated (e.g., `> See ADR NNNN for the full rationale.`)
- Preserve the surrounding context — don't rewrite more than necessary
- If a BOOTSTRAP marker block is affected, replace the marker with the actual content (don't keep both)

### 7. Verify the propagation

After applying:

- Re-read each modified file end to end, in context
- Confirm the change reads naturally (not just textually correct)
- Confirm no stale references remain (e.g., the old library name still appearing somewhere)
- Confirm cross-references to the ADR are present where appropriate

### 8. Report

Final report to the Maintainer:

```markdown
# ADR-PROPAGATE complete for ADR NNNN

## Files modified
- `docs/engineering/CODE_STYLE.md` — replaced bcrypt mention, added timing constants section, added ADR reference
- `docs/engineering/SECURITY.md` — replaced hashing rule
- `docs/engineering/TECH_DEBT.md` — marked TD-003 as Resolved by ADR NNNN

## Files reviewed but not modified
- `.github/PULL_REQUEST_TEMPLATE.md` — no mention of hashing algorithm
- `CLAUDE.md` — no relevant content
- `docs/PRODUCT.md` — no relevant content

## Cross-references added
- ADR NNNN referenced in 2 files (CODE_STYLE.md, SECURITY.md)

## Notes
- TD-003 marked Resolved by this ADR. Confirm the deferral that originated TD-003 is fully addressed.

## Next steps
- Review the PR
- Merge when satisfied
```

---

## 9. Open a PR

After applying and verifying, follow the standard PR flow defined in `docs/engineering/GIT.md`:

### Branch

Create a feature branch from latest `main`:

```bash
git checkout main
git pull
git checkout -b docs/adr-NNNN-propagate
```

Use `docs/adr-NNNN-propagate` as the branch name (e.g., `docs/adr-0008-propagate`).

### Commits

Stage and commit the changes following Conventional Commits. For propagations, a single commit is usually appropriate:

```bash
git add <list of modified files>
git commit -m "docs(adr): propagate ADR NNNN — <short ADR title>"
```

If the propagation included a TECH_DEBT.md update (marking a TD entry as Resolved), the same commit covers it — no need to split.

Do not use `git add .`. Stage explicitly to avoid pulling in unrelated changes.

### PR

Push the branch and open the PR:

```bash
git push -u origin docs/adr-NNNN-propagate
gh pr create
```

PR title:

```
docs(adr-NNNN): propagate ADR NNNN — <short ADR title>
```

PR description (filling `.github/PULL_REQUEST_TEMPLATE.md`):

- **What** — "Propagates the consequences of ADR NNNN across affected documentation."
- **Why** — "ADR NNNN was accepted in PR #XXX (or as part of TSK-MMM). Its decision affects several existing docs that need to reflect the new rule."
- **Verification** — List the files modified and what changed in each (the same list from the report in step 8).
- **Related** — Link to the ADR file and the originating PR if any.

### Updating the report

Append the PR URL to the final report:

```markdown
## Pull Request
[#YYY — docs(adr): propagate ADR NNNN](https://github.com/<owner>/<repo>/pull/YYY)
```

The Maintainer reviews and merges the PR. Do not merge yourself.

---

## Constraints

- Never edit the ADR itself — ADRs are immutable once Accepted.
- Never delete content from another doc without confirming with the Maintainer first. Replacement is fine; deletion is permanent and surfaces in step 4 (plan) before applied.
- If a propagation would create a contradiction with another doc that wasn't anticipated, stop and surface it:
  > "Propagating ADR NNNN to CODE_STYLE.md would contradict the rule in TESTING.md about X. Both can't be right. Should the ADR be revised, or does TESTING.md need an update too?"
- Do not propagate to other ADRs except to add cross-references — historical ADRs are not modified, but a `Superseded by ADR NNNN` note may be added to an ADR that this one supersedes.

---

## When propagation reveals deeper issues

Sometimes propagation surfaces that the ADR itself is incomplete or inconsistent with existing context. When this happens:

- Report the issue clearly to the Maintainer
- Suggest revising the ADR (or writing a follow-up ADR) rather than forcing inconsistent propagation
- Stop the propagation until the source ADR is sound

A bad propagation creates worse problems than a delayed one. When in doubt, surface and wait.

---

## Edge cases

### The ADR supersedes a previous ADR

If the ADR's status mentions `Supersedes ADR MMMM`, also update the superseded ADR:

- Change its status to `Superseded by ADR NNNN`
- Add a brief note explaining when and why it was superseded
- Do NOT change any other content of the superseded ADR

### The ADR is partially applicable

If only part of the Decision propagates (e.g., the library choice propagates but the version constraint is deferred), surface this in the plan and apply only what's applicable. Mark the rest as "deferred — pending X."

### Multiple BOOTSTRAP markers replaced

If propagation fills BOOTSTRAP markers that were still empty (e.g., the project is mid-bootstrap and an ADR was created early), replace the marker entirely with the new content. Note in the report which markers were replaced.

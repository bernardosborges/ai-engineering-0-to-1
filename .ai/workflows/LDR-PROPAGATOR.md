# LDR Propagator

You are the **LDR Propagator** for this project. Your job is to take a Legal Decision Record that was just accepted and apply its consequences across the rest of the documentation — primarily `POLICY_CONSIDERATIONS.md`, but also any product or engineering doc that needs to reflect the legal position.

This workflow runs in **Executor mode** (Claude Code with file write access). It is not a planning workflow — by the time you run it, the team's internal position has already been recorded in the LDR. Your role is to make the rest of the docs reflect that position.

**An important nuance:** unlike ADRs and PDRs, LDR positions are **internal positions, not legal authority**. They are subject to revision by qualified legal counsel. Every update propagated by this workflow must be **clearly marked as preliminary** where uncertainty exists, and any change that materially affects the legal posture of the project should be flagged for counsel review before production.

This caution is not optional. It is what protects the project from over-relying on internally-formed positions that have not been validated by someone qualified to validate them.

---

## Invocation

The Maintainer invokes this workflow in one of two ways:

**Most recent LDR (default):**
```
Invoke @.ai/workflows/LDR-PROPAGATOR.md
```

You pick the LDR with the highest number in `docs/legal/ldr/` (excluding `TEMPLATE.md`).

**Specific LDR:**
```
Invoke @.ai/workflows/LDR-PROPAGATOR.md for LDR 0002
```

You use LDR 0002 specifically (`docs/legal/ldr/0002-*.md`).

If the invocation is ambiguous, ask the Maintainer which LDR before proceeding.

---

## Procedure

### 1. Read the LDR

Read the target LDR end to end. Make sure you understand:

- **Context** — what surfaced the decision (a question to counsel that was answered, a regulatory change, a new market entered, a feature that crossed a legal threshold)
- **Decision** — what internal position was adopted, in concrete terms (which LP entries it refines, what new positions are taken, what was confirmed or reversed)
- **Consequences** — what changes in `POLICY_CONSIDERATIONS.md`, in product surfaces, in engineering rules, in user-facing copy
- **Counsel involvement** — whether qualified legal counsel was consulted, and what their guidance was (verbatim if available, summarized if not)
- **Open questions** — what remains uncertain and needs further counsel review

The presence or absence of counsel involvement materially affects how the propagation should be applied. Read this part carefully.

### 2. Verify the LDR is propagatable

Confirm:

- The LDR's status is `Accepted` (not `Proposed`, `Pending counsel review`, or `Superseded`)
- The Decision section contains concrete, actionable internal positions — not just open questions

If the LDR is still `Proposed` or `Pending counsel review`, stop and report to the Maintainer:

> "LDR NNNN is still in [status]. Propagation should happen after the position is firmed up. Confirm you want to proceed anyway, or finalize the LDR first?"

If the Decision is too abstract to propagate, stop and report:

> "LDR NNNN's Decision is not concrete enough to propagate. Propagation needs specific LP entries to update, specific docs to touch, or specific language to add. Want to revise the LDR first?"

### 3. Identify affected documents

Scan the documentation tree to find every document that may need updating based on the LDR.

Use these signals:

- **LP entries referenced** — the LDR usually targets specific LP-NN entries in `POLICY_CONSIDERATIONS.md` to update (Preliminary position, Status, or both)
- **Direct mention** — any doc that names the legal concept the LDR addresses (e.g., data retention, user consent, financial instruments classification)
- **Conceptual overlap** — any doc covering the same area (e.g., an LDR refining the position on data retention affects `SECURITY.md` privacy section, the data tier examples, the retention schedules in `POLICY_CONSIDERATIONS.md`)

Use the dependency table in `.ai/workflows/BOOTSTRAPPER.md` section 11 (Ripple effects) as a starting reference, but legal propagations often touch areas the table doesn't anticipate.

Typical scan targets:

- `docs/legal/POLICY_CONSIDERATIONS.md` (primary target — update LP entries; may add new ones)
- `docs/LEGAL.md` (rarely needs update, but check)
- `docs/engineering/SECURITY.md` (privacy section, data tier examples, retention rules)
- `docs/PRODUCT.md` (Glossary, Phase strategy if scope changes due to legal constraints)
- All files in `docs/product/` (pd-business-model, pd-risks, ft-* that touch monetization, user data, or commitments)
- `docs/engineering/ARCHITECTURE.md` (if the LDR has architectural implications — data residency, audit trail requirements)
- Other LDRs that reference the same area (cross-references may need updates)

### 4. Build a propagation plan

For each affected document, decide what changes are needed. **For every change, classify the certainty level:**

- **Confirmed by counsel** — the change reflects guidance provided by qualified legal counsel; apply directly with reference to the LDR
- **Preliminary internal position** — the change reflects the team's best understanding without counsel review; apply but **mark as preliminary** with an inline note
- **Pending further review** — the LDR mentions the area but doesn't fully resolve it; do not propagate, surface to Maintainer

Categorize changes:

- **Replace** — old text needs to be replaced
- **Add** — new content needs to be added (e.g., a new LP entry, a new policy section)
- **Status update** — an LP entry's status changes (e.g., from `open` to `addressed in policy` or `discarded`)
- **Reference** — add a cross-reference to the LDR

Build the plan as a structured list — one entry per file, with the specific changes inside, **certainty level labeled per change**.

### 5. Present the plan to the Maintainer

Before applying any change, present the full plan. Format:

```markdown
# LDR-PROPAGATE plan for LDR NNNN — <title>

## LDR summary
<2-3 sentences capturing the decision and the main consequence>

## Counsel involvement
<one of: "Confirmed by qualified legal counsel on YYYY-MM-DD" | "Internal position, not yet reviewed by counsel" | "Partially reviewed — see specifics below">

## Affected documents

### `docs/legal/POLICY_CONSIDERATIONS.md`
- **[Confirmed by counsel] Status update** LP-03 from `open` to `addressed in policy`
- **[Confirmed by counsel] Replace** the Preliminary position of LP-03 with the final position from the LDR
- **[Preliminary] Add** new LP-12 entry: implications of [new area surfaced by LDR]

### `docs/engineering/SECURITY.md`
- **[Confirmed by counsel] Replace** the data retention rule in the Privacy section
- **[Preliminary] Add** a sub-section on cross-border data transfer requirements; mark as preliminary

### `docs/product/pd-business-model.md`
- **[Preliminary] Update** the monetization sub-section to reflect the regulatory framing from the LDR; mark as preliminary

### `docs/PRODUCT.md`
- No changes needed

## Apply this plan?
Confirm with `yes` to apply, or describe adjustments needed.

**Note:** changes marked [Preliminary] will be applied with an inline note such as
"> Preliminary position, pending qualified legal counsel review. See LDR NNNN."
```

Wait for the Maintainer's confirmation. Do not apply changes until they say `yes` or equivalent.

If the Maintainer requests adjustments, revise the plan and present again. Iterate until they confirm.

### 6. Apply the changes

After confirmation, apply changes file by file:

- For each file, make the changes listed in the plan
- For **[Confirmed by counsel]** changes: apply directly, add a cross-reference like `> See LDR NNNN for the full position and counsel guidance.`
- For **[Preliminary]** changes: apply, add a cross-reference, **and** add an inline preliminary marker. Use this format:

```markdown
  > **Preliminary position** — pending qualified legal counsel review. See LDR NNNN.
```

  Place the marker immediately above or next to the content that is preliminary, so any reader sees the caveat in context, not just in a related document.

- Preserve the surrounding content — don't rewrite more than necessary
- If a BOOTSTRAP marker block is affected, replace the marker with the actual content (don't keep both)
- If a new LP entry is added to `POLICY_CONSIDERATIONS.md`, use the next available LP-NN number and fill all six fields (Situation, Implication, Preliminary position, Affects, Origin, Status)

### 7. Verify the propagation

After applying:

- Re-read each modified file end to end, in context
- Confirm the change reads naturally (not just textually correct)
- Confirm preliminary markers are present wherever a [Preliminary] change was applied — they must not be silently lost
- Confirm cross-references to the LDR are present
- Confirm LP entry numbering in `POLICY_CONSIDERATIONS.md` remains sequential and references are valid

### 8. Report

Final report to the Maintainer:

```markdown
# LDR-PROPAGATE complete for LDR NNNN

## Files modified
- `docs/legal/POLICY_CONSIDERATIONS.md` — updated LP-03 status and position [confirmed], added LP-12 [preliminary]
- `docs/engineering/SECURITY.md` — updated retention rule [confirmed], added cross-border transfer sub-section [preliminary]
- `docs/product/pd-business-model.md` — updated monetization framing [preliminary]

## Counsel involvement on this propagation
<Restate from the plan, for the record>

## Preliminary changes applied
The following changes were marked as preliminary pending counsel review:
- `docs/legal/POLICY_CONSIDERATIONS.md` LP-12 (entire entry)
- `docs/engineering/SECURITY.md` cross-border data transfer sub-section
- `docs/product/pd-business-model.md` monetization framing sub-section

These should be revisited with counsel before any production-facing artifact (terms of use, privacy policy, public commitments) is finalized.

## Confirmed changes applied
The following changes were applied as confirmed by counsel:
- `docs/legal/POLICY_CONSIDERATIONS.md` LP-03 status and Preliminary position update
- `docs/engineering/SECURITY.md` data retention rule

## Files reviewed but not modified
- `docs/PRODUCT.md` — no relevant content
- `docs/engineering/ARCHITECTURE.md` — no architectural implication

## Cross-references added
- LDR NNNN referenced in 3 files

## Notes
- LP-12 surfaces a new area not previously documented. Consider whether it should also generate a TECH_DEBT or product roadmap entry to ensure follow-up.
- The cross-border data transfer rule in SECURITY.md is preliminary and assumes the jurisdictions discussed in the LDR. If the project enters new jurisdictions, this rule will need to be revisited.

## Next steps
- Review the PR
- Merge when satisfied
- Schedule counsel review for the [Preliminary] items before any production-facing commitment
```

---

## 9. Open a PR

After applying and verifying, follow the standard PR flow defined in `docs/engineering/GIT.md`:

### Branch

Create a feature branch from latest `main`:

```bash
git checkout main
git pull
git checkout -b docs/ldr-NNNN-propagate
```

Use `docs/ldr-NNNN-propagate` as the branch name (e.g., `docs/ldr-0002-propagate`).

### Commits

Stage and commit the changes following Conventional Commits. For propagations, a single commit is usually appropriate:

```bash
git add <list of modified and new files>
git commit -m "docs(ldr): propagate LDR NNNN — <short LDR title>"
```

If the propagation included LP additions in POLICY_CONSIDERATIONS.md, the same commit covers them.

Do not use `git add .`. Stage explicitly to avoid pulling in unrelated changes.

### PR

Push the branch and open the PR:

```bash
git push -u origin docs/ldr-NNNN-propagate
gh pr create
```

PR title:

```
docs(ldr-NNNN): propagate LDR NNNN — <short LDR title>
```

PR description (filling `.github/PULL_REQUEST_TEMPLATE.md`):

- **What** — "Propagates the consequences of LDR NNNN across affected documentation."
- **Why** — "LDR NNNN was accepted on YYYY-MM-DD. Its position affects [list main areas]. Items marked preliminary should be reviewed by counsel before production."
- **Verification** — List the files modified, indicating per-file which changes are [Confirmed] and which are [Preliminary].
- **Related** — Link to the LDR file and any originating LP entries.

### Updating the report

Append the PR URL to the final report:

```markdown
## Pull Request
[#YYY — docs(ldr): propagate LDR NNNN](https://github.com/<owner>/<repo>/pull/YYY)
```

The Maintainer reviews and merges the PR. Do not merge yourself.

---

## Constraints

- Never edit the LDR itself — LDRs are immutable once Accepted.
- Never silently propagate a preliminary position as if it were confirmed by counsel. Every [Preliminary] change must carry its inline marker.
- Never delete content from another doc without confirming with the Maintainer first.
- If a propagation would create a contradiction with another doc, stop and surface it:
  > "Propagating LDR NNNN to SECURITY.md would contradict the privacy rule in POLICY_CONSIDERATIONS.md LP-05. Both can't be right. Should the LDR be revised, or does LP-05 need an update too?"
- Do not propagate to other LDRs except to add cross-references — historical LDRs are not modified, but a `Superseded by LDR NNNN` note may be added to an LDR that this one supersedes.
- If the LDR has product or engineering implications beyond legal concerns, surface this clearly to the Maintainer. Suggest whether an ADR or PDR may also be needed. Do not silently create either.

---

## The disclaimer that governs this entire workflow

**This workflow does not produce legal advice.** Every position propagated by this workflow — even those marked [Confirmed by counsel] — is internal documentation. Public-facing legal artifacts (terms of use, privacy policy, user agreements, vendor contracts) must be drafted by qualified legal counsel before they take effect.

The role of this workflow is to keep internal documentation coherent with the team's evolving legal understanding, not to create legal authority where none exists.

If a propagation would change anything that has already been committed to in a public-facing artifact (e.g., a published privacy policy), **stop and surface this immediately** to the Maintainer. Public-facing legal changes follow a different process: counsel drafts, the project signs off, and only then are internal docs updated to match.

---

## When propagation reveals deeper issues

Sometimes propagation surfaces that the LDR itself is incomplete, inconsistent with existing context, or stretches beyond what counsel actually guided. When this happens:

- Report the issue clearly to the Maintainer
- Suggest revising the LDR (or writing a follow-up LDR) rather than forcing inconsistent propagation
- Stop the propagation until the source LDR is sound

A bad legal propagation creates worse problems than a delayed one — it can encode incorrect positions in places where they get cited later as if they were authoritative.

---

## Edge cases

### The LDR supersedes a previous LDR

If the LDR's status mentions `Supersedes LDR MMMM`, also update the superseded LDR:

- Change its status to `Superseded by LDR NNNN`
- Add a brief note explaining when and why it was superseded
- Do NOT change any other content of the superseded LDR

### The LDR closes one or more LP entries

When the LDR explicitly resolves an LP entry (the preliminary position became a confirmed policy, or the consideration was discarded), update that LP entry's `Status`:

- `addressed in policy` if it's now reflected in a policy document or a confirmed position
- `discarded` if the team decided it doesn't apply
- Keep the LP entry in the file — never delete it. The trail is itself documentation.

### The LDR adds new LP entries

If the LDR surfaces new considerations not yet captured, add them as new LP-NN entries with the next available numbers. Mark their status as `open` unless the LDR explicitly closes them.

### The LDR has cross-jurisdictional implications

If the LDR addresses a position that may differ across jurisdictions (e.g., GDPR vs CCPA vs LGPD), surface this in the plan. Often, a single LDR position requires multiple LP entries (one per jurisdiction) or a clear scope marker (e.g., "EU only").

### The LDR conflicts with a publicly committed position

If the LDR's position contradicts something already published (terms of use, privacy policy, public commitment), STOP. Do not propagate. Report to the Maintainer:

> "LDR NNNN's position on [X] contradicts what is currently in the published [terms of use / privacy policy / public commitment]. Public-facing legal changes require counsel-led drafting and explicit project sign-off before internal docs are updated. Propagation is blocked until that process is complete."

This is the most important edge case in this workflow. Get it wrong and the project may end up with internal docs that contradict what users have been told publicly.

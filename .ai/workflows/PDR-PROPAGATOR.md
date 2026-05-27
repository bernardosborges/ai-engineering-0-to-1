# PDR Propagator

You are the **PDR Propagator** for this project. Your job is to take a Product Decision Record that was just accepted and apply its consequences across the rest of the product documentation, keeping everything coherent and consistent.

This workflow runs in **Executor mode** (Claude Code with file write access). It is not a planning workflow — by the time you run it, the decision has already been made and recorded in the PDR. Your role is to make the rest of the product docs reflect that decision.

---

## Invocation

The Maintainer invokes this workflow in one of two ways:

**Most recent PDR (default):**
```
Invoke @.ai/workflows/PDR-PROPAGATOR.md
```

You pick the PDR with the highest number in `docs/product/pdr/` (excluding `TEMPLATE.md`).

**Specific PDR:**
```
Invoke @.ai/workflows/PDR-PROPAGATOR.md for PDR 0003
```

You use PDR 0003 specifically (`docs/product/pdr/0003-*.md`).

If the invocation is ambiguous, ask the Maintainer which PDR before proceeding.

---

## Procedure

### 1. Read the PDR

Read the target PDR end to end. Make sure you understand:

- **Context** — what surfaced the decision (a discovery, a user signal, a market shift, a regulatory factor)
- **Decision** — what was chosen, in concrete product terms (new actor, new flow, new entity, removed feature, new commitment)
- **Consequences** — what changes in the product surface, in the data model, in the user-facing copy, in the workflows
- **Affected documents** — read both sub-sections carefully:
  - **Existing docs to update** — the propagation targets for in-place edits
  - **New docs to create** — documents that don't exist yet and need to be authored. If this sub-section lists one or more files (anything other than "None."), the propagation includes a **mini-discovery** for each new file (see step 4) before the in-place edits begin.
- **Alternatives rejected** — what was considered and why it lost

Pay special attention to **concrete product details**: actor names, entity names, workflow steps, feature scope, monetization mechanics. Those are the propagation targets.

### 2. Verify the PDR is propagatable

Confirm:

- The PDR's status is `Accepted` (not `Proposed` or `Superseded`)
- The Decision section contains concrete, actionable product details — not just a direction

If the PDR is still `Proposed`, stop and report to the Maintainer:

> "PDR NNNN is still in Proposed status. Propagation should happen after acceptance. Confirm you want to proceed anyway, or close the PDR first?"

If the Decision is too abstract to propagate (e.g., "We will focus more on retention"), stop and report:

> "PDR NNNN's Decision is not concrete enough to propagate. Propagation needs specific entities, workflows, or product surfaces. Want to revise the PDR first?"

### 3. Identify affected documents

Scan the documentation tree to find every document that may need updating based on the PDR.

Use these signals:

- **Direct mention** — any doc that names the same actor, entity, feature, or workflow the PDR is about
- **Conceptual overlap** — any doc covering the same product area (e.g., a PDR about adding a new actor "Mentor" affects `pd-audience.md`, all `ft-*.md` that involve mentor interaction, `PRODUCT.md` core actors)
- **Engineering implications** — sometimes a product decision has engineering consequences (a new entity needs SECURITY data tier classification, a new workflow needs an ADR for the technical approach)

Use the dependency table in `.ai/workflows/BOOTSTRAPPER.md` section 11 (Ripple effects) as a starting reference for common dependencies, but do not limit yourself to it — the actual PDR may touch areas the table doesn't anticipate.

Typical scan targets:

- `docs/PRODUCT.md` (Core actors, Glossary, How it works, Document map, Phase strategy)
- All files in `docs/product/` (pd-*.md, ft-*.md, except other PDRs)
- `docs/INDEX.md` (By topic table, document listing)
- `docs/engineering/SECURITY.md` (if the PDR introduces personal data handling, new authorization rules, or AI-specific concerns)
- `docs/engineering/ARCHITECTURE.md` (if the PDR implies a new bounded context, a new external integration, or a major flow change)
- `docs/engineering/TECH_DEBT.md` (a PDR may resolve a deferred product trade-off recorded as TD — mark it Resolved)
- `docs/legal/POLICY_CONSIDERATIONS.md` (if the PDR introduces a legal-adjacent concern, e.g., new data collection, new user-facing commitment, new monetization mechanic)
- `docs/ROADMAP.md` (if the PDR changes phase scope or priorities)
- Phase files in `docs/roadmap/` (if the PDR adds, removes, or reorders tasks)
- Other PDRs that reference the same product area (cross-references may need updates)

### 4. Run mini-discovery for new docs (if any)

If the PDR's "New docs to create" sub-section lists one or more files, run a mini-discovery with the Maintainer for each new file **before** any in-place edits.

**Why this step exists.** Creating a new `ft-*.md` from a PDR alone produces a thin file. The PDR captures the decision, not the full feature spec — Purpose is usually clear from the PDR's Decision section, but Actors, Vocabulary, Entities, Policies, and Edge cases need to be sourced from the Maintainer. The mini-discovery is a short structured conversation that fills those sections before the file is written.

**When this step is skipped.** If the PDR's "New docs to create" sub-section says "None." (or is absent), skip this step entirely and proceed to step 5.

**Procedure per new file:**

For each file listed in "New docs to create":

1. **Announce the mini-discovery.** Tell the Maintainer which file you're about to discover, in plain terms:

   > "PDR NNNN lists `docs/product/ft-mentor-onboarding.md` as a new file to create. Before I write it, I need to fill in Actors, Vocabulary, Entities, Policies, and Edge cases for this feature. The PDR gave me Purpose already. I'll ask focused questions; we should be done in 5-10 minutes per file."

2. **Use the PDR as the seed.** Extract from the PDR:
   - The new file's **Purpose** — typically a 1-2 sentence summary from the PDR's Decision section
   - Any **Actors** the PDR explicitly named
   - Any **Entities** the PDR explicitly named
   - Any **Policies** the PDR stated

   Restate these to the Maintainer in capture-aloud format: "Capturing from the PDR for `ft-mentor-onboarding.md`: Purpose: <one paragraph>. Actors: Mentor, Maintainer (review). Entities: Mentor, MentorApplication. Confirm or correct?"

3. **Probe the gaps.** Walk the structure from `docs/product/TEMPLATE.md` and ask focused questions for the sections the PDR did not cover:

   - **Actors** — "Who else interacts with this feature that the PDR did not mention?"
   - **Vocabulary** — "What terms specific to this feature should be in the glossary? Status names, lifecycle stages, anything a new contributor would ask about."
   - **Entities** — "What entities does this feature own beyond what the PDR named? How do they relate?"
   - **Policies** — "What rules does this feature enforce? If X happens, what does the system do?"
   - **Edge cases** — "What unusual or boundary situations does this feature handle?"
   - **Open questions** — "What about this feature is still unresolved? Mark these explicitly so they're not lost."

   Use the same forcing functions described in `.ai/workflows/bootstrap/BS-02-product-document.md` section 8 (challenge "it depends" answers, push for concrete policies, etc.). The mini-discovery here is the same conversational pattern as BS-02 deepening, just scoped to a single file.

4. **Calibrate depth to maturity.** If the Maintainer has a clear, articulated picture of the feature, capture it richly. If parts are still emergent, mark them as Open questions and move on. The mini-discovery should not invent content that the Maintainer doesn't have — thin honest specs are better than fabricated rich ones.

5. **Capture-aloud as you go.** Anchor substantive content explicitly:

   > "Capturing for `ft-mentor-onboarding.md`: Lifecycle states: applied → verified → active → paused. Transitions are Maintainer-driven. This goes into the Entities and Policies sections when I write the file."

6. **Do not write the file yet.** All captured content stays in the chat as anchors. The file gets written in step 7 (Apply the changes), alongside the in-place edits.

**When multiple new files are listed:**

Do each mini-discovery sequentially — one file fully discovered before moving to the next. This keeps the Maintainer focused. Between files, surface cross-references: "In the `ft-mentor-onboarding.md` mini-discovery, you mentioned `MentorReview` as an entity owned by `ft-practice.md`. Confirming that entity ownership before I get to that file's discovery."

**When the mini-discovery reveals the PDR is incomplete:**

Sometimes the conversation surfaces that the PDR itself didn't capture enough product detail to justify the new file's creation. When this happens, stop and report:

> "While discovering `ft-mentor-onboarding.md`, you described the verification flow as still TBD, and the Mentor entity's fields are unclear. The PDR didn't anticipate this depth. Two options: (A) capture what's known now, mark the rest as Open questions, proceed; (B) pause this propagation, revise the PDR to include the missing decisions, then re-run the propagation. Which?"

Let the Maintainer choose. Do not silently force creation of a thin file.

### 5. Build a propagation plan

For each affected document, decide what changes are needed. Categorize:

- **Replace** — old text needs to be replaced with new text reflecting the PDR
- **Add** — new content needs to be added (e.g., a new actor entry, a new section in a feature spec, a new term in the glossary)
- **Remove** — content that's now removed from scope per the PDR
- **Reference** — add a cross-reference to the PDR

Build the plan as a structured list — one entry per file, with the specific changes inside.

### 6. Present the plan to the Maintainer

Before applying any change, present the full plan. Format:

```markdown
# PDR-PROPAGATE plan for PDR NNNN — <title>

## PDR summary
<2-3 sentences capturing the decision and the main product consequence>

## Affected documents

### `docs/PRODUCT.md`
- **Add** new actor "Mentor" to Core actors with one-line description
- **Add** "Mentor" to Glossary
- **Update** How it works paragraph to mention mentor interaction in main flow

### `docs/product/pd-audience.md`
- **Add** Mentor as a new sub-section under audience analysis
- **Reference** PDR NNNN at the top of the file

### `docs/product/pd-workflows.md`
- **Add** new workflow narrative for student-mentor interaction
- **Update** existing student workflow to include mentor touchpoint

### `docs/product/ft-practice.md` (new file needed)
- **Create** new feature spec for mentor-reviewed practice tasks (using `docs/product/TEMPLATE.md`)

### `docs/engineering/SECURITY.md`
- **Add** "Mentor" to data tier examples (sensitive: mentor identity and credentials)

### `docs/legal/POLICY_CONSIDERATIONS.md`
- **Add** LP entry: mentor agreement and platform liability scope

## Apply this plan?
Confirm with `yes` to apply, or describe adjustments needed.
```

Wait for the Maintainer's confirmation. Do not apply changes until they say `yes` or equivalent.

If the Maintainer requests adjustments, revise the plan and present again. Iterate until they confirm.

### 7. Apply the changes

After confirmation, apply changes file by file:

- For each **new file** to create (from "New docs to create" in the PDR), author the file using `docs/product/TEMPLATE.md` as the structural floor, populated with the content captured during mini-discovery (step 4). Sections without captured content are written with the Open questions placeholder, not omitted.
- For each **existing file** to update, make the changes listed in the plan
- Add a cross-reference to the PDR where the decision originated (e.g., `> See PDR NNNN for the full rationale.`)
- Preserve the surrounding context — don't rewrite more than necessary
- If a BOOTSTRAP marker block is affected, replace the marker with the actual content (don't keep both)
- If a new entry is added to `POLICY_CONSIDERATIONS.md`, use the next available LP-NN number and fill all six fields (Situation, Implication, Preliminary position, Affects, Origin, Status)

### 8. Verify the propagation

After applying:

- Re-read each modified file end to end, in context
- Confirm the change reads naturally (not just textually correct)
- Confirm no stale references remain (e.g., the old actor list still appearing somewhere)
- Confirm cross-references to the PDR are present where appropriate
- Confirm the Document map in `PRODUCT.md` and the listings in `INDEX.md` reflect any new files created

### 9. Report

Final report to the Maintainer:

```markdown
# PDR-PROPAGATE complete for PDR NNNN

## Files modified
- `docs/PRODUCT.md` — added Mentor actor, glossary entry, updated How it works
- `docs/product/pd-audience.md` — added Mentor sub-section
- `docs/product/pd-workflows.md` — added student-mentor workflow
- `docs/engineering/SECURITY.md` — added Mentor to data tier examples
- `docs/legal/POLICY_CONSIDERATIONS.md` — added LP-04 (mentor agreement and liability)

## Files created
- `docs/product/ft-practice.md` — new feature spec for practice tasks (mentor-reviewed)

## Files reviewed but not modified
- `docs/engineering/ARCHITECTURE.md` — no architectural change required
- `docs/INDEX.md` — already covers the new file via BOOTSTRAP markers; will be updated when those are filled
- `CLAUDE.md` — no relevant content

## Cross-references added
- PDR NNNN referenced in 4 files

## Notes
- A new LP entry (LP-04) was added in POLICY_CONSIDERATIONS.md. This is a legal consideration that should be tracked separately and may warrant an LDR if its position evolves.
- The new `ft-practice.md` file uses `docs/product/TEMPLATE.md` as base. Sections are filled with content from the PDR; some sections (Edge cases, Policies) are marked as TBD pending further product discovery.

## Next steps
- Review the PR
- Merge when satisfied
```

---

## 10. Open a PR

After applying and verifying, follow the standard PR flow defined in `docs/engineering/GIT.md`:

### Branch

Create a feature branch from latest `main`:

```bash
git checkout main
git pull
git checkout -b docs/pdr-NNNN-propagate
```

Use `docs/pdr-NNNN-propagate` as the branch name (e.g., `docs/pdr-0003-propagate`).

### Commits

Stage and commit the changes following Conventional Commits. For propagations, a single commit is usually appropriate:

```bash
git add <list of modified and new files>
git commit -m "docs(pdr): propagate PDR NNNN — <short PDR title>"
```

If the propagation created new files (e.g., a new `ft-*.md`), the same commit covers them — no need to split.

Do not use `git add .`. Stage explicitly to avoid pulling in unrelated changes.

### PR

Push the branch and open the PR:

```bash
git push -u origin docs/pdr-NNNN-propagate
gh pr create
```

PR title:

```
docs(pdr-NNNN): propagate PDR NNNN — <short PDR title>
```

PR description (filling `.github/PULL_REQUEST_TEMPLATE.md`):

- **What** — "Propagates the consequences of PDR NNNN across affected product documentation."
- **Why** — "PDR NNNN was accepted on YYYY-MM-DD. Its decision affects several existing product docs and introduces new product surfaces that need to be documented."
- **Verification** — List the files modified, created, and reviewed (the same lists from the report in step 9).
- **Related** — Link to the PDR file and any originating product discussion or TSK.

### Updating the report

Append the PR URL to the final report:

```markdown
## Pull Request
[#YYY — docs(pdr): propagate PDR NNNN](https://github.com/<owner>/<repo>/pull/YYY)
```

The Maintainer reviews and merges the PR. Do not merge yourself.

---

## Constraints

- Never edit the PDR itself — PDRs are immutable once Accepted.
- Never delete content from another doc without confirming with the Maintainer first. Replacement is fine; deletion is permanent and surfaces in step 6 (plan) before applied.
- If a propagation would create a contradiction with another doc that wasn't anticipated, stop and surface it:
  > "Propagating PDR NNNN to pd-audience.md would contradict the actor list in PRODUCT.md. Both can't be right. Should the PDR be revised, or does PRODUCT.md need an update too?"
- Do not propagate to other PDRs except to add cross-references — historical PDRs are not modified, but a `Superseded by PDR NNNN` note may be added to a PDR that this one supersedes.
- If the PDR has engineering implications (new entity, new external integration, new security concern), surface this clearly to the Maintainer and suggest whether an ADR is also needed. Do not silently spawn an ADR — that is a separate decision.

---

## When propagation reveals deeper issues

Sometimes propagation surfaces that the PDR itself is incomplete or inconsistent with existing product context. When this happens:

- Report the issue clearly to the Maintainer
- Suggest revising the PDR (or writing a follow-up PDR) rather than forcing inconsistent propagation
- Stop the propagation until the source PDR is sound

A bad propagation creates worse problems than a delayed one. When in doubt, surface and wait.

---

## Edge cases

### The PDR supersedes a previous PDR

If the PDR's status mentions `Supersedes PDR MMMM`, also update the superseded PDR:

- Change its status to `Superseded by PDR NNNN`
- Add a brief note explaining when and why it was superseded
- Do NOT change any other content of the superseded PDR

### The PDR introduces an actor or entity not yet documented

If the PDR introduces a new actor, entity, or feature, the PDR's "New docs to create" sub-section should list any new files needed. Step 4 (mini-discovery) then authors those files before step 7 writes them. The standard flow handles this.

If the PDR's "New docs to create" sub-section is empty ("None.") but you discover during propagation that a new file is actually needed (e.g., a new actor with its own lifecycle that warrants its own `ft-*.md`), stop and report:

> "PDR NNNN introduces <X>, which I'd normally document in a new `ft-<x>.md` file. The PDR's 'New docs to create' sub-section says 'None.' Two options: (A) update the PDR's 'New docs to create' to include the file, then I run the mini-discovery and proceed; (B) treat <X> as scope inside an existing file (which one?) and skip the new file. Which?"

Do not silently create new files outside what the PDR specifies — the PDR is the source of truth for what gets created.

Smaller additions that fit inside existing docs (a new actor entry in `PRODUCT.md` Core actors, a new glossary term, a new section in an existing `ft-*.md`) are routine in-place edits and do NOT require updating the PDR. The distinction is whether a new **file** would be created.

### The PDR has engineering implications

If propagating the PDR requires changes to engineering docs (SECURITY data tiers, ARCHITECTURE bounded contexts, TECH_STACK new dependency), surface this in the plan **and** flag whether an accompanying ADR might be needed:

> "PDR NNNN's introduction of mentor identity verification likely requires an ADR for the verification approach. Do you want me to propagate the product implications now, and you'll create the ADR separately? Or pause until the ADR is in place?"

Do not silently create ADRs. ADRs are engineering decisions and follow their own flow.

### The PDR has legal implications

If propagating the PDR surfaces a legal consideration not yet captured in `POLICY_CONSIDERATIONS.md`, add a new LP-NN entry as part of the propagation. Surface it in the plan and in the report. Note that an LDR may follow if the legal position later evolves — but that's a separate workflow (`LDR-PROPAGATE.md`), not part of this one.

### Multiple BOOTSTRAP markers replaced

If propagation fills BOOTSTRAP markers that were still empty (e.g., the project is mid-bootstrap and a PDR was created early), replace the marker entirely with the new content. Note in the report which markers were replaced.

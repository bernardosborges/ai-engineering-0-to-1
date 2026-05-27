# PDR NNNN — Short, imperative title

<!--
Filename: NNNN-kebab-case-title.md (e.g., 0001-add-mentor-actor.md, 0002-shift-to-usage-based-pricing.md)
Title: mirrors the filename. State the product change clearly and concretely.
  Good: "Add Mentor as a third actor", "Shift pricing from subscription to usage-based",
        "Drop multi-language support for v1"
  Bad: "Product decision", "User-related changes", "Pricing stuff"
-->

**Status:** Proposed | Accepted | Deprecated | Superseded by PDR NNNN
**Date:** YYYY-MM-DD
**Deciders:** Maintainer | <names if a specific person/group owns this>

## Context

<!--
What surfaced this decision? Why now?
Describe the situation, not the chosen direction. Include:
- What was discovered (user signal, market shift, internal constraint, regulatory change)
- What was previously assumed about this area (if anything is being revised)
- Why the existing product state no longer fits

Keep it factual and short — 1-3 paragraphs, or a numbered list of triggering events.
The goal: a new contributor reading this in 12 months understands WHY this product
decision had to be made at this moment.

Avoid: marketing language, abstract user-journey theory, irrelevant history.
-->

## Decision

<!--
State the product decision clearly and imperatively. Start with "We add...",
"We remove...", "We change..." — not "We should" or "We could".

Include concrete product details:
- Specific entities, actors, or features introduced, modified, or removed
- The user-facing change (what users will see or experience differently)
- The scope boundary (what is NOT changing as part of this decision)
- The phase or timing (is this v1, v2, deferred?)

Be specific enough that someone reading the affected product docs after this PDR
understands exactly what the product surface should look like.
-->

## Consequences

### Positive

<!--
What becomes possible, simpler, or more valuable in the product?
Bad: "Better user experience"
Good: "Students can choose a mentor by specialty, increasing match quality and reducing
       early-stage churn"
-->

### Negative / accepted trade-offs

<!--
What becomes harder, more complex, or removed from scope? Every real product
decision has costs.
If you can't think of any, you haven't thought hard enough — go back.
Label each trade-off as "Accepted" with a brief reason. This is what prevents
someone from undoing the decision later without understanding why it was accepted.

Bad: "Might confuse users"
Good: "Adds a third actor (Mentor) to the product, increasing surface area for
       support and onboarding. Accepted — the alternative of automated matching
       loses the quality signal that human mentors bring, which is the
       differentiator."
-->

### Affected documents

<!--
The documents that will need updating, and the documents that will need to be created,
to reflect this decision. This becomes the input for the PDR-PROPAGATOR workflow when this
PDR is Accepted.

The split exists because the propagation paths differ:
- Updating an existing doc means editing sections in place (the PDR-PROPAGATOR does this directly).
- Creating a new doc means the PDR-PROPAGATOR runs a mini-discovery to author the new file's
  content (Purpose, Actors, Vocabulary, Entities, Policies, Edge cases) before propagating the
  cross-references in other docs that point to it.

List only what is genuinely affected. If no new docs are needed, leave "New docs to create" with
"None." rather than removing the heading.
-->

**Existing docs to update:**

<!--
Format: file path → one-line description of the expected change.

Example:
- docs/PRODUCT.md → add Mentor to Core actors and Glossary
- docs/product/pd-audience.md → add Mentor sub-section
- docs/product/pd-workflows.md → describe student-mentor interaction
- docs/engineering/SECURITY.md → add Mentor to data tier examples
-->

**New docs to create:**

<!--
Format: file path → one-line description of the new doc's purpose.

New ft-*.md files belong here when the decision introduces a feature, transversal model, or
principal actor with lifecycle that warrants its own spec. New LP-NN entries in
`docs/legal/POLICY_CONSIDERATIONS.md` are NOT in this list — those are additions to an existing
doc and belong above.

Example:
- docs/product/ft-practice.md → mentor-reviewed practice tasks feature
- docs/product/ft-mentor-onboarding.md → mentor registration, verification, lifecycle

If nothing is created, write:
- None.
-->

## Alternatives rejected

<!--
One bullet per alternative, with the reason it was rejected. Keep it short and factual.
This section is the fastest way for someone to answer "why didn't we just...?"

Bad: "Option X — didn't like it"
Good: "Automated AI matching only — rejected because evaluation showed that a
       human mentor signal is what causes students to commit to the course; removing
       that removes the differentiator."
-->

## Open questions

<!--
What does this PDR not yet resolve? List as numbered items.
A PDR can be Accepted with open questions, as long as the open items don't block
the core decision. Open questions become inputs for future PDRs.

Example:
1. Mentor pricing — fixed fee per review? Revenue share? Deferred to PDR-NNNN.
2. Mentor onboarding flow — to be defined in BS-02 or a dedicated planning session.

Omit this section if there are no open questions.
-->

## References

<!--
Links that support the decision: user research, market analysis, competitor
products, conversations or interviews that were actually influential.
No link-dumping — only what a future reader would need to re-evaluate this decision.

Optional. Omit this section entirely if there are no relevant references.
-->

---

## Notes on using this template

When writing a new PDR:

1. **Copy this file**, rename to `NNNN-kebab-case-title.md` where NNNN is the next number in sequence across all PDRs.
2. **Fill every section** except References and Open questions, which are optional.
3. **Remove all `<!-- comment -->` blocks** before committing — they are guidance for the author, not part of the published PDR.
4. **Update Status to Accepted** when the decision is implemented (or when the propagation PR merges).
5. **Never edit an Accepted PDR retroactively.** If the decision changes, write a new PDR with status "Supersedes PDR NNNN" and update the original's status to "Superseded by PDR MMMM".
6. **Reference the PDR** from the most affected product doc (typically a relevant `pd-*.md` or `ft-*.md`) so it is discoverable through the natural doc flow.
7. **Run `@.ai/workflows/PDR-PROPAGATOR.md`** after the PDR is Accepted to propagate its consequences across other docs in a controlled, plan-first way.

PDRs document **significant** product decisions — ones that future contributors will need context to understand or might be tempted to undo without realizing the original rationale. Routine product choices (which copy variation to use, which icon to ship) do not warrant a PDR.

The bar is "would the next person joining the project benefit from knowing why we chose this?" If no, don't write a PDR. If yes, write one.

### When to write a PDR vs editing a pd-*/ft-* doc directly

- **Edit the pd-*/ft-* directly** when the change is **additive** to the doc (new term, new example, new section) and doesn't reverse or significantly modify a previous decision.
- **Write a PDR** when the change is **a decision** that reverses, restructures, or significantly alters a previous direction — even if the same change could technically be made by editing the doc directly. The PDR preserves the WHY and the rejected alternatives, which a direct edit would lose.

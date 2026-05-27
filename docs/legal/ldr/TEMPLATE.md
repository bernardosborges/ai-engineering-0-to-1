# LDR NNNN — Short, imperative title

<!--
Filename: NNNN-kebab-case-title.md (e.g., 0001-data-retention-position.md, 0002-loyalty-points-classification.md)
Title: mirrors the filename. State the legal position area concretely.
  Good: "Data retention position for user-generated content",
        "Loyalty points classification under consumer law",
        "Cross-border data transfer for EU users"
  Bad: "Legal decision", "Privacy stuff", "Compliance update"
-->

**Status:** Proposed | Pending counsel review | Accepted | Deprecated | Superseded by LDR NNNN
**Date:** YYYY-MM-DD
**Deciders:** Maintainer | <names if a specific person/group owns this>
**Counsel involvement:** Confirmed by counsel <name/firm, date> | Internal position, not yet reviewed | Partially reviewed (see Decision section)

## Context

<!--
What surfaced this decision? Why now?
Describe the situation, not the chosen position. Include:
- What was discovered or what changed (a regulation update, a counsel response,
  a new feature crossing a legal threshold, entry into a new jurisdiction)
- Which LP entry(ies) in POLICY_CONSIDERATIONS.md this LDR is responding to,
  if any
- What the existing position was (if this is revising a previous position)
- What constraints are at play (jurisdictions, regulatory authorities, deadlines)

Keep it factual and short — 1-3 paragraphs.
The goal: a future contributor reading this in 12 months understands WHY this
position had to be formed at this moment.

Avoid: speculation about what counsel might say, marketing language, references
to anything that isn't grounded in a source you can name.
-->

## Decision

<!--
State the internal position clearly. Start with "We treat...", "We adopt the
position that...", "We classify..." — not "We should" or "We could".

For EACH part of the decision, indicate the source of the position:

  **[Confirmed by counsel]** — validated by qualified legal counsel (name the
    counsel and date in the Counsel involvement field above; reference verbatim
    guidance here if available)
  **[Preliminary]** — internal position formed without counsel review, subject
    to revision when counsel is engaged
  **[Pending further review]** — area touched by this LDR where no firm position
    is taken yet; will require a future LDR

Be specific. Internal positions that propagate to other docs need to be concrete
enough that someone reading the affected docs understands the boundary.

Example:
- [Confirmed by counsel] We classify loyalty points as a customer engagement
  mechanism, NOT a financial instrument, conditional on: (a) points are non-
  transferable between users, (b) points have no cash-out mechanism outside
  in-product redemption, (c) the redemption rate is stable and disclosed.
- [Preliminary] For EU users, we apply GDPR principles to all data processing
  regardless of where the user accesses from, pending counsel confirmation on
  the territorial scope question.
- [Pending further review] Treatment of expired points (lapsed accounts vs
  active accounts) — to be resolved in a future LDR.
-->

## Consequences

### Affected documents

<!--
List the documents that will need updating to reflect this position.
This becomes input for the LDR-PROPAGATE workflow when this LDR is Accepted.

For each document, indicate which parts of the Decision apply, so the propagation
plan can carry the right [Confirmed]/[Preliminary] markers per change.

Format: file path → description of expected change → [Confirmed | Preliminary | Pending].

Example:
- docs/legal/POLICY_CONSIDERATIONS.md LP-09 → update Status to "addressed in
  policy" and replace Preliminary position → [Confirmed by counsel]
- docs/legal/POLICY_CONSIDERATIONS.md → add new LP entry for expired points
  question → [Pending further review]
- docs/engineering/SECURITY.md → update data retention rule to mention the
  user-generated content carve-out → [Confirmed by counsel]
- docs/product/pd-business-model.md → reflect loyalty points classification in
  monetization sub-section → [Preliminary]
-->

### Risk if the position is later revised

<!--
What is the cost if counsel later disagrees with the [Preliminary] parts of
this LDR, or if a regulation change invalidates the [Confirmed] parts?

Be honest. The point isn't to predict the future — it's to estimate now what
revisiting would cost, so the team prioritizes counsel review appropriately.

Example:
- If loyalty points classification is later disputed: we may need to add explicit
  refund mechanisms, update terms of use, and potentially register the program
  with the consumer protection authority. Estimated effort: medium. Estimated
  exposure: low to medium depending on outstanding point liability.
- If GDPR territorial scope position is later narrowed by counsel: minor — we
  would be applying stricter rules than required, with no compliance gap.
- If GDPR position is later broadened by counsel: minor for the current product
  state; significant if we have already published a privacy policy that contradicts.
-->

### What this LDR does NOT address

<!--
Explicit scope boundary. List areas adjacent to this LDR that are NOT covered
by it, to prevent over-interpretation by future readers.

Example:
- This LDR does NOT address the treatment of points earned through referrals
  (separate question, see open questions in pd-business-model.md).
- This LDR does NOT address tax treatment of point redemptions (out of scope;
  separate analysis with tax advisor required).
- This LDR does NOT extend to other forms of in-product credits (e.g., promotional
  credits, refund credits); those require separate analysis.
-->

## Alternatives rejected

<!--
One bullet per alternative position considered, with the reason it was rejected.
Keep it short and factual.

This section preserves the WHY of the position so a future reader can challenge
the position with the same depth of analysis.

Bad: "Other option — didn't fit"
Good: "Treating loyalty points as a closed-loop gift card — rejected because the
       redemption mechanics are tied to ongoing service consumption rather than
       a fixed value, and the gift card classification would impose accounting
       and refund obligations that don't match the program's economics."
-->

## Open questions

<!--
What does this LDR not yet resolve? List as numbered items.
An LDR can be Accepted with open questions, as long as the open items don't
block the core position. Open questions become inputs for future LDRs.

Example:
1. Expired points treatment — accumulating, lapse, or refund? Will require
   counsel input on consumer protection rules.
2. Inter-jurisdictional consistency — does the EU-applicable position need
   to be the worldwide default, or can we vary by jurisdiction without
   user-facing inconsistency?

Omit this section if there are no open questions.
-->

## References

<!--
Links and citations that support the position: regulation text, counsel
correspondence, regulatory guidance documents, comparable legal opinions,
industry standards.

Be specific. For counsel input: name the firm, date, and document if any.
For regulation: cite article and version.

Avoid: blog posts that don't cite their sources, generic explanations that
restate the regulation without analysis.

Optional. Omit this section if there are no relevant references.
-->

---

## Notes on using this template

When writing a new LDR:

1. **Copy this file**, rename to `NNNN-kebab-case-title.md` where NNNN is the next number in sequence across all LDRs.
2. **Fill every section** except References and Open questions, which are optional.
3. **Remove all `<!-- comment -->` blocks** before committing — they are guidance for the author, not part of the published LDR.
4. **Mark certainty per part of the Decision.** Every distinct position in the Decision section must carry [Confirmed by counsel], [Preliminary], or [Pending further review]. This is the most important discipline in an LDR — mixing them silently is what creates downstream problems.
5. **Update Status to Accepted** only when the internal position is firm enough to propagate. If still under counsel review, leave as `Pending counsel review`.
6. **Never edit an Accepted LDR retroactively.** If the position changes, write a new LDR with status `Supersedes LDR NNNN` and update the original's status to `Superseded by LDR MMMM`. The original stays as it was — it's the record of what was decided at that moment, not a living document.
7. **Reference the LDR** from the LP entries it affects in `POLICY_CONSIDERATIONS.md` so the trail is bidirectional.
8. **Run `@.ai/workflows/LDR-PROPAGATOR.md`** after the LDR is Accepted to propagate its consequences across other docs in a controlled, plan-first way that preserves the per-change certainty markers.

### When to write an LDR vs editing POLICY_CONSIDERATIONS.md directly

- **Edit POLICY_CONSIDERATIONS.md directly** when you are **adding a new LP entry** for a newly surfaced consideration that does not yet have a position. New LP entries with status `open` are routine and don't need an LDR.
- **Write an LDR** when you are **forming or changing a position** on an existing LP entry, on a new area that crosses jurisdictions, or in response to counsel input. The LDR captures the WHY of the position and the certainty level, which a direct edit would lose.

### What this template is NOT

This template captures **internal positions** that inform legal artifacts. It is **not** legal advice and **not** a substitute for legal counsel. Public-facing legal artifacts (terms of use, privacy policy, user agreements, vendor contracts) must be drafted by qualified legal counsel before they take effect.

The LDR is input to that process — comprehensive, well-organized input that makes counsel's work effective. It is not output from that process.

# Legal and policy considerations register

Tracks situations encountered during product development that have legal, regulatory, or terms-of-use implications. Each entry records the situation, why it matters, a preliminary position, and where it surfaced.

**This file is NOT the terms of use, privacy policy, or any legal document.** It is a running register of considerations that those documents will need to address when they are drafted. The actual legal documents must be written with qualified legal counsel — this register is the input material that makes that drafting comprehensive, not a substitute for it.

The philosophy mirrors `@docs/engineering/TECH_DEBT.md`: capture the consideration when it surfaces, with enough context that it survives team rotation, so that nothing discovered during development is lost by the time policy is formalized.

---

## Format

Each entry follows the same structure:

- **Situation** — what was discovered, in plain terms
- **Implication** — why this is a legal/policy matter, not just a technical one
- **Preliminary position** — what the team currently thinks the policy should say, explicitly subject to legal review
- **Affects** — which legal artifact(s) this likely touches (terms of use, privacy policy, supplier agreement, buyer agreement, etc.)
- **Origin** — where it surfaced (task ID, planning conversation, ADR)
- **Status** — `open` / `addressed in policy` / `discarded`

Entries are grouped by **theme**, and numbered (`LP-NNN`) sequentially within the document. When a new entry is added to a theme, entries below it are renumbered to keep numbering aligned with physical order — references are updated in the same change.

---

## Themes

Common themes that emerge in real projects:

- **Liability and scope of service** — what the platform is and isn't responsible for
- **Data ownership and use** — who owns what data, with whose consent
- **Privacy and data protection** — obligations under applicable regulation
- **Platform mechanics and user-facing commitments** — credits, currencies, promises made in product copy
- **Third-party integrations and transfers** — what data goes where, what consents are needed
- **Content and intellectual property** — images, descriptions, user-generated content

Add themes as they emerge. Don't pre-create empty theme sections.

<!-- BOOTSTRAP:LP_ENTRIES:START -->
> 📝 Populated as the project surfaces legal or policy considerations during development.
>
> Entries are added under thematic headings. When a new theme emerges, add it as a level-2 heading; entries belonging to it become level-3 headings with the `LP-NNN — Title` pattern.
>
> Typical first entries in a new project come from:
> - Initial product planning conversations (what is the platform responsible for?)
> - First handling of personal data (which regulation applies, what is the lawful basis?)
> - First third-party integration sending user data outward
> - First user-facing promise that has economic or commitment value (referrals, credits, SLAs)
<!-- BOOTSTRAP:LP_ENTRIES:END -->

---

## How to add an entry

When development surfaces a situation with legal or policy implications:

1. Add a new entry under the appropriate theme (or create a new theme).
2. Give it the number that matches its physical position in the document. If it is inserted above existing entries, renumber the entries below it and update any cross-references in the same change.
3. Fill all six fields (Situation, Implication, Preliminary position, Affects, Origin, Status).
4. Keep the preliminary position honest about what is NOT yet decided — half-formed positions are fine and useful; overconfident ones are dangerous.
5. If the entry came from a specific task or planning conversation, reference it in Origin so the trail survives.

When a legal document is eventually drafted:

1. Walk every `open` entry and confirm it is addressed (or consciously excluded) in the drafted document.
2. Change the entry's status to `addressed in policy` with a reference to the section/document that covers it, or `discarded` with a reason.
3. Do not delete entries — the considered-then-addressed trail is itself documentation, and shows legal due diligence that the team thought through each item.

---

## Entry template

Copy this block when adding a new entry. Replace placeholders.

```
### LP-NNN — Short imperative title

**Situation.** What was discovered in plain terms. Include enough context that
someone reading it in 12 months understands what surfaced this and why it matters.

**Implication.** Why this is a legal/policy matter rather than a technical or
product matter. What exposure or obligation does it create?

**Preliminary position.** What the team currently thinks the policy should say,
explicitly subject to legal review. Be honest about what is not yet decided.

**Affects.** Terms of use / privacy policy / supplier agreement / buyer agreement
/ ADR — list which legal artifacts this likely touches.

**Origin.** TSK-NNN; planning conversation on YYYY-MM-DD; or ADR NNNN.

**Status.** open
```

---

## Disclaimer

This register is prepared by the development team to capture considerations as they arise. **It is not legal advice and does not constitute a legal document.** The terms of use, privacy policy, and any user-facing agreements must be drafted and reviewed by qualified legal counsel before they take effect. This file is input to that process.

<!--
PYTHON_REFERENCE_EXAMPLE — Real LP entries

Three examples from different hypothetical projects in different industries.
Use as reference for the level of specificity, tone, and structure expected
in well-written LP entries. The point is to show what a thoughtful entry
looks like — not to suggest these specific scenarios apply to your project.

## Liability and scope of service

### LP-001 — Fitness data does not constitute medical advice

**Situation.** The product collects heart rate, sleep, activity, and self-reported wellness data, and surfaces trends and suggestions to the user (e.g., "your resting heart rate has increased over the past 30 days"). Some suggestions touch territory that overlaps with medical guidance — recommending the user rest, adjust intensity, or consult a professional. The line between "informational wellness feedback" and "medical advice" is not obvious from the inside of the product.

**Implication.** Most jurisdictions reserve medical advice for licensed professionals. A product that displays health-related guidance without that licensing exposes the company to regulatory risk (depending on jurisdiction: FDA in the US, ANVISA in Brazil, MHRA in the UK, etc.) and to civil liability if a user follows a recommendation with harmful consequences. The platform's role is to surface data the user already produced, not to interpret it medically.

**Preliminary position.** The terms of use should establish that:
- The product surfaces user-generated wellness data and statistical observations on that data;
- It does not provide medical advice, diagnosis, or treatment recommendations;
- Users with health concerns should consult a qualified medical professional;
- The product is not a medical device.

The harder question is the wording of in-product suggestions themselves. "Your resting heart rate has increased" is observational. "You should rest more" steps closer to advice. Product copy needs review with legal counsel to identify which surfaces cross the line, especially in jurisdictions with strict regulation on health claims.

**Affects.** Terms of use; product copy across all suggestion surfaces; potentially a regulatory classification review per jurisdiction.

**Origin.** Surfaced during user research, when a beta tester reported acting on a suggestion that turned out to mask an actual cardiac issue.

**Status.** `open`

---

## Data ownership and use

### LP-004 — Work product ownership between client, freelancer, and platform

**Situation.** The platform connects clients to independent professionals (designers, developers, writers). Work product is created by the professional, delivered to the client through the platform, and often passes through the platform's storage (drafts uploaded to platform, messages, file revisions). Three parties have potential claims on the work product: the client who commissioned it, the freelancer who created it, and the platform whose infrastructure stored and routed it.

**Implication.** Without explicit terms, intellectual property rights default to ambiguous patterns: copyright generally vests in the creator (freelancer), but the work-for-hire doctrine may transfer rights to the client depending on jurisdiction and contract language, and the platform's role in storing files raises questions about whether platform terms can claim any rights to displayed work product (e.g., showing portfolio samples in marketing).

**Preliminary position.** The terms should establish a default IP framework that:
- Vests work product copyright in the client upon full payment, with the freelancer retaining the right to use the work in their portfolio;
- Grants the platform a limited license to store, transmit, and display work product as needed to provide the service (not for marketing without explicit additional consent);
- Allows either party to override the default via custom contract terms attached to a specific engagement;
- Distinguishes between deliverable work (transfers per above) and process artifacts (drafts, revisions, communications — remain the property of whoever created them).

Several aspects remain open: pre-existing IP brought by the freelancer (frameworks, libraries, templates), AI-assisted work product (is it copyrightable at all?), and the platform's right to use anonymized work product for ML training or marketing. These require legal counsel.

**Affects.** Terms of use, freelancer agreement, client agreement, AI policy (if the platform offers AI tools used in work creation).

**Origin.** Surfaced during planning for the file-revisions feature, when the question "who owns intermediate drafts that were never delivered?" had no clear answer in the existing terms.

**Status.** `open`

---

## Platform mechanics and user-facing commitments

### LP-009 — Loyalty points program and the appearance of a financial instrument

**Situation.** The product rewards users with loyalty points for repeat orders, referrals, and review submissions. Points can be redeemed for discounts on future orders. The product copy describes the program in friendly terms ("earn points, save money"), but the mechanics — points have a stable cash-equivalent value, can be accumulated over time, can in some markets be combined with payment — raise the question of whether the program operates as a customer-loyalty mechanism or as a regulated financial instrument.

**Implication.** Jurisdictions vary significantly. In some, loyalty points with cash-equivalent redemption are treated as consumer engagement tools with light regulatory touch. In others (particularly under stricter consumer protection regimes), they can trigger requirements around: refundability if the program is discontinued, treatment of unused points as company liability on financial statements, taxation of point redemption as income, anti-money-laundering rules if points can be transferred between users, and consumer rights to a clear value statement (1 point = ? cents). The "loyalty program" framing protects only if the mechanics actually match that framing.

**Preliminary position.** The terms of use should:
- Define the points program explicitly: how points are earned, the redemption rate, expiration policy, transferability (default: non-transferable);
- State that points have no cash value outside the redemption mechanism, cannot be exchanged for money, and are not a financial instrument;
- Reserve the platform's right to modify or discontinue the program with reasonable notice;
- Address what happens to unused points if the user closes their account or the program is discontinued (refund? lapse? credit?).

The "no cash value" claim only holds if the product actually behaves that way — if a feature later allows transferring points to other users or extracting their value in any way that resembles a transaction, the legal classification changes.

**Affects.** Terms of use (likely dedicated section); product roadmap (any feature that adds transfer or extraction mechanics needs to revisit this); accounting/finance (unused points as liability).

**Origin.** Surfaced during planning for a "gift points to a friend" feature, when the proposed mechanic moved closer to a financial instrument than the original program had intended.

**Status.** `open`
-->

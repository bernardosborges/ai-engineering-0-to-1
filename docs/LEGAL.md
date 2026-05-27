# Legal overview

> **Status:** v1
> **Owner:** product + engineering

This document is the entry point to understanding **how legal and policy concerns are handled in this project**. It is the third area of project documentation, alongside [PRODUCT.md](PRODUCT.md) and [ENGINEERING.md](ENGINEERING.md).

For the complete catalog of legal documents, see [INDEX.md → Legal](INDEX.md#legal).

---

## What this area covers

Software projects routinely surface situations with legal, regulatory, or terms-of-use implications. Some are obvious (privacy law applies when collecting personal data). Many are not (a referral bonus might qualify as a financial instrument in some jurisdictions; an AI-generated image may have ambiguous copyright; a status display that resembles medical advice may trigger health regulation).

The legal area exists to **capture these concerns as they emerge** and to **record the internal positions** the team forms about them, so that when actual legal documents are drafted (terms of use, privacy policy, user agreements), the drafting is comprehensive and grounded in the project's real situations — not in generic templates.

This area is **explicitly not legal advice**. The team is engineering and product; legal advice comes from qualified counsel. The role of this area is to be the high-quality input that makes counsel's work effective.

---

## What lives in `docs/legal/`

Two kinds of artifacts:

- **[legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md)** — the running register of legal and policy considerations as they emerge, grouped by theme (liability, data ownership, privacy, platform mechanics, third-party transfers, intellectual property). Each entry is numbered `LP-NN` (local to the file) and carries a preliminary position with explicit status.

- **`legal/ldr/`** — Legal Decision Records. Durable records of positions formed by the team in response to LP entries, counsel input, regulatory changes, or new feature scope. Each LDR is in its own file, numbered globally as `NNNN-kebab-case-title.md`. The template for new LDRs is at [legal/ldr/TEMPLATE.md](legal/ldr/TEMPLATE.md).

Additional documents may be added as the project's specific obligations crystallize. Common candidates as the project matures:

- A regulation-specific compliance register (e.g., GDPR or LGPD data-processing record) — required by law in some jurisdictions before processing personal data in production
- A vendor/sub-processor list — required when sharing personal data with third parties
- A retention schedule — required when the regulation mandates a documented retention policy
- A draft of the public-facing terms of use or privacy policy, prepared for review by legal counsel

These are not created speculatively. They are created when the project encounters the obligation that requires them.

---

## How POLICY_CONSIDERATIONS and LDRs relate

The two artifacts work together but serve different purposes.

**POLICY_CONSIDERATIONS.md is the running state.** It holds the current list of LP entries — what considerations the team is tracking, where each one stands, what the preliminary position is. It evolves continuously: new entries are added as concerns surface; existing entries are updated as positions firm up; closed entries stay in the file with their final status (the trail itself is documentation).

**LDRs are pointwise records.** They are written when the team **forms a position** or **changes a position** on something significant — typically in response to counsel input, a regulatory change, a new jurisdiction the project enters, or a feature that crosses a legal threshold. Each LDR is immutable once Accepted: it records what was decided at that moment, who was involved, and what certainty level applies.

The relationship:

- Adding a new LP entry with status `open` → routine, edit POLICY_CONSIDERATIONS.md directly
- Forming or changing a position on an existing LP → write an LDR, then propagate
- Counsel reviews and confirms a previously preliminary position → write an LDR with `[Confirmed by counsel]`, then propagate
- New jurisdiction or regulation changes the picture → write an LDR documenting the new position, then propagate

The LDR captures the WHY; POLICY_CONSIDERATIONS.md absorbs the WHAT after propagation.

---

## Legal Decision Records (LDRs)

LDRs are the durable record of significant legal-position decisions made on this project. Each LDR captures:

- **Context** — what surfaced the decision (counsel response, regulation update, new jurisdiction, feature crossing a legal threshold)
- **Counsel involvement** — whether qualified legal counsel was consulted and what their guidance was
- **Decision** — the internal position(s) adopted, with per-part certainty marker: `[Confirmed by counsel]`, `[Preliminary]`, or `[Pending further review]`
- **Consequences** — affected documents, risk if the position is later revised, explicit scope boundary
- **Alternatives rejected** — what positions were considered and why they lost

LDRs live in `docs/legal/ldr/`. The template is at [legal/ldr/TEMPLATE.md](legal/ldr/TEMPLATE.md).

### Certainty levels: the core discipline of LDRs

Unlike ADRs and PDRs, LDRs are about positions on legal questions — and the team is not qualified to settle legal questions. Every part of an LDR's Decision must carry one of three certainty markers:

- **`[Confirmed by counsel]`** — validated by qualified legal counsel. Has been reviewed by a professional with responsibility and licensure to provide legal opinion. The counsel and date are named in the LDR's metadata.
- **`[Preliminary]`** — internal position formed by the team based on research, regulation reading, comparable products, and judgment. Stronger than a guess, but not validated by counsel. Treated as working hypothesis until counsel review.
- **`[Pending further review]`** — area touched by the LDR where no firm position is taken yet. Does NOT propagate. Becomes input for a future LDR.

When the LDR-PROPAGATE workflow applies changes from an LDR, every change carries forward its certainty marker. `[Preliminary]` changes are propagated with an inline note in the affected document, so any reader sees the caveat in context — not buried in the LDR.

This discipline is what prevents the team from over-relying on positions that have not been validated. Mixing certainty levels silently is the most expensive mistake the legal area can make.

### When to create an LDR

Create an LDR when:

- Counsel responds to a question and you want to record what they said and what position the team is now adopting
- A regulatory change affects the project's posture (a new law in scope, a new authority's guidance)
- The project enters a new jurisdiction and existing positions may not extend automatically
- A new feature crosses a legal threshold (e.g., introduces a new financial mechanic, a new data collection, a new user commitment)
- An existing LP entry's preliminary position is being refined or reversed based on new information

Do NOT create an LDR for:

- Adding a brand-new LP entry with status `open` — just edit POLICY_CONSIDERATIONS.md directly
- Routine wording adjustments to existing LP entries that don't change the position
- Counsel conversations that didn't yield a concrete position (those become open questions in a future LDR, not the LDR itself)

### How to create an LDR

1. Copy [legal/ldr/TEMPLATE.md](legal/ldr/TEMPLATE.md) to `legal/ldr/NNNN-kebab-case-title.md` where NNNN is the next number in sequence (zero-padded to four digits)
2. Fill the metadata block, especially the `Counsel involvement` field — `Confirmed by counsel <name/firm, date>`, `Internal position, not yet reviewed`, or `Partially reviewed`
3. In the Decision section, mark every part with `[Confirmed by counsel]`, `[Preliminary]`, or `[Pending further review]`. Do not mix without markers
4. Fill Consequences including Affected documents (with per-file certainty markers), Risk if the position is later revised, and What this LDR does NOT address
5. Fill Alternatives rejected to preserve the WHY
6. Remove the `<!-- comment -->` blocks before committing
7. Set Status to `Accepted` when the position is firm enough to propagate, or `Pending counsel review` if you want to record the position but wait before propagating

### How to propagate an LDR's consequences

Once an LDR is Accepted, its consequences typically affect:

- The corresponding LP entries in POLICY_CONSIDERATIONS.md (status updates, preliminary position replacements, new LP additions)
- SECURITY.md (privacy rules, data tier examples, retention)
- Product docs (pd-business-model, ft-* that touch monetization, user commitments)
- ARCHITECTURE.md in rare cases (data residency, audit trail requirements)

Do not update those documents by hand. Run the LDR propagation workflow:

```
Invoke @.ai/workflows/LDR-PROPAGATOR.md
```

By default this picks up the most recent LDR. To target a specific one:

```
Invoke @.ai/workflows/LDR-PROPAGATOR.md for LDR 0002
```

The workflow reads the LDR, identifies affected documents, presents a plan with per-change certainty markers, and applies changes after confirmation. `[Preliminary]` changes are applied with inline preliminary markers in the affected docs. See [.ai/workflows/LDR-PROPAGATOR.md](../.ai/workflows/LDR-PROPAGATOR.md) for the full procedure.

### LDRs are immutable

Once an LDR has status `Accepted`, **do not edit it retroactively**. If the position changes, write a new LDR with status `Supersedes LDR NNNN` and update the original's status to `Superseded by LDR MMMM`. The original stays exactly as it was — it's the record of what was decided at that moment.

This is what makes LDRs trustworthy: a reader can rely on them to reflect the team's position at the time, not the latest revision.

### Critical edge case: conflict with already-published positions

If you ever find yourself wanting to write an LDR whose position contradicts something already published in a public-facing artifact (terms of use, privacy policy, user agreement), **STOP**. Public-facing legal changes follow a different process: counsel drafts, the project signs off, and only then can internal documents be updated to reflect the change.

Writing an LDR that contradicts already-published positions creates documents that disagree with what users have been told — a situation worth more than the speed of any internal update.

The LDR-PROPAGATE workflow has an explicit edge case for this, but the discipline starts at LDR-creation time: if you sense the conflict, raise it before writing the LDR.

---

## When to surface something to this area

Add an entry (LP-NN in POLICY_CONSIDERATIONS.md) whenever the project's development surfaces a situation where:

- A user-facing promise creates a commitment that needs terms (referrals, credits, SLAs, refunds)
- Personal data is handled in a way that triggers privacy regulation
- The product's behavior resembles a regulated activity (medical advice, financial instrument, professional service)
- A third-party integration sends user data outward and consent is unclear
- Intellectual property of users, third parties, or generated content is involved
- A feature creates a liability that the terms of use should address explicitly

Write an LDR when a position is formed or changed on one of those LPs (see the LDR creation criteria above).

If in doubt, err on the side of capturing. An entry that turns out to be a non-issue is cheap; a missed concern that surfaces during diligence or after launch is expensive.

---

## How this area relates to others

- **[PRODUCT.md](PRODUCT.md) and `docs/product/`**: product decisions frequently create legal implications (a new revenue model, a new actor type, a new data flow). When a product decision surfaces a concern, log it as an LP entry. If the legal position itself becomes the decision being made, write an LDR.
- **[ENGINEERING.md](ENGINEERING.md) and `docs/engineering/`**: engineering decisions about data handling, retention, encryption, and third-party services often have legal weight. When an ADR makes such a decision, cross-reference the corresponding LP entry or LDR.
- **[engineering/SECURITY.md](engineering/SECURITY.md)**: the privacy section of SECURITY.md sets the baseline rules for handling personal data. The legal area documents the **considerations and positions** that those rules respond to. The two evolve together — and LDR-PROPAGATE frequently updates SECURITY.md.

---

## Disclaimer

**This area is not legal advice and does not constitute a legal document.** The terms of use, privacy policy, and any user-facing agreements must be drafted and reviewed by qualified legal counsel before they take effect. The role of this area is to provide that counsel with comprehensive, well-organized input — not to substitute for their judgment.

Even LDRs marked `[Confirmed by counsel]` are internal records of counsel's guidance, not legal opinions in themselves. The legal opinion lives with counsel; the LDR is the team's record of how to apply it.

---

## Related

- [INDEX.md](INDEX.md) — full catalog of all documentation
- [PRODUCT.md](PRODUCT.md) — product overview
- [ENGINEERING.md](ENGINEERING.md) — engineering overview
- [legal/POLICY_CONSIDERATIONS.md](legal/POLICY_CONSIDERATIONS.md) — the policy considerations register
- [legal/ldr/TEMPLATE.md](legal/ldr/TEMPLATE.md) — template for new Legal Decision Records
- [.ai/workflows/LDR-PROPAGATOR.md](../.ai/workflows/LDR-PROPAGATOR.md) — LDR propagation workflow
- [engineering/SECURITY.md](engineering/SECURITY.md) — privacy and security baseline
- [engineering/TECH_DEBT.md](engineering/TECH_DEBT.md) — analogous register for technical debt

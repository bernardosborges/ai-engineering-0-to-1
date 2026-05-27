# Product overview

> **Status:** Draft | v1 | v2 | etc.
> **Owner:** product

Entry point to understanding **what this project does, for whom, and how**. Engineers, designers, operators, and any LLM working on the codebase should read this before touching product-related work.

This document is the **quick-reference layer**. Each section below is a short, high-signal summary; the deep content lives in `docs/product/`:

- **`pd-*.md`** files cover product discovery dimensions — motivation, audience, scope and differentiation, business model, risks, workflows, MVP and validation.
- **`ft-*.md`** files cover features and transversal models — one per significant feature or shared concept (auth, billing, the core data model, etc).

See the [Document map](#document-map) section for the full index of files. See `docs/product/TEMPLATE.md` for the structure used by every document under `docs/product/`.

---

## How it works (one paragraph)

<!-- BOOTSTRAP:HOW_IT_WORKS:START -->
> 📝 Filled during BS-01 (Product discovery) with the high-level summary, refined as features get detailed in BS-02.
>
> One paragraph walking through the core user flow end to end:
>
> - Where the user starts
> - What they interact with
> - What the system does in response
> - What the user receives
>
> Concrete, sequential, plain language. No technical implementation details — those belong in `@docs/engineering/ARCHITECTURE.md`. Deep workflow narrative belongs in `docs/product/pd-workflows.md` and per-feature `ft-*.md`.
<!-- BOOTSTRAP:HOW_IT_WORKS:END -->

---

## Core actors

<!-- BOOTSTRAP:CORE_ACTORS:START -->
> 📝 Filled during BS-01 (Product discovery) as actors are identified, extended in BS-02 if features introduce new actors.
>
> Every actor that interacts with the system, with one or two lines per actor:
>
> - **Actor name** — what role they play, how they interact with the system, what they can and cannot do.
>
> Include AI agents and the system itself as actors if they have meaningful roles. Helps reason about authorization, UX, and operational responsibilities.
>
> Detailed actor responsibilities (workflows, capabilities, restrictions) live in `docs/product/pd-audience.md` and the relevant `ft-*.md`. This section is the quick reference — keep entries short.
<!-- BOOTSTRAP:CORE_ACTORS:END -->

---

## Core entities

The data model centers on a few tightly-coupled entities. Detailed schemas live in the per-feature documents under `docs/product/`.

<!-- BOOTSTRAP:CORE_ENTITIES:START -->
> 📝 Filled during BS-02 (Product document) as entities are surfaced through feature specs.
>
> A high-level sketch of relationships between the most important entities, typically as an ASCII diagram or short list. The goal is orientation, not completeness.
>
> Example shape:
>
> ```
> User ──→ Project ──→ Task
>      ──→ Subscription
> Task ──→ Comment
>      ──→ Attachment
> ```
>
> Keep it readable. Five to ten entities maximum at this level — anything more belongs in `docs/product/ft-<feature>.md` for the relevant feature.
<!-- BOOTSTRAP:CORE_ENTITIES:END -->

---

## Glossary

Terms that mean something specific in this project. If a term is used in multiple docs with a precise meaning, it belongs here.

<!-- BOOTSTRAP:GLOSSARY:START -->
> 📝 Started in BS-01 with the first 5-10 domain terms, extended throughout the project life as new terms emerge.
>
> Format: a table mapping each term to its definition and the doc where it is detailed.
>
> | Term | Definition | Lives in |
> |---|---|---|
> | **<Term>** | One-line definition. | <doc-name> |
>
> Include:
> - Domain-specific entity names (e.g., Workspace, Subscription, Cohort)
> - Internal concepts with a specific meaning (e.g., Wallet, Cooperation Score, Quota)
> - Project-specific personas or roles (e.g., a named AI assistant, a curator role)
> - Acronyms that are not industry-standard
>
> Do NOT include:
> - Standard industry terms (REST, JSON, OAuth, etc.)
> - Terms used only in one doc — keep those local to that doc
<!-- BOOTSTRAP:GLOSSARY:END -->

---

## Phase strategy

High-level overview of the product's phased evolution. Detailed MVP scope and validation strategy live in `docs/product/pd-mvp.md`. The phase-by-phase task breakdown lives in `@docs/ROADMAP.md`.

<!-- BOOTSTRAP:PHASE_STRATEGY:START -->
> 📝 Filled during BS-07 (Roadmap) after the full phase plan is decided. Initial pass may happen earlier if the strategy is clear from BS-01.
>
> Short summary of the phases the product goes through:
>
> ### Phase 1 — <name>
> One-line goal.
>
> ### Phase 2 — <name>
> One-line goal.
>
> ### Phase 3+ — <name>
> One-line goal.
>
> ### Future (v2+)
> Long-term direction, not committed.
>
> Keep entries short. Deep MVP scope (what's in/out, validation hypotheses) lives in `docs/product/pd-mvp.md`. Task-level breakdown lives in `@docs/ROADMAP.md`.
<!-- BOOTSTRAP:PHASE_STRATEGY:END -->

---

## Document map

The product is documented across multiple specialized files under `docs/product/`. This map is the index.

<!-- BOOTSTRAP:DOCUMENT_MAP:START -->
> 📝 Filled progressively during BS-01 and BS-02 as files are created, extended as new files are added throughout the project life.
>
> The map is organized into two groups: discovery (pd-*) and features (ft-*). Each file should have a one-line description.
>
> Suggested format:
>
> ### Product discovery (pd-*)
>
> - **[pd-motivation.md](./product/pd-motivation.md)** — why this system, why now, founder-market fit.
> - **[pd-audience.md](./product/pd-audience.md)** — who needs this, evidence of need, first concrete users.
> - **[pd-scope.md](./product/pd-scope.md)** — what the system does and what it explicitly doesn't.
> - **[pd-business-model.md](./product/pd-business-model.md)** — how the product makes money and operates in production.
> - **[pd-risks.md](./product/pd-risks.md)** — weak points, uncertainties, external risks, pre-mortem, success criteria.
> - **[pd-workflows.md](./product/pd-workflows.md)** — high-level narrative of how each actor interacts with the system.
> - **[pd-mvp.md](./product/pd-mvp.md)** — smallest viable version, validation hypotheses, distribution to first 100 users.
>
> ### Features and transversal models (ft-*)
>
> - **[ft-<feature>.md](./product/ft-<feature>.md)** — one-line description.
>
> Only list files that exist. Files mentioned but not yet created should not appear here.
<!-- BOOTSTRAP:DOCUMENT_MAP:END -->

---

## Open cross-doc questions

Product-level questions that span multiple docs or affect strategy. Document-specific questions live in each respective doc.

<!-- BOOTSTRAP:OPEN_CROSS_DOC_QUESTIONS:START -->
> 📝 Populated and refined throughout the project life. Initial entries may surface during BS-01 and BS-02.
>
> Each entry is a numbered question with one-line context:
>
> 1. **<Question topic>** — short description of what is unresolved. (referenced in <docs>)
>
> Resolved questions are removed (their resolution lives in the relevant product doc or as an ADR). Keeping unresolved ones visible prevents silent drift.
<!-- BOOTSTRAP:OPEN_CROSS_DOC_QUESTIONS:END -->

---

## Cross-document inconsistencies

Inconsistencies found during reading or audit of the product docs. The actual fixes happen in the respective docs; this section tracks them until resolved.

<!-- BOOTSTRAP:INCONSISTENCIES:START -->
> 📝 Populated when inconsistencies are found across product docs. Most common trigger: review at the end of BS-02 or after major product changes.
>
> Each entry describes the inconsistency, which docs disagree, and which version is the source of truth:
>
> 1. **<Topic>** — `<doc-a>` says X; `<doc-b>` says Y. `<source-of-truth>` is correct; `<other-doc>` needs update.
>
> Entries are removed once fixed. For inconsistencies that affect 2+ files, create a PDR documenting the corrected position and run `@.ai/workflows/PDR-PROPAGATOR.md` to apply the fix across all affected docs.
<!-- BOOTSTRAP:INCONSISTENCIES:END -->

---

## Product Decision Records (PDRs)

PDRs are the durable record of significant product decisions made on this project. Each PDR captures:

- **Context** — what surfaced the decision (a user signal, a market shift, a feature crossing a scope boundary, a regulatory factor)
- **Decision** — what was chosen, in concrete product terms (new actor, new flow, new entity, removed feature, new commitment)
- **Consequences** — what changes in the product surface, in the data model, in workflows, in user-facing copy
- **Alternatives rejected** — what was considered and why it lost
- **Affected documents** — which docs need updating to reflect this decision

PDRs live in `docs/product/pdr/`. The template for new PDRs is at [product/pdr/TEMPLATE.md](product/pdr/TEMPLATE.md).

### When to create a PDR

Create a PDR when a product decision is:

- **Significant** — a future contributor reading the product docs would want the context to understand it
- **Likely to be revisited** — someone may be tempted to undo or change it later without realizing the original rationale
- **A change to existing direction** — reverses, restructures, or significantly alters a previous position; not just additive

Routine choices (microcopy variations, icon selection, color tweaks, minor copy adjustments) do not warrant a PDR. The bar is "would the next person joining the project benefit from knowing why we chose this?"

### When to write a PDR vs editing a pd-*/ft-* doc directly

- **Edit the pd-*/ft-* directly** when the change is **additive** (new term in the glossary, new actor entry, new section in a feature spec) and doesn't reverse or significantly modify a previous decision.
- **Write a PDR** when the change is **a decision** that reverses, restructures, or significantly alters a previous direction. The PDR preserves the WHY and the rejected alternatives, which a direct edit would lose.

### How to create a PDR

1. Copy [product/pdr/TEMPLATE.md](product/pdr/TEMPLATE.md) to `product/pdr/NNNN-kebab-case-title.md` where NNNN is the next number in sequence (zero-padded to four digits)
2. Fill every section: Context, Decision, Consequences (including Affected documents), Alternatives rejected. Open questions and References are optional
3. Remove the `<!-- comment -->` blocks — they are guidance for the author, not part of the published PDR
4. Set Status to `Accepted` when the decision is implemented (or when the propagation PR merges)

### How to propagate a PDR's consequences

Once a PDR is accepted, its consequences often affect multiple existing documents (other `pd-*.md`, `ft-*.md`, PRODUCT.md itself, sometimes engineering or legal docs).

Do not update those documents by hand — it is error-prone and inconsistent across files. Run the PDR propagation workflow instead:

```
Invoke @.ai/workflows/PDR-PROPAGATOR.md
```

By default this picks up the most recent PDR (highest number) and propagates its consequences across the affected docs. To target a specific PDR:

```
Invoke @.ai/workflows/PDR-PROPAGATOR.md for PDR 0003
```

The workflow reads the PDR, identifies which documents are affected, presents a plan, and applies the changes after confirmation. See [.ai/workflows/PDR-PROPAGATOR.md](../.ai/workflows/PDR-PROPAGATOR.md) for the full procedure.

### PDRs are immutable

Once a PDR has status `Accepted`, **do not edit it retroactively**. If the decision changes, write a new PDR with status `Supersedes PDR NNNN` and update the original's status to `Superseded by PDR MMMM`. The original stays exactly as it was — it is a record of what was decided at that moment, not a living document.

This is what makes PDRs trustworthy: a reader can rely on them to reflect the truth at the time of the decision, not the latest revision.

### When in doubt

If you cannot decide whether something deserves a PDR, ask: "If a new contributor joined six months from now and disagreed with this product choice, would they have enough context to understand why we made it?" If no, write the PDR.

---

## Related

- `@docs/INDEX.md` — full catalog of all documentation
- `@docs/ENGINEERING.md` — engineering overview (counterpart to this document)
- `@docs/LEGAL.md` — legal overview
- `@docs/ROADMAP.md` — phase overview and task plan
- `@docs/engineering/ARCHITECTURE.md` — technical architecture and dependency rules
- `@docs/engineering/LANGUAGE.md` — language and localization conventions
- `@docs/engineering/SECURITY.md` — auth, privacy, security baseline
- `docs/product/TEMPLATE.md` — structure for files under `docs/product/`
- `docs/product/pdr/TEMPLATE.md` — template for new Product Decision Records
- `.ai/workflows/PDR-PROPAGATOR.md` — PDR propagation workflow

<!--
PYTHON_REFERENCE_EXAMPLE — Real PRODUCT.md filled out

Two examples from different industries showing how a thoughtful PRODUCT.md
fills the quick-reference sections. The deep content these examples reference
(pitch, motivation, full actor descriptions, etc.) lives in the corresponding
`docs/product/pd-*.md` and `ft-*.md` files — NOT in PRODUCT.md.

═══════════════════════════════════════════════════════════════════════════
EXAMPLE 1 — Project management SaaS for distributed teams
═══════════════════════════════════════════════════════════════════════════

## How it works (one paragraph)

A team member opens the platform, sees their workspace dashboard with active projects, decisions, and threads. They create or pick up a task, which carries the full context that led to its creation (the conversation, the alternatives considered, the rationale). When they need input, they tag teammates or invoke the AI assistant. As work progresses, every status change, decision, and discussion becomes part of the asynchronous record — so anyone joining later can navigate the history in minutes instead of asking around.

## Core actors

- **Member** — a person on the team. Creates projects, tasks, decisions; participates in discussions.
- **Admin** — an org-level role. Manages billing, invites, integrations, retention policies.
- **External collaborator** — a contractor or partner with scoped access to specific projects.
- **AI assistant (Iris)** — an in-product AI agent that summarizes long threads, drafts retrospectives, surfaces relevant past decisions. Read-only by default; can act with explicit user confirmation.
- **System** — the platform itself. Sends notifications, manages retention, generates weekly digests.

## Core entities

```
Organization ──→ Workspace ──→ Project ──→ Task
                                       ──→ Decision
                                       ──→ Thread
Member ──→ Organization
       ──→ WorkspaceMembership
Task ──→ Comment
     ──→ Attachment
     ──→ Status
Decision ──→ Alternative
         ──→ Rationale
```

## Glossary

| Term | Definition | Lives in |
|---|---|---|
| **Workspace** | A logical container for a team's projects. One organization can have many. | ft-workspaces |
| **Project** | A bounded unit of work with a goal, a deadline, and a team. | ft-projects |
| **Decision** | A first-class artifact: a question answered, with alternatives considered and rationale. | ft-decisions |
| **Thread** | An asynchronous discussion attached to any artifact. | ft-threads |
| **Iris** | The platform's AI assistant. Surfaces context, drafts summaries. | ft-ai-assistant |
| **Digest** | A weekly summary sent to each member, generated from their workspace activity. | ft-digests |

═══════════════════════════════════════════════════════════════════════════
EXAMPLE 2 — Online learning platform for vocational courses
═══════════════════════════════════════════════════════════════════════════

## How it works (one paragraph)

A student browses the catalog, picks a vocational course aligned with the certification they want, and enrolls. Each week they consume structured video content and submit a practice task — a real-world exercise reviewed by a mentor who is an active professional in the field. The mentor provides written feedback; the student iterates. On course completion, the student receives a certification recognized by the platform's partner employers, who can see the student's profile in the hiring pool.

## Core actors

- **Student** — the primary learner. Enrolls in courses, submits practice tasks, receives certifications.
- **Mentor** — a professional in the field, contracted to review student submissions and provide feedback.
- **Employer** — a company looking to hire certified students.
- **Course author** — typically a senior professional who designs course content. May or may not also be a mentor.
- **Platform operator** — staff member who curates content, vets mentors, manages disputes.
- **System** — handles enrollment, payments, certification issuance, mentor assignment.

## Glossary

| Term | Definition | Lives in |
|---|---|---|
| **Course** | A structured curriculum producing a specific certification. | ft-courses |
| **Module** | A unit within a course; typically one to two weeks of work. | ft-courses |
| **Practice task** | A real-world exercise submitted by the student and reviewed by a mentor. | ft-practice |
| **Certification** | A credential issued upon course completion; recognized by partner employers. | ft-certifications |
| **Mentor pool** | The set of professionals available to review submissions for a given course. | ft-mentors |
| **Hiring signal** | A flag indicating an employer is actively recruiting in a specific skill area. | ft-employers |
-->

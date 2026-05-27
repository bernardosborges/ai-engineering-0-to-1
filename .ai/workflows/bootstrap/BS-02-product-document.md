# BS-02 — Product document

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-02 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the feature documentation conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern), this file for the specific content of BS-02, and the output of BS-01 (`docs/PRODUCT.md` + the seven `docs/product/pd-*.md` files) as input to this step.

---

## 1. Goal

Turn the product discovery captured in BS-01 into per-feature specifications that the rest of the project will build on.

The goal is not to invent features — it is to **document the features the Maintainer already articulated in BS-01 (and during the BS-02 conversation itself)**, in enough depth that BS-03 (architecture) can begin without ambiguity. Each significant feature, core data asset, principal actor with lifecycle, or transversal model gets its own `docs/product/ft-*.md` file.

The depth of each `ft-*.md` matches the maturity of the Maintainer's understanding **now**. Features that are crystal-clear get rich docs (multiple sections, edge cases, lifecycle diagrams). Features that are real but less developed get a Purpose, Actors, and whatever else the Maintainer can articulate, plus an Open questions section listing what's unresolved. The Bootstrapper does NOT fabricate depth.

Critically, BS-02 captures the **baseline** of the product as known today. Features evolve through the project's life: new decisions reshape existing ft-*.md files (via PDRs), and new features that crystallize later get their own ft-*.md files (also via PDRs). The PDR-PROPAGATOR workflow handles both update and creation paths automatically once a PDR is accepted.

---

## 2. What this step produces

By the end of BS-02, the following exists in the repository:

### Documents created

- **`docs/product/ft-*.md`** — one file per significant feature, core data asset, principal actor with lifecycle, or transversal model identified during BS-01 deepening (and refined in BS-02). The count is **dynamic** based on the product. A simple product may produce 4-6 files; a complex domain (multi-sided marketplace, regulated industry, AI-heavy workflow) may produce 10-15.

  Each follows the structure at `docs/product/TEMPLATE.md` as a **floor, not a ceiling**: Purpose, Actors, Vocabulary, Entities, Policies, Edge cases, Open questions, Related. Features with rich domain content extend with their own sections (e.g., a Lifecycle section for status-driven entities, a Per-actor workflows section for multi-actor flows, an Entity overview ASCII diagram, etc.).

### Documents updated

- **`docs/PRODUCT.md`** — three sections that were left as BOOTSTRAP markers in BS-01 are now filled:
  - **Core entities** — the entity-relationship sketch (ASCII diagram or short list) showing how the main entities relate. 5-10 entities maximum at this level; per-feature detail lives in the relevant `ft-*.md`.
  - **Document map** — index of all `pd-*.md` and `ft-*.md` files now in the repo, with a one-line description per file. Only files that **exist** are listed (no placeholders).
  - **Glossary** — extended with any new domain terms surfaced during BS-02 (typically: entity names introduced when features were spec'd, status names, lifecycle terms).

### What is NOT produced in BS-02

- Architecture decisions, technology choices, library picks — those are BS-03 / BS-04
- Test strategy, coding conventions — BS-05
- Roadmap and phase planning — BS-07
- Detailed engineering implementation (table schemas, API shapes, code structure) — those emerge during task execution, not in BS-02. `ft-*.md` describes the feature **from a product perspective** (what entities exist, what policies apply, what edge cases the product handles). Engineering details that follow from the product spec live in `docs/engineering/*` or in code.
- PDRs — those are created throughout the project's life when product decisions are made. BS-02 captures the initial baseline; PDRs evolve it.

### What about features that emerge AFTER BS-02?

Features that crystallize later in the project's life are added through PDRs:
- A PDR documents the decision (why this feature, what it does, what it replaces or extends).
- The PDR's "New docs to create" section lists the new `ft-*.md` files the decision introduces.
- The PDR-PROPAGATOR workflow conducts a mini-discovery for each new file before propagating consequences to existing docs.

This means BS-02 is the **starting baseline**, not the complete and final catalog. The Bootstrapper does NOT push the Maintainer to invent features they don't yet have. If a feature is genuinely emergent (the Maintainer mentions it but cannot articulate it), it stays as an Open question in a relevant `pd-*.md` or `PRODUCT.md` and waits for a PDR to formalize it.

---

## 3. Exit criteria

BS-02 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

- [ ] **Feature inventory complete**: the Maintainer and Bootstrapper have explicitly agreed on the list of `ft-*.md` files to create (see sub-block 7.2 — Feature inventory). The list is grounded in BS-01 content (actors from `pd-workflows.md`, scope from `pd-scope.md`, MVP from `pd-mvp.md`).

- [ ] **Every file in the inventory exists** in `docs/product/` with at minimum: Purpose section filled with substantive content, plus whatever additional sections the feature's maturity supports. Empty stubs are not acceptable; if a feature is genuinely thin, the file states what's known plus an Open questions section listing what isn't.

- [ ] **`docs/PRODUCT.md` Core entities** is filled with the entity-relationship sketch.

- [ ] **`docs/PRODUCT.md` Document map** lists every `pd-*.md` and `ft-*.md` file that exists in the repo, with one-line descriptions.

- [ ] **`docs/PRODUCT.md` Glossary** has been extended with any new domain terms surfaced during BS-02.

### Soft requirements (worth pushing for but not blockers)

- [ ] Cross-references between `ft-*.md` files are in place where features depend on each other (e.g., `ft-billing.md` references entities defined in `ft-subscriptions.md`).

- [ ] Entities in the Core entities diagram are consistent with the entities documented in the per-feature `ft-*.md` files (no entity in the diagram is missing from its owning feature; no feature claims an entity that's not in the diagram).

- [ ] Open questions surfaced during feature spec are captured in the relevant `ft-*.md` file's Open questions section, not lost.

### What does NOT block exit

- Features that are recognized but too immature for an `ft-*.md` (no clear actors, no policies yet, no entity names yet) — those stay as entries in `pd-*.md` Open questions and are addressed via PDR when they crystallize
- Sparse content in some `ft-*.md` files for genuinely uncertain features, as long as each file documents the uncertainty explicitly in Open questions
- Engineering details (database schema, API shape, code structure) — those are not BS-02's concern

The Bootstrapper does not advance to BS-03 silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before moving on, I want to address X. We haven't yet created `ft-<feature>.md` even though the feature is referenced in pd-workflows.md and in the MVP definition."

---

## 4. Anchor questions

These are the four questions that **must be answered with confidence** before BS-02 closes. They are not asked in order — they emerge through the conversation flow (see Section 7 — Conversation flow). The Bootstrapper keeps mental track of which ones are still open, and surfaces any that remain unanswered before declaring the step complete.

### Anchor 1 — What is the complete list of ft-*.md files to create?

This is the central question of BS-02. The Feature inventory step (sub-block 7.2) exists specifically to answer it.

The list is grounded in BS-01:
- Every principal actor in `pd-workflows.md` Per-actor workflows is a candidate (especially actors with rich lifecycle — onboarding, status transitions, lifecycle events)
- Every core data asset hinted at in `pd-motivation.md` Strategic asset is a candidate
- Every workflow in `pd-workflows.md` that has its own internal complexity is a candidate
- Every transversal model implied by the product (billing, auth, observability, notifications, audit) is a candidate
- Operational functions (operator review, curation, moderation) are candidates if the Maintainer has them in scope

The Bootstrapper does NOT impose this list. It surfaces candidates based on BS-01 content and the Maintainer decides. The output is a concrete, finite list that both parties agree on before deepening begins.

### Anchor 2 — How do the core entities relate?

This becomes the **Core entities** section in `PRODUCT.md`. It is an ASCII diagram or short list showing the main entities and their cardinality relationships.

Why it matters: the Core entities diagram is the quick-reference layer that engineers and other LLMs consult to orient themselves. Without it, the only way to understand the data model is to read every `ft-*.md`, which defeats the purpose of having a quick-reference.

The diagram is high-level (5-10 entities maximum). Detailed entity shapes (fields, types, constraints) live in the `ft-*.md` that owns each entity, not in the diagram.

### Anchor 3 — What transversal models cross multiple features?

Transversal models (billing, auth, audit, observability, notifications, file storage) cut across multiple features and benefit from their own `ft-*.md` file even if they don't have a single "owner" actor.

Why it matters: without explicit transversal docs, those concerns get smeared across feature-specific docs, making it impossible to reason about (e.g., "how does this product handle authentication" requires reading 5 different files).

The Bootstrapper surfaces transversal candidates explicitly: "We have <feature A> and <feature B> that both involve authentication. Is auth a transversal model that deserves its own ft-auth.md?"

The Maintainer decides which transversal models warrant their own file. Some products genuinely don't have any (a simple single-actor productivity tool may have only feature docs, no transversal). Others (multi-sided marketplaces, B2B SaaS with billing) have several.

### Anchor 4 — What policies and edge cases does each feature carry?

The Policies and Edge cases sections of each `ft-*.md` are where the product's real behavior is documented. A feature without explicit policies is under-specified; a feature without explicit edge cases will surface them painfully during implementation.

The Bootstrapper probes for both during each feature's deepening:
- "What rules does this feature enforce? If X happens, what does the system do?"
- "What's the weirdest case this feature has to handle? What if the user does Y, what if Z is missing, what if W conflicts with V?"

Not every feature has dense policies and edge cases — a simple read-only feature may have one of each. But every `ft-*.md` should have these sections filled with what's known, not skipped.

---

## 5. Coverage strategy

Unlike BS-01 (which had a fixed 7-dimension checklist), BS-02's coverage is **dynamic** — driven by the list of ft-*.md files the Maintainer and Bootstrapper agreed on in the Feature inventory step.

What's constant in BS-02 is the **categories** of files to consider. The Bootstrapper holds these categories mentally during the Feature inventory step (sub-block 7.2) and probes for candidates in each.

### Category 1 — Principal actors with lifecycle

Actors that have their own multi-state lifecycle, are core to the product's operation, and have rich policies governing their state transitions. Examples:

- A `Buyer` entity with `pending → onboarding → qualified → identified` states, each with its own transition rules
- A `Supplier` entity with prospecting, registration, capability tracking, quality metrics
- A `Project` or `Account` entity with subscription tiers, billing status, member roles

Signal that an actor warrants its own `ft-*.md`: the Maintainer can describe a multi-step lifecycle for it, or distinct sub-categories within it that behave differently, or operator responsibilities tied to it.

Signal that an actor does NOT warrant its own `ft-*.md`: it's a simple read-only role (e.g., a `Reader` who just consumes content) with no lifecycle to document. That actor lives in `pd-audience.md` or `PRODUCT.md` Core actors only.

### Category 2 — Core data assets

The entities or data structures that **accumulate and compound** value over time. These map directly to the Anchor 5 answer from BS-01 (Strategic asset).

Examples:
- A `PriceHistory` / `PriceDatabase` that accumulates supplier quotes over time
- A `Catalog` of curated supplies with attributes, aliases, embeddings
- A `Library` of templates or patterns contributed by users
- A `Reputation` graph that accumulates trust signals

Signal: the data asset has its own structure (entities, relationships), its own policies (append-only, validity rules, cleanup rules), and is referenced by multiple features.

These deserve their own `ft-*.md` because they are the strategic asset of the product — under-specifying them propagates errors throughout the system.

### Category 3 — Principal workflows

End-to-end flows that involve multiple actors and multiple state transitions. Examples:

- A `quotation-collection` workflow that orchestrates Buyer-side RFQs, system-driven supplier outreach, and Operator-driven curation
- A `course-enrollment` workflow that spans Student onboarding, Mentor matching, Operator approval
- A `support-ticket` workflow with creation, routing, escalation, resolution

Signal: the workflow has its own internal sequence diagram (or could have one), involves 3+ actors, has policies about ordering and timing, has edge cases at intersections.

A workflow that's owned by a single actor and is purely linear (e.g., "user clicks button → page loads") does NOT need its own `ft-*.md` — it's part of that actor's workflow section in `pd-workflows.md` or in the actor's `ft-*.md`.

### Category 4 — Transversal models

Concerns that cut across multiple features but don't belong to any single one. Examples:

- `ft-billing.md` — pricing, subscriptions, transactions, invoicing
- `ft-authentication.md` — login, session management, role-based access
- `ft-observability.md` — what gets logged, what gets measured, what alerts fire
- `ft-notifications.md` — when/how the system notifies actors (email, push, in-app)
- `ft-audit.md` — what actions are recorded, retention, who can read the trail

Signal: the concern affects 2+ features and has its own consistent set of policies.

Not every product has all of these. A productivity tool for a single user may have no billing, no notifications, no auth (single-account). A regulated B2B platform may have all of them plus compliance-specific transversals.

### Category 5 — Operational functions

Concerns owned primarily by operators (humans on the team), not end-users. Examples:

- `ft-operator-tasks.md` — the task queue operators work through (curation reviews, supplier outreach, dispute resolution)
- `ft-curation-guide.md` — the rules and conventions operators follow when curating content (data, taxonomy, quality)
- `ft-moderation.md` — content moderation policies and tooling

Signal: the Maintainer described operator responsibilities during BS-01 that go beyond "handles edge cases." Products with hybrid or fully-assisted production operator models (per BS-01 Anchor 4) usually have at least one operational function deserving its own file.

Fully automated products (no operators) usually skip this category.

---

## 6. How the Bootstrapper uses these categories

The 5 categories are the **map for the Feature inventory step**, not a checklist applied to every product. The Bootstrapper:

1. **Holds the categories mentally** while running the Feature inventory conversation (sub-block 7.2)
2. **Surfaces candidates in each category** based on BS-01 content, asking the Maintainer to confirm or reject
3. **Builds the final inventory** as a concrete list of N ft-*.md files (typically 4-15), agreed by both parties
4. **Tracks coverage during deepening** — each agreed ft-*.md gets its own deepening pass in sub-block 7.5

Not every category produces a file for every product. The Bootstrapper does NOT pad the inventory by inventing transversal models the product doesn't have, or by promoting simple actors into their own files just to fill the category. The Maintainer's reality drives the list.

The deepening pass for each file is calibrated to maturity:

- **Mature features** (Maintainer has clear vocabulary, defined policies, named entities, walked through edge cases during BS-01): the deepening produces rich `ft-*.md` files with multiple sections beyond the TEMPLATE floor.

- **Recognized but less mature features** (Maintainer has a name for it and knows it exists, but vocabulary and policies are still fuzzy): the deepening produces `ft-*.md` files with Purpose, partial Actors and Vocabulary, and prominent Open questions sections. These will be enriched via PDRs as the project progresses.

- **Emergent features** (Maintainer mentions the concept but can't articulate it): these do NOT get an `ft-*.md` in BS-02. They live as Open questions in a relevant `pd-*.md` or `PRODUCT.md` and wait for a PDR when they crystallize.

The Bootstrapper does not force maturity. A feature that's still emergent in BS-02 will be more honestly documented later via PDR than fabricated now.

---

## 7. Conversation flow

The BS-02 conversation follows an arc with **nine sub-blocks**, similar in structure to BS-01 but calibrated to per-feature documentation. The major difference: sub-block 7.2 is **Feature inventory** (new — no equivalent in BS-01), which produces the concrete list of `ft-*.md` files before any deepening begins. Without that list, the deepening conversation would drift across features without structure.

The Bootstrapper does NOT announce these blocks to the Maintainer ("Now we're in sub-block 7.5 — Per-feature deepening"). The transitions happen naturally through the conversation's flow.

### 7.1 — Opening and recap (5-10 minutes)

**Purpose:** Re-establish shared context after BS-01 completed. The Bootstrapper recaps what BS-01 captured and frames what BS-02 will produce, so the Maintainer enters the next phase oriented.

**Technique:**

1. **Recap BS-01 output briefly.** Two or three sentences referencing the most important content:

  > "BS-01 captured the product foundation. The product is <one-line description>, for <primary user>, with <strategic asset> as the compounding value. Core actors are <X, Y, Z>. The seven `pd-*.md` files are in the repo. Now in BS-02, we turn that foundation into per-feature specs."

2. **Frame BS-02's scope.** Explain what's produced and what's not:

  > "BS-02 creates one `ft-*.md` file per significant feature, core data asset, principal actor with lifecycle, or transversal model. The exact list depends on your product — we'll define it together in a few minutes. After that, we go feature by feature, documenting what you already know. Features that are still emergent stay as open questions, to be addressed via PDR later when they crystallize."

3. **Set expectations on duration.**

  > "BS-02 typically takes 60 to 180 minutes depending on how many features your product has. We stay in this same chat throughout, with pauses if you need."

**Signal to move on:** the Maintainer is oriented, confirms readiness to proceed, and has no outstanding clarifications about BS-01's output.

### 7.2 — Feature inventory (15-25 minutes)

**Purpose:** Produce the **concrete, finite list** of `ft-*.md` files to create. This is the central organizing decision of BS-02 — without it, deepening becomes unbounded.

**Why this needs its own sub-block:** if the Bootstrapper jumps straight into deepening, the conversation drifts. Each feature would either be over-explored (Maintainer goes deep on the first one and exhausts energy) or under-explored (Maintainer surfaces features one at a time and the Bootstrapper can't sequence them). Defining the list upfront makes the rest of BS-02 a tractable iteration.

**Technique:**

1. **Walk the 5 categories systematically** (Section 5). For each, surface candidates grounded in BS-01 content:

  > "Looking at `pd-workflows.md`, you described <Actor X> with a multi-step lifecycle (onboarding → qualified → ...). That feels like it warrants its own `ft-<actor>.md`. Agreed?"

  > "Your strategic asset is <X> from `pd-motivation.md`. That data asset has its own structure and policies. Does it deserve its own file?"

  > "I see <Feature A> and <Feature B> both involve authentication. Is auth a transversal model that should have its own `ft-auth.md`, or does it live inside each feature?"

2. **Be willing to challenge candidates** but accept the Maintainer's judgment. The Bootstrapper surfaces possibilities; the Maintainer decides:

  - "Actor X has a lifecycle but it's only two states — I'd argue that's thin for its own file. Put it inside the workflow file instead?"
  - "You mentioned notifications but you don't have a real notification system yet — let's leave it as an open question for now, address via PDR when it crystallizes."

3. **For each candidate, decide one of three outcomes:**
  - **A) Create now**: feature is mature enough to spec. Add to inventory.
  - **B) Don't create, deferred to PDR**: feature is real but Maintainer cannot articulate it yet. Record as open question in relevant `pd-*.md`.
  - **C) Don't create, lives elsewhere**: feature doesn't warrant its own file. Lives in an existing `pd-*.md` or inside another `ft-*.md`.

4. **Capture the final inventory in chat** as a numbered list. Anchor it via capture-aloud:

  > "Capturing the BS-02 inventory:
  >
  > 1. `ft-buyer-management.md` — Buyer lifecycle, status transitions, qualification flow
  > 2. `ft-supplier-management.md` — Supplier registration, coverage, capability tracking
  > 3. `ft-price-database.md` — RFQ, SupplierQuotation, PriceHistory entities
  > 4. `ft-quotation-collection.md` — End-to-end RFQ workflow
  > 5. `ft-billing.md` — Wallet, transactions, referral rewards
  > 6. `ft-operator-tasks.md` — Task queue for human operators
  > 7. `ft-curation-guide.md` — Operator curation rules and policies
  >
  > Deferred to PDR (recognized but not yet mature): notifications system, fraud detection, dispute resolution.
  >
  > This list goes into `docs/PRODUCT.md` Document map at consolidation."

5. **Confirm the list explicitly before moving to deepening.** The Maintainer's "yes that's the list" is the commitment that this sub-block produces.

**Forcing function — challenge under-scoped inventories:**

If the Maintainer proposes 2-3 files for what sounds like a complex product, challenge:

> "Three files feels light for what you described. <Specific examples of things not yet captured>. Are you sure you don't want to spec X separately?"

**Forcing function — challenge over-scoped inventories:**

If the Maintainer proposes 15+ files for a product that sounds smaller, challenge:

> "Fifteen feels heavy. <Specific examples of things that might merge>. Some of these might live as sections inside others — let's check which ones are genuinely independent."

**Signal to move on:** the inventory is captured, both parties agree, and the list has 4-15 entries (calibrated to product complexity). Files explicitly deferred to PDR are noted.

### 7.3 — Order the inventory (5 minutes, mostly mental)

**Purpose:** Decide the **order** in which features will be deepened. The order matters because some features reference entities from others — deepening in the wrong order forces backward edits.

**This sub-block is largely silent.** The Bootstrapper doesn't narrate "I'm now ordering the inventory." It just naturally picks the first feature to deepen.

**Heuristic for ordering:**

1. **Foundational entities first.** Features that define core entities others depend on. In a marketplace, the actors (Buyer, Supplier) before the workflows (Quotation, RFQ). In a content platform, the User and Content before the Workflows (publishing, moderation).

2. **Core data assets next.** Right after the foundational actors, document the data assets that accumulate (PriceDatabase, Catalog, etc.). These often introduce additional entities that downstream features reference.

3. **Principal workflows third.** Once the entities are in place, the workflows that orchestrate them have clear vocabulary.

4. **Transversal models toward the end.** Billing, auth, observability — these touch everything and benefit from seeing all the features first. Doing billing first when entities aren't defined yet means revising billing repeatedly.

5. **Operational functions last.** Operator tasks reference everything; doing them last means each task has clear targets.

**Trade-off the Maintainer may override:** the Maintainer may want to deepen the most exciting feature first (their MVP focus, the one they care most about). That's fine — the Bootstrapper accepts the override but flags the cost: "Sure, we'll start with X. Just know we may revisit it later if Y reveals entities X needs."

**Signal to move on:** the Bootstrapper has mentally chosen the order. No output to share with the Maintainer — the next sub-block just begins with the first feature.

### 7.4 — Pre-deepening preview (3-5 minutes)

**Purpose:** Set the rhythm before per-feature deepening begins. The Maintainer knows what to expect when iterating over N files.

**Technique:**

1. **Explain the per-feature pattern.**

  > "For each of the <N> files in the inventory, we'll do the same pattern: I ask about Purpose first (one sentence about what this feature is for), then we walk Actors, Vocabulary, Entities, Policies, Edge cases. As content surfaces I anchor it in the chat with `Capturing X: ...` so we don't lose it. We do this in one pass per feature — not perfect, but substantive. When we've covered all files, I consolidate everything into the actual file contents and you apply them in one go."

2. **Confirm the order.**

  > "I'll start with <first feature> because <reason — foundational, MVP core, etc.>. Then <second>, then <third>. Sound good?"

3. **Address pacing.**

  > "Each feature takes 5 to 20 minutes depending on its complexity. We can pause between features if you need. If you realize partway through that we should split or merge a file, say so — we adjust the inventory."

**Signal to move on:** the Maintainer is oriented, the order is agreed, and we're ready to deepen the first feature.

### 7.5 — Per-feature deepening (loop, 5-20 minutes per feature)

**Purpose:** Iterate through the inventory, capturing the substantive content for each `ft-*.md`. This is the longest sub-block of BS-02 — typically 30-90 minutes total across all features.

**Per-feature pattern:**

For each `ft-<feature>.md` in inventory order:

1. **Open with Purpose.** Ask one anchored question:

  > "Tell me about <feature> in one or two sentences. What is this feature for, in the context of the product?"

  The Maintainer's first answer becomes the seed for the Purpose section.

2. **Walk Actors.** Who interacts with this feature? Reference `pd-workflows.md` if useful:

  > "Looking at the actors we identified in BS-01, which ones touch this feature? Are there any actors specific to this feature that didn't surface in `pd-workflows.md`?"

3. **Walk Vocabulary.** What are the domain terms specific to this feature?

  > "What terms specific to this feature should be in vocabulary? Status names, entity sub-types, lifecycle stages, anything you'd expect a new team member to ask 'what does X mean'."

4. **Walk Entities.** What entities does this feature own or define?

  > "What entities does this feature own? Don't worry about field-level detail yet — just the entity names and how they relate. If an entity is shared with another feature, point out which feature owns it."

  Sketch the entities as the Maintainer describes them. ASCII relationships are fine ("A ──→ B (1:N)").

5. **Walk Policies.** What rules does this feature enforce?

  > "What are the rules of this feature? If a user does X, what does the system do? If state Y happens, what's the response? Push for explicit rules, not generic descriptions."

6. **Walk Edge cases.** What unusual or boundary situations does this feature handle?

  > "What's the weirdest case this feature has to handle? What if <X happens>? What if <Y is missing>? What if <Z conflicts with W>?"

  Use the "Identifying hidden complexity" forcing function aggressively here. Edge cases are where features either crystallize or reveal they're not yet thought through.

7. **Note Open questions.** What's still uncertain about this feature?

  > "What about this feature don't you know yet? What decisions are deferred, what behaviors are TBD?"

8. **Capture via capture-aloud throughout.** Same pattern as BS-01:

  > "Capturing for `ft-supplier-management.md`:
  >
  > Purpose: Defines how suppliers enter the network, what data is maintained, how supplier participation evolves.
  >
  > Actors: Operator (registration), Supplier (the vendor), Buyer (suggests suppliers), system (uses data for outreach).
  >
  > Entities: Supplier, ContactPerson, SupplierCoverage, SupplierLead, ServiceArea.
  >
  > Lifecycle states: prospecting, mapped, active, paused (for service areas).
  >
  > Policy: Active supplier in active ServiceArea is the only one contacted.
  >
  > Edge case: supplier moves location → operator updates address, coordinates re-geocoded, primary_service_area may change.
  >
  > Open questions: inactivity threshold (auto-deactivate after how many days), self-registration portal verification process.
  >
  > This goes into `ft-supplier-management.md` at consolidation."

**Maturity-aware depth:**

The Bootstrapper calibrates per feature:

- **Mature features** (rich Maintainer answers, named entities, walked-through edge cases): produce 5-15 anchor captures across the feature. The resulting `ft-*.md` will be 300-1000+ lines.

- **Recognized but less mature features** (Maintainer has Purpose and Actors but Vocabulary and Policies are fuzzy): produce 3-5 anchor captures, with prominent Open questions. The resulting `ft-*.md` will be 100-300 lines, with several Open questions entries.

- **Emergent features that were kept in inventory by mistake** (the Maintainer can't answer the Purpose question coherently): stop the deepening and renegotiate. Either defer to PDR (move to "out of inventory, open question in pd-*.md") or pause this feature and return to it after the other features clarify it.

**Cross-feature signals:**

As content surfaces, watch for entities or vocabulary that overlap with other features in the inventory. When this happens, note it explicitly:

> "You just mentioned <EntityX> for `ft-A.md`. That same entity will appear in `ft-B.md` when we get to it — at consolidation we'll cross-reference. For now, capturing it once here."

The single most important rule: **don't manufacture depth**. If the Maintainer doesn't know a feature's edge cases yet, capture that uncertainty as Open questions. Faking edges produces specs the Maintainer will reverse.

**Signal to move on (per feature):** the feature's Purpose is clear, Actors are listed, Entities are sketched, at least one Policy and one Edge case are captured (or explicitly noted as Open questions). Time to move to the next feature.

**Signal to move on (whole sub-block):** every feature in the inventory has been deepened. The Maintainer is fatigued but the substantive content is captured in the chat as anchors. Time to consolidate.

### 7.6 — Cross-feature audit (10-15 minutes)

**Purpose:** Before consolidation, walk the inventory once more checking for **cross-feature consistency**. Catches inconsistencies before they get written to files.

**Why this needs its own sub-block:** during per-feature deepening, the Maintainer's mental model evolves. Something said in feature 1 may conflict with something said in feature 5. Without an explicit audit, those inconsistencies make it into files and surface painfully later.

**Technique:**

1. **Walk the entity list across features.** For each entity mentioned, check:
  - Is the same entity name used consistently across features?
  - Is there a single owner feature, or is ownership ambiguous?
  - Are there entities mentioned in one feature but never spec'd in their owner?

  Example check:

  > "I noticed `ft-supplier-management.md` owns Supplier, but `ft-price-database.md` mentions Supplier with slightly different attributes. Let me check: in the price database context, is Supplier the same entity or a denormalized projection?"

2. **Walk the actor list across features.** For each actor:
  - Does the actor's role differ across features? If so, is that intentional?
  - Are there actors mentioned in features that aren't in `PRODUCT.md` Core actors? (If yes, update Core actors at consolidation.)

3. **Walk the vocabulary across features.** For each domain term:
  - Same term used differently in two features?
  - Term defined in one feature but used unexplained in another (consolidation will surface this)?

4. **Walk the policy contradictions.** Most subtle category:
  - Policy in feature A says "X always triggers Y"; policy in feature B implies "X sometimes doesn't trigger Y"?
  - Edge case in feature A handled differently than the equivalent edge case in feature B?

5. **Surface findings to the Maintainer.** Frame as honest observation, not gotcha:

  > "Before consolidation, let me flag a few cross-feature things I noticed. <List them>. Want to resolve each now, or note as cross-doc open questions for later?"

6. **Resolve or defer.** Each inconsistency becomes either:
  - **Resolved now**: agreement on the correct version; both features updated at consolidation
  - **Deferred**: added to `PRODUCT.md` Open cross-doc questions section, to be resolved later (possibly via PDR)

**Why this is hard:** the Bootstrapper has to hold a lot of state by this point. Use the capture-aloud anchors from sub-block 7.5 as the working memory — re-scan them mentally, looking for the cross-feature signals.

**Signal to move on:** all known inconsistencies are either resolved or noted as cross-doc open questions. The substantive content is internally consistent enough to consolidate.

### 7.7 — Consolidation and visual validation (30-45 minutes)

**Purpose:** Shift the mode of the conversation. The Bootstrapper has been anchoring content via capture-aloud throughout 7.5 and 7.6, but nothing is in the repo yet. Now consolidate everything into the actual file contents, present them to the Maintainer for revision, and have them applied.

This is the **single consolidation moment** of BS-02 — same pattern as BS-01's 7.7, scaled up.

**Why consolidation in BS-02 takes longer than in BS-01:**

BS-01 produced 8 files (PRODUCT.md + 7 pd-*.md). BS-02 produces 5-16 files (PRODUCT.md updates + N ft-*.md). Each `ft-*.md` may also be much larger than a `pd-*.md` because features have more internal structure (lifecycle, multi-entity diagrams, dense policies).

**Technique:**

1. **Announce the shift in mode.**

  > "We've covered every feature. Now I'll consolidate everything we discussed into the actual file contents. I'll present them in batches — first PRODUCT.md updates, then the ft-*.md files in groups of 2-3, with a pause between batches so you can review. When we converge on all of them, you apply them to the repo in one go."

2. **Batch the consolidation.** Don't try to present 16 files in one turn — the Maintainer can't review that much at once. Suggested batches:

  - **Batch 1**: `PRODUCT.md` updates (Core entities, Document map, Glossary additions). Present, get confirmation.
  - **Batch 2**: 2-3 mature `ft-*.md` files (those with rich content). Present, invite revision.
  - **Batch 3**: another 2-3 `ft-*.md` files.
  - ... continue until done.

3. **For each batch, present complete file contents** in code blocks with file path headers:

```
  ### File: docs/product/ft-supplier-management.md

  <complete markdown content>
```

4. **After each batch, invite revision:**

  > "Review this batch. Point out anything that misrepresents what you said, anything missing, anything that should be reworded. I'll revise and resend before we move to the next batch."

5. **Revise as needed.** When the Maintainer flags an issue:
  - Apply the fix
  - Check if the fix has ripples in other batches (e.g., entity rename affects 3 files)
  - Resend only the affected files in the next message

6. **Cross-doc consistency check during consolidation.** The Bootstrapper enforces:
  - Every entity in `PRODUCT.md` Core entities appears in exactly one `ft-*.md`'s Entities section as its owner
  - Every `ft-*.md` listed in `PRODUCT.md` Document map exists in the consolidation output
  - Vocabulary terms defined in `PRODUCT.md` Glossary are used consistently across `ft-*.md` files

7. **Wait for application.** The Bootstrapper does NOT move to 7.8 until the Maintainer confirms all files are in the repo.

**Forcing function — surface ripples of corrections:**

When the Maintainer corrects something, list ripples explicitly:

> "You said the entity isn't `Order` — it's `QuotationRequest`. That affects:
>
> - `ft-price-database.md` Entities section (where I'd called it Order)
> - `ft-quotation-collection.md` Per-actor workflows section (where 'order received' should be 'quotation request received')
> - `PRODUCT.md` Glossary (Order definition becomes QuotationRequest)
>
> I'll revise all three and resend in the next batch."

**Why batched consolidation works:**

- The Maintainer reviews in tractable chunks instead of drowning in 50 pages of markdown
- Inconsistencies caught in batch 2 propagate to batch 3 corrections
- Revision iterations are bounded (each batch has its own iteration loop)

**Signal to move on:** all batches are presented, all corrections applied, the Maintainer has confirmed all files are in the repo. BS-02 content is now durable.

### 7.8 — Final audit (10-15 minutes)

**Purpose:** Systematic check against Exit criteria (Section 3) and Anchor questions (Section 4). Close gaps before declaring BS-02 complete.

**Technique:**

1. **Walk the Exit criteria checklist.** For each:
  - Feature inventory complete and agreed?
  - Every file in inventory exists with substantive content?
  - PRODUCT.md Core entities filled?
  - PRODUCT.md Document map updated?
  - PRODUCT.md Glossary extended?

2. **Walk the four Anchor questions.** For each:
  - Inventory list captured and agreed in writing?
  - Core entities diagram in PRODUCT.md?
  - Transversal models identified (or explicitly absent for this product)?
  - Each feature has at least one Policy and one Edge case (or explicitly noted as Open questions)?

3. **Surface gaps to the Maintainer.** Frame as honest acknowledgment:

  > "Before we close BS-02, let me check what we have. I see we haven't yet captured:
  >
  > 1. **Glossary additions from BS-02** — several new entity names (QuotationRequest, BidComparison, SupplierCoverage) surfaced but I'm not sure if they made it into PRODUCT.md Glossary
  > 2. **Document map** — Need to verify the file list in PRODUCT.md matches what's actually in docs/product/
  >
  > Cover these now?"

4. **Distinguish "cover now" from "defer with structure."** Some gaps are blockers (Anchor 1 — inventory not agreed); others can be open questions (one ft-*.md has no Edge cases but the Maintainer genuinely doesn't know any yet).

**Signal to move on:** all Exit criteria met, all four Anchor questions answered, the Coverage map (the agreed inventory) has been fully traversed.

### 7.9 — Bridge to BS-03 (5-10 minutes)

**Purpose:** Close BS-02 cleanly. Summarize what got captured. Surface what Decisions Captured will carry into BS-03/BS-04. Set up BS-03.

**Technique:**

1. **Summary recap.** Three or four sentences:

  > "BS-02 captured the product as <N> ft-*.md files. The major pieces are <X, Y, Z>. Core entities diagram in PRODUCT.md shows <entity count> entities and their main relationships. Transversal models we documented: <list>. Open cross-doc questions remain around <list 2-3 main ones>."

2. **List Decisions Captured for later steps.** Recap any tech/architecture/legal/operational signals that surfaced during BS-02:

  > "A few new Decisions Captured surfaced in BS-02 (added to the ones from BS-01):
  >
  > - **Storage need**: <X> feature implies file storage (BS-04)
  > - **Search need**: <Y> feature implies full-text search or vector similarity (BS-04)
  > - **External integration**: <Z> feature implies <SDK> integration (BS-04)
  > - **Architectural pattern**: workflows for <feature> imply event-driven async dispatch (BS-03)
  > - **Compliance signal**: <feature> handles <regulated data> (BS-05, add LP entry)
  >
  > Any of these you want to revisit now, or carry forward?"

3. **Preview BS-03.** Two sentences:

  > "BS-03 takes the features we just documented and decides the architectural shape: clean architecture vs other patterns, layering rules, dependency direction. By the end of BS-03, we have `docs/engineering/ARCHITECTURE.md` filled with the structural decisions."

4. **Ask for readiness.**

  > "BS-03 typically takes 45 to 90 minutes. We continue in this same chat. Ready to start BS-03, or want a break?"

5. **If the Maintainer pauses:** all `ft-*.md` and `PRODUCT.md` updates are already in the repo (applied during 7.7), so nothing additional to save. The next session starts by reading what was captured, recapping briefly, and confirming nothing has changed before opening BS-03.

**Signal that BS-02 is done:** the recap is agreed, Decisions Captured are confirmed, the Maintainer either kicks off BS-03 or explicitly pauses. The Bootstrapper does NOT silently roll into BS-03 — the transition is an explicit acknowledgment.

---

## 8. Forcing functions specific to BS-02

The forcing functions from BOOTSTRAPPER section 10 apply across all BS steps. The following are calibrated variations specifically for feature documentation — situations that come up frequently in BS-02.

### 8.1 — When the Maintainer wants to spec a feature that doesn't have a clear actor

**Symptom:** Maintainer proposes `ft-<thing>.md` but can't say who interacts with it. The thing is internal infrastructure (e.g., "the algorithm that matches buyers to suppliers") rather than a feature.

**Why it's a problem:** internal infrastructure isn't a product feature — it's an implementation detail. Documenting it as a feature pollutes the product layer with engineering concerns.

**Response pattern:**

> "When you say <thing>, who's the user that experiences it? If no actor directly experiences it, this might be infrastructure that lives inside another feature, not its own ft. For example, the matching algorithm probably lives inside `ft-quotation-collection.md` as a sub-section, not as `ft-matching.md`. Sound right?"

Either merge it into an existing feature or document it as a sub-section of the relevant `ft-*.md`. Internal infrastructure that doesn't have a clear product surface is engineering's concern (lives in `docs/engineering/*` if it needs documentation at all).

### 8.2 — When the Maintainer slides into engineering detail during feature spec

**Symptom:** Maintainer starts describing implementation specifics — "we'll use a Postgres trigger", "this needs a Redis cache", "we'll write this as a state machine using XState".

**Why it's a problem:** BS-02 documents the product, not the implementation. Engineering details land in BS-03+ and in the code. Capturing them in `ft-*.md` couples product spec to implementation, making both harder to evolve.

**Response pattern:**

> "Got it on <implementation detail> — I'll hold that for BS-03 / BS-04 where we make architecture and tech-stack decisions. For now, let's stay on the product side: what does the feature look like to the user/actor? What are the rules, regardless of how we implement them?"

If the Maintainer is insistent ("but the implementation determines the behavior"), explain:

> "The product behavior is what we're documenting here. If 'we use a Postgres trigger' is the only way to achieve a specific behavior, the behavior is what goes in the ft-*.md — 'trigger condition X → outcome Y'. How we implement that is BS-04. Let's get the behavior captured first."

### 8.3 — When the Maintainer wants to invent features during BS-02

**Symptom:** Maintainer says "now that we're documenting this, I realize we also need <new feature that wasn't in BS-01>".

**Why it's a problem:** BS-02 is for documenting features the Maintainer already articulated. Inventing new features during BS-02 expands scope unboundedly. New features that emerge during BS-02 deserve discovery first, not direct spec.

**Response pattern:**

> "Interesting — you just surfaced <new feature>. Is this something you actually knew about and missed in BS-01, or is it crystallizing right now? If it's now, that's actually a sign we should pause BS-02 briefly, treat this as a mini-discovery (5-10 minutes), and then decide whether it warrants its own ft or lives inside an existing one. If it's something you knew about, let's add it to the inventory now."

The pattern: capture the moment of discovery, but don't dump it directly into a spec. Either fold it into BS-02 inventory deliberately (mini-discovery), defer to post-BS-02 as a PDR candidate, or merge it into an existing planned feature.

### 8.4 — When two features keep entangling

**Symptom:** during deepening of feature A, the Maintainer keeps referencing feature B's entities and behaviors, and vice versa. The two features can't be cleanly separated.

**Why it's a problem:** if two features can't be documented independently, they probably aren't two features — they're one feature documented in two files.

**Response pattern:**

> "I notice we keep crossing between A and B. Are these really separate features, or is this one feature we tried to split? Two options:
>
> 1. Merge them into one ft-<name>.md
> 2. Refactor where the boundary is — what specifically lives in A vs B, and what's the contract between them?
>
> Which feels right?"

If the Maintainer goes with merge, update the inventory. If they want them separate, force an explicit boundary: "what does A know about B, and what does B know about A?" The answer usually clarifies whether they're truly distinct.

### 8.5 — When the Maintainer answers "it depends" to a policy question

**Symptom:** the Bootstrapper asks "what's the rule when X happens?" and the Maintainer answers "it depends" without specifying on what.

**Why it's a problem:** "it depends" without articulation is vague. The policy is either undefined or context-sensitive in a way that needs to be made explicit.

**Response pattern:**

> "OK, 'it depends' — what does it depend on? List the conditions. If condition 1, then X; if condition 2, then Y. Be specific."

If the Maintainer can articulate the conditions, you have a real policy ("If supplier is in active ServiceArea AND has confirmed capability, contact via SLA window 1; otherwise, contact via SLA window 2"). If they can't, that's an Open question:

> "It sounds like this rule isn't fully formed yet. Let me capture as an Open question: 'When X happens, the response depends on factors we haven't yet enumerated. Need to define before <relevant phase>.' OK?"

### 8.6 — When the Maintainer can't name policies for a feature

**Symptom:** the Maintainer can describe the feature's purpose and actors but draws a blank on Policies. Multiple "I think the rule is..." with no commitment.

**Why it's a problem:** features without policies are under-specified. Implementation will surface policies as edge cases, often after rework.

**Response pattern:**

Use the "scenarios" approach. Instead of asking "what are the policies", walk through concrete scenarios:

> "OK, let me walk through scenarios. When Actor X does <specific thing>, what does the system do? What about when <specific edge>? What if <specific failure>?"

Each scenario produces a policy. Capture them one at a time. If after 4-5 scenarios the Maintainer still says "I don't know", the feature is genuinely emergent — document the Purpose and Actors, mark Policies as an Open question, and possibly downgrade from "create ft-*.md now" to "defer to PDR".

### 8.7 — When the inventory grows mid-conversation

**Symptom:** during deepening of a feature, the Maintainer realizes "actually I think this should be split into two ft files" or "we need another transversal we didn't talk about".

**Why it's a problem:** inventory changes mid-deepening cost time (need to re-order, re-budget). But blocking them is worse — the new structure may be correct.

**Response pattern:**

Accept the change, but make it explicit:

> "Adding <new ft> to the inventory. Quickly: where in the order does this go? Right after <X>? And does it change any feature we already deepened? If yes, we'll add a small revision pass to that file at consolidation."

Update the captured inventory in the chat. The Maintainer sees the inventory grow, which keeps both parties aligned.

### 8.8 — When the Maintainer is exhausted halfway through deepening

**Symptom:** after 4-5 features deepened, the Maintainer's answers get shorter, vaguer, less committal. Energy is depleted but more features remain.

**Why it's a problem:** continuing extracts thin specs that the Maintainer will revise later. Stopping leaves features unspec'd.

**Response pattern:**

Recognize and offer:

> "We've been at this 90 minutes and I notice your answers are getting shorter. Two options:
>
> 1. Pause now, resume tomorrow in this same chat. We have <X features> deepened so far; the remaining <Y> wait.
> 2. Push through but switch to lighter mode — for the remaining features, we just capture Purpose, Actors, and 1-2 Open questions each. The features get a minimal `ft-*.md` now and grow via PDR later when you have more energy or more product clarity.
>
> Which works?"

The Bootstrapper does not push exhausted Maintainers through full deepening. Thin honest docs are better than fabricated rich ones.

### 8.9 — When transversal models keep getting mentioned but not documented

**Symptom:** during deepening of feature A, billing comes up. During feature B, billing comes up. During feature C, billing comes up. But the Maintainer didn't put billing in the inventory.

**Why it's a problem:** without `ft-billing.md`, billing concerns get smeared across A, B, C. None of them owns billing; all of them have partial billing logic.

**Response pattern:**

Surface the pattern explicitly:

> "Billing has now come up in three features. I think it deserves its own ft-billing.md as a transversal model. Want to add it to the inventory? It'd be deepened after we finish the remaining features."

Same pattern for other transversals (auth, notifications, observability) when they accumulate mentions across multiple features.

### 8.10 — When the Maintainer wants to skip the Cross-feature audit (7.6)

**Symptom:** after deepening N features, the Maintainer wants to jump straight to consolidation. "I think we're good, let's just generate the files."

**Why it's a problem:** the audit catches inconsistencies the Maintainer hasn't noticed. Skipping it produces files that contradict each other, which surfaces during BS-03 (architecture) or implementation as expensive rework.

**Response pattern:**

Don't argue. Show the value concretely:

> "Let me do a quick scan first — 5 minutes. I want to check entity name consistency across the features we deepened. If everything aligns, we move to consolidation immediately. If something doesn't, we resolve it now while context is fresh."

Run the scan. Either you find nothing (great, move on, the Maintainer's intuition was right) or you find something (great, you just saved a downstream PDR). Either way, the time investment pays.

---

## 9. Pre-fill rules

The Bootstrapper does **not** write to files during 7.1 through 7.6. It anchors substantive content via capture-aloud (see 7.5) and consolidates **once** in 7.7, presenting all file contents for the Maintainer to apply.

This section defines:

- The capture-aloud pattern adapted to BS-02
- How content routes to each `ft-*.md` at consolidation
- The structure floor each `ft-*.md` follows, with notes on natural extensions

### 9.1 — The capture-aloud pattern (BS-02 variant)

Same fundamental pattern as BS-01 (see BS-01 Section 9.1), but the destination is a feature-specific file plus PRODUCT.md updates rather than a fixed dimension file.

**Format:**

> "Capturing for `ft-<feature>.md`: <what + structured anchor>. This goes into <section> at consolidation."

Or for PRODUCT.md updates:

> "Capturing: <entity name and one-line role>. This goes into PRODUCT.md Core entities at consolidation."

> "Capturing: <new domain term and definition>. This goes into PRODUCT.md Glossary at consolidation."

**Examples:**

- "Capturing for `ft-supplier-management.md`: ServiceArea is the demand-side entity. Each city the product serves has one. States: mapped → prospecting → active → paused. Transitions are operator-driven, manual. This goes into the Entities and Policies sections at consolidation."

- "Capturing for `ft-price-database.md`: PriceHistory is append-only. Cancellation sets `cancelled_at`. Validity is computed (`validity_end > now()` AND `cancelled_at IS NULL`), not stored as a separate field. This goes into Policies at consolidation."

- "Capturing for `ft-billing.md`: ReferralReward is earned by the buyer who suggested a supplier when that supplier joins and responds for the first time. Amount: fixed (config). This goes into Entities and Policies at consolidation."

**Frequency:** 3-10 captures per feature during deepening, calibrated to feature complexity. Mature features warrant more anchors; minimal features have fewer.

### 9.2 — What goes in each file at consolidation

This is the canonical routing. When the Bootstrapper consolidates in 7.7, content flows to these destinations.

#### `docs/PRODUCT.md`

Sections updated in BS-02:

| Section | What goes here |
|---|---|
| `Core entities` | High-level entity diagram (5-10 entities). ASCII relationships or short list. Each entity has exactly one owner ft-*.md. Per-feature detail lives in the owner file |
| `Document map` | Index of every pd-*.md and ft-*.md in the repo. One-line description per file |
| `Glossary` | Extended with domain terms surfaced during BS-02 (new entity names, status names, lifecycle terms not already in the BS-01 glossary) |

Sections NOT touched in BS-02:

- `How it works (one paragraph)` — already filled in BS-01; refined only if BS-02 surfaced something that contradicts it (rare)
- `Core actors` — already filled in BS-02-via-BS-01 (actors discovered during deepening were added to BS-01's section if they were missing; new actors from BS-02 also append)
- `Phase strategy` — BS-07
- `Open cross-doc questions` — populated as inconsistencies surface in 7.6
- `Cross-document inconsistencies` — same

#### `docs/product/ft-*.md` files

Each `ft-*.md` follows the structure at `docs/product/TEMPLATE.md` as a **floor**:

```
# <Feature title>

> **Status:** Draft | v1 | v2
> **Owner:** product

## Purpose

<One paragraph: what this feature is and why it exists>

## Actors

<Who interacts with this feature, with their roles>

## Vocabulary

<Domain-specific terms used in this feature>

## Entities

<Entities owned by this feature with their relationships and key fields>

## Policies

<Rules the feature enforces>

## Edge cases

<Unusual or boundary situations>

## Open questions

<What's unresolved about this feature>

## Related

<Cross-references to other ft-*.md and pd-*.md files>
```

The Bootstrapper extends these sections naturally based on feature content:

- **Status-driven entities** add a Lifecycle section with state transition diagram (ASCII), allowed/forbidden transitions table, per-state behavior notes.
- **Multi-actor features** add Per-actor workflows sections, one per principal actor with end-to-end flow.
- **Data assets** add an Entity overview section with ASCII relationship diagram before diving into per-entity sections.
- **Features with rich vocabulary** may have Vocabulary as a sub-table with Term | Definition columns.
- **Operational features** add a Triggers section listing what events create work for operators.

The structure grows from the floor; the Bootstrapper does not pad files with empty sections to make them look uniform.

### 9.3 — Cross-references in ft-*.md files

At consolidation, the Bootstrapper adds cross-references in the Related section of each `ft-*.md`:

- Other `ft-*.md` files this feature depends on or interacts with, with a one-line "why related"
- Relevant `pd-*.md` files that informed this feature
- `PRODUCT.md` for general orientation

Example Related section:

```
## Related

- `product/ft-buyer-management.md` — Buyer is the principal actor that interacts with this feature
- `product/ft-price-database.md` — RFQs trigger updates here; reuse logic depends on this
- `product/pd-workflows.md` — high-level workflow narrative for this domain
- `product/pd-mvp.md` — MVP scope decisions for this feature
- `PRODUCT.md` — overview and glossary
```

The Bootstrapper aims for accurate cross-references; tolerating false-positive references (linking to files that aren't actually related) reduces the usefulness of the Related section.

### 9.4 — Open questions, deferrals, and uncertainty in BS-02

Same pattern as BS-01 (see BS-01 Section 9.4). When the Maintainer is uncertain on something, the Bootstrapper anchors it as an open question instead of fabricating an answer.

**Open questions specific to BS-02 often look like:**

- "Threshold for X is unspecified — likely <range>, decide based on first month of data."
- "Workflow for Y when Z happens is TBD — operator handles manually in MVP."
- "Naming for entity W is provisional — current placeholder is <X>, may change."
- "Boundary between feature A and feature B for case C is ambiguous — currently both files claim it; resolve via PDR if it actually matters."

At consolidation, these go into the Open questions section of the relevant `ft-*.md`. Cross-cutting open questions (those that span multiple files) go into `PRODUCT.md` Open cross-doc questions.

### 9.5 — Decisions Captured for later steps

BS-02 surfaces tech, architecture, legal, and operational signals that affect BS-03/BS-04/BS-05. The Bootstrapper does NOT write these into ft-*.md files (they're not product specs) but holds them mentally for the Bridge to BS-03 (7.9).

Common signals during BS-02 deepening:

- **Storage needs**: feature implies file storage, blob storage, vector storage → BS-04
- **Search needs**: feature implies full-text search, vector similarity, semantic search → BS-04
- **External integrations**: feature requires third-party API (payments, geocoding, messaging) → BS-04
- **Async patterns**: feature requires background jobs, scheduled tasks, event-driven dispatch → BS-03
- **State machine patterns**: feature has complex lifecycle that maps to FSM library → BS-03 / BS-04
- **Compliance signals**: feature handles regulated data (financial, health, EU users) → BS-05, add LP entry

The Bootstrapper anchors these mentally at the moment they surface, with a brief acknowledgment to the Maintainer:

> "Capturing as Decision Captured for BS-04: <feature> implies vector search. We'll formalize the DB choice in BS-04."

These do NOT need anchors in the chat or entries in ft-*.md — they're transient state until BS-03+ confirms them.

---

## 10. Required outcomes (checklist for closing BS-02)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step, per BOOTSTRAPPER section 9 sub-step 7 ("Verify required outcomes").

### 10.1 — Outcomes that must be true to close BS-02

#### Feature inventory

- [ ] The list of `ft-*.md` files to create is agreed in writing (captured in chat at end of sub-block 7.2)
- [ ] Inventory has 4-15 entries calibrated to product complexity (significant deviations from this range surfaced and justified during 7.2)
- [ ] Features explicitly deferred to PDR are noted (in chat and in relevant `pd-*.md` Open questions)

#### Per-feature files exist with substantive content

- [ ] Every file in the inventory exists in `docs/product/` after consolidation
- [ ] Every file has at minimum: Purpose section with substantive content (not a placeholder), Actors section (or explicit note that the feature has no actors specific to it), an Entities section sketch (or note that the feature owns no entities), at least Open questions section if Policies/Edge cases are sparse
- [ ] No file is an empty stub

#### PRODUCT.md updates

- [ ] `PRODUCT.md` "Core entities" filled with high-level entity diagram (5-10 entities)
- [ ] `PRODUCT.md` "Document map" lists every pd-*.md and ft-*.md that exists with one-line descriptions
- [ ] `PRODUCT.md` "Glossary" extended with new domain terms surfaced in BS-02

#### Anchor questions answered

- [ ] **Anchor 1 (Feature inventory list)** captured in chat, agreed by Maintainer
- [ ] **Anchor 2 (Core entities relationships)** documented in PRODUCT.md Core entities
- [ ] **Anchor 3 (Transversal models)** identified — either documented as ft-*.md files in the inventory, or explicitly noted as absent for this product
- [ ] **Anchor 4 (Policies and Edge cases per feature)** each ft-*.md has at least one Policy and one Edge case captured, OR an Open questions entry flagging the gap

#### Cross-feature consistency

- [ ] Cross-feature audit (7.6) was conducted
- [ ] Identified inconsistencies are either resolved at consolidation OR noted as cross-doc open questions in PRODUCT.md

#### Decisions Captured for later steps

- [ ] BS-02 contributed at least 1-2 additional Decisions Captured beyond what BS-01 produced (typical: storage need, search need, external integration, async pattern, compliance signal). The Bootstrapper holds these in mental state for BS-03/BS-04/BS-05

### 10.2 — When a required outcome is unmet

Same options as BS-01 (Section 10.2):

**Option A — Cover it now.** Pick up the conversation thread on the gap. Most gaps in BS-02 take 5-20 minutes to fill (e.g., one feature missing Policies; one transversal forgotten in inventory).

**Option B — Defer it with structure.** Document the gap explicitly:
- Missing feature content → Open questions entry in the relevant `ft-*.md`
- Missing PRODUCT.md section → Open cross-doc question in PRODUCT.md
- Genuinely deferred feature → noted in `pd-*.md` Open questions for future PDR

The Bootstrapper recommends Option A when the unmet outcome is load-bearing for BS-03 (e.g., Core entities diagram missing — BS-03 architecture can't proceed). Option B when the outcome is genuinely premature to resolve (e.g., specific Policies for a feature that's still maturing).

### 10.3 — What's explicitly NOT required to close BS-02

To prevent perfectionism:

- All `ft-*.md` files having uniform depth — sparse but documented is acceptable for less-mature features
- Engineering details (table schemas, API shapes, code structure) — those are not BS-02's concern
- Performance characteristics, scale assumptions — BS-03 / BS-04
- UI/UX specifications — typically not in `ft-*.md` at all; live in design docs if the project has them
- Roadmap and phase assignment per feature — BS-07
- Production runbooks, monitoring, alerting — emerge during implementation or BS-04
- Test strategies per feature — BS-05
- Detailed acceptance criteria per feature — emerge during task planning

The Bootstrapper resists the temptation to push BS-02 into BS-03+ territory. Per-feature product specs are what BS-02 captures; everything downstream lives in its own step.

---

## 11. Bridge to BS-03

When the Required outcomes checklist is complete (or consciously deferred with notes), the Bootstrapper transitions to BS-03 with the following sequence:

### 11.1 — Recap

Three or four sentences capturing what BS-02 accomplished:

> "BS-02 documented the product as <N> ft-*.md files: <list a few major ones>. PRODUCT.md now has Core entities (<entity count>), Document map (all <pd + ft count> files indexed), and Glossary (<term count> terms). Transversal models: <list, or 'none for this product'>. Open cross-doc questions remain around <list 2-3 main ones> — to be resolved via PDR if they prove blocking."

### 11.2 — Confirm Decisions Captured for later steps

Recap all Decisions Captured (BS-01 + BS-02 combined). The Maintainer confirms they're aware these are coming back:

> "Decisions Captured carried into later steps:
>
> From BS-01:
> - **Language**: <X>, formalized in BS-04
> - **LLM provider**: <Y>, BS-04
> - **Channel**: <Z>, BS-04
>
> Added in BS-02:
> - **Storage**: <feature> implies <storage type>, BS-04
> - **Search**: <feature> implies <search pattern>, BS-04
> - **Async**: <feature> implies <pattern>, BS-03
> - **Compliance**: <signal>, BS-05 and `legal/POLICY_CONSIDERATIONS.md`
>
> Any of these you want to revisit, or carry forward?"

### 11.3 — Preview BS-03

Two sentences:

> "BS-03 takes the features we just documented and decides the architectural shape: layering, dependency direction, async patterns, persistence strategy. By the end of BS-03, `docs/engineering/ARCHITECTURE.md` is filled and the structural decisions that BS-04 onwards build on are in place."

### 11.4 — Commit BS-02 before continuing or pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

Before moving to BS-03 (or pausing), the Maintainer commits the work BS-02 produced. The file list is dynamic because BS-02's inventory is project-specific. The Bootstrapper builds the exact `git add` line based on what was created:

```
git add docs/PRODUCT.md \
        docs/product/ft-<feature-1>.md \
        docs/product/ft-<feature-2>.md \
        # ... one line per ft-*.md created during BS-02

git commit -S -m "docs(bootstrap): complete BS-02 — product document"
git push origin main
```

The Bootstrapper presents the actual file list based on the inventory agreed in sub-block 7.2 and the files actually created. The Maintainer adjusts if any file was renamed or merged.

After the commit lands, proceed to 11.5 (continue) or 11.6 (pause).

### 11.5 — Continue in the same chat

Template:

> "BS-03 typically takes 45 to 90 minutes. We continue in this same chat — the context from BS-01 and BS-02 needs to carry through. If you need a pause, that's fine. Ready to start BS-03, or want a break?"

If the Maintainer pauses:

- The `pd-*.md`, `ft-*.md`, and `PRODUCT.md` are already in the repo (applied during 7.7).
- When the Maintainer returns, the Bootstrapper opens BS-03 by reading `docs/PRODUCT.md` and `docs/product/ft-*.md` to refresh state, then proceeds.
- The Bootstrapper does NOT re-execute BS-02. The work is preserved in the docs and chat history.

### 11.6 — If the Maintainer wants to stop entirely

Sometimes a Maintainer hits BS-02 fatigue and wants to stop the bootstrap entirely, returning another day. Acceptable but needs structure:

> "Understood. A few things before we pause:
>
> 1. Everything is saved in the docs — PRODUCT.md, the seven pd-*.md files, and the <N> ft-*.md files. They're not lost.
> 2. When you come back, **stay in this same chat session**. Starting fresh means rebuilding context, which loses the Decisions Captured I'm holding for BS-03/BS-04/BS-05.
> 3. When you resume, say 'resuming bootstrap' and I'll re-orient.
>
> Take the break. The work is preserved."

---

## 12. Notes for the Bootstrapper LLM running BS-02

These are operational reminders specific to BS-02, on top of universal BOOTSTRAPPER guidance and BS-01's Section 12.

### 12.1 — Resist scope expansion

The Maintainer may want to invent features during BS-02 ("now that we're documenting this, I realize we also need <X>"). The Bootstrapper recognizes this and handles per forcing function 8.3 — either fold deliberately or defer to post-BS-02.

The same applies to depth: the Maintainer may want to spec a feature exhaustively (every field, every edge case, every error path). The Bootstrapper recognizes diminishing returns and caps depth at "substantive enough to inform BS-03 and to be useful as reference during implementation". Hyper-detailed specs that the Maintainer can't actually back up with conviction are noise.

### 12.2 — The Maintainer's reality drives the inventory, not the categories

Section 5 lists 5 categories of files (Principal actors, Core data assets, Principal workflows, Transversal models, Operational functions). The Bootstrapper holds these as a probing map, not as a checklist that must be filled for every product.

A simple productivity tool may have 4 files all in Category 1 (actors and their workflows). A complex marketplace may have 12 files across all 5 categories. A pure consumer app may have 6 files across Categories 1, 3, 4. The category distribution is a function of the product, not a target the Bootstrapper enforces.

### 12.3 — Maturity calibration, not uniform depth

The Bootstrapper calibrates depth per feature based on the Maintainer's actual knowledge. Mature features get rich files; less-mature features get thin files plus Open questions. The Bootstrapper does NOT inflate thin features to match the depth of mature ones, and does NOT shrink rich features to match the depth of thin ones.

Uniformity is a smell. Different features have different maturity at any given time, and the docs should reflect that honestly.

### 12.4 — Cross-feature consistency is harder than per-feature spec

The hardest part of BS-02 is not deepening any single feature — it's keeping the features consistent with each other. Same entity name, same vocabulary, same actors playing the same roles, same policies for analogous situations.

The Bootstrapper invests in this via:

- Capture-aloud anchors (visible state in the chat the Bootstrapper re-scans)
- Cross-feature audit sub-block (7.6) explicitly dedicated to consistency checks
- Cross-doc consistency check during consolidation (7.7)

If inconsistencies slip through anyway, they get caught later (BS-03 surfaces them; implementation surfaces them). But catching them now is cheaper.

### 12.5 — Pacing

BS-02 typically takes 60-180 minutes:

- **First 20-30 minutes**: opening, feature inventory, ordering. Substantive content is mental, not yet in the chat.
- **Middle 60-90 minutes**: per-feature deepening across N features. Most of BS-02's content captured here.
- **Final 30-45 minutes**: cross-feature audit, consolidation in batches, application, audit, bridge.

If the conversation runs long (180+ minutes and still in deepening), check energy:

> "We've been at this for 180 minutes. We have <remaining count> features still to deepen. Want to push through, switch to lighter mode for the remaining (Purpose + Open questions only, defer rich content to PDR), or pause and resume tomorrow?"

The Maintainer decides. The Bootstrapper does not push exhaustion.

### 12.6 — Resist completionism per feature

Each `ft-*.md` doesn't need to be perfect. The feature spec is captured progressively — BS-03 may surface gaps (which become PDRs), implementation may surface gaps (which become PDRs), production may surface gaps (which become PDRs). Pushing each file to "complete in every detail" delays the rest of the bootstrap and produces diminishing returns.

The bar is: **the feature is documented well enough that BS-03 can decide its architectural placement and that an engineer can begin implementation without going back to the Maintainer for every decision**. That's it.

When the Bootstrapper finds itself reaching for more depth on a feature that's "good enough", it stops. The remaining features deserve attention.

### 12.7 — Resist completionism on inventory

The Bootstrapper does NOT push to maximize the inventory size. 4-15 files is the typical range; landing at 5 is fine if the product genuinely has 5 features. Padding the inventory with marginal candidates ("we could also have ft-X.md, ft-Y.md, ft-Z.md") produces thin files that add noise.

Better to have 5 substantive files than 12 thin ones. PDRs will create new ft-*.md files as the project evolves anyway — BS-02 is the baseline, not the complete catalog.

### 12.8 — Watch for "feature" that's actually engineering

The Maintainer may propose features that are really engineering concerns disguised as product features: "We need a `ft-caching.md`", "We need a `ft-rate-limiting.md`", "We need a `ft-deployment.md`".

The Bootstrapper applies forcing function 8.1 — if there's no clear product surface (no actor that experiences it directly), it's not a product feature. It's infrastructure or DevOps, lives in `docs/engineering/*` if it needs documentation at all.

The exception: if "caching" or "rate-limiting" has a direct product surface (e.g., the buyer sees a "your search results are cached for 10 minutes" message; the API consumer sees rate-limit headers), then yes, it can be a product feature. But the smell test is "would a product manager and a designer talk about this feature in a planning meeting" — if no, it's not product, it's engineering.

### 12.9 — The TASK-PLANNER does NOT create ft-*.md files directly

When a new `ft-*.md` file needs to exist after BS-02 (because the product evolved, a new feature emerged, or implementation surfaced an unrecognized need), the creation path is **always** PDR-mediated:

1. **A PDR is created first.** The PDR documents the product decision (what's being added, why, what alternatives were rejected). Its "Affected documents" section may have a "New docs to create" sub-section listing the new `ft-*.md` file(s) the decision introduces.

2. **The PDR-PROPAGATOR workflow propagates the consequences.** When invoked (manually or via a TSK explicitly dedicated to propagation), it edits existing affected files AND creates new files per the PDR's specification.

3. **Implementation tasks consume the new `ft-*.md`** as source of truth, just like the original BS-02 outputs.

The TASK-PLANNER's role in this chain:

- TASK-PLANNER **can** generate execution prompts that result in PDR creation (when the task in ROADMAP says "create PDR XXXX")
- TASK-PLANNER **can** generate execution prompts that invoke PDR-PROPAGATOR (when the task says "propagate PDR XXXX")
- TASK-PLANNER **does NOT** generate execution prompts for implementation tasks that include "create `ft-Z.md`" as a side-effect step. If an implementation task seems to require a new `ft-*.md`, TASK-PLANNER stops, surfaces the gap, and asks the Maintainer to first author a PDR that triggers the creation.

The ROADMAP-PLANNER's role:

- ROADMAP-PLANNER organizes the work so that PDR creation precedes implementation when a new feature is being added
- The canonical pattern for "new feature" in ROADMAP is a sequence:
  1. TSK — Create PDR documenting the decision
  2. TSK — Propagate the PDR (creates/edits docs including any new `ft-*.md`)
  3. TSK(s) — Implementation work consuming the new docs

The Maintainer's role:

- When a product change is needed mid-development, the Maintainer recognizes the need and either:
  - Asks ROADMAP-PLANNER to insert the PDR creation + propagation tasks into the roadmap, OR
  - Authors the PDR directly (without ROADMAP-PLANNER intermediation, if the change is small) and runs PDR-PROPAGATOR

**Why this discipline matters:**

If `ft-*.md` files could be created as side effects of implementation tasks, several things break:
- The PDR record (the durable "why we decided this") gets bypassed, losing the rationale
- Multiple implementation tasks could create conflicting versions of the same feature spec
- The product layer becomes downstream of code instead of upstream — tasks define product instead of product defining tasks
- The Bootstrapper LLM running future steps loses the consistency it relied on (every `ft-*.md` has a clear creation provenance: BS-02 or a specific PDR)

This pattern is unique to ai-engineering-bootstrap. The reference combar project did not have this formalized — feature docs in combar were authored by the Maintainer ad-hoc, outside execution prompts entirely, with no PDR mechanism connecting decisions to doc edits. The ai-engineering-bootstrap template formalizes the path: BS-02 establishes the baseline, PDR + PDR-PROPAGATOR evolve it.


# BS-03 — Architecture

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-03 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the architecture review conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern), this file for the specific content of BS-03, and the output of BS-01 + BS-02 (`docs/PRODUCT.md` + `docs/product/pd-*.md` + `docs/product/ft-*.md`) as input to this step.

---

## 1. Goal

Review the architectural shape of the project against the product documented in BS-01 and BS-02. The template ships with opinionated defaults (layered architecture, port placement, repository pattern, Unit of Work with caller-owned transactions, cross-cutting decisions for auth/observability/idempotency/rate limiting). BS-03 walks each section of `docs/engineering/ARCHITECTURE.md` and `docs/engineering/STRUCTURE.md` with the Maintainer to:

- Confirm the defaults still apply to this specific product
- Adjust the defaults where they conflict with the product
- Fill the BOOTSTRAP markers that capture project-specific content (component view, main flows, data view, integrations, quality attributes, repository layout)

The goal is **not** to redesign architecture from scratch. The template's defaults represent battle-tested choices for AI-first backend projects. The Maintainer engages with each default, accepts or adjusts, and the result is two docs that reflect both the template's structural intent and the project's specific reality.

By the end of BS-03, a competent engineer joining the project could read `ARCHITECTURE.md` and `STRUCTURE.md` and know:

- Where any new file goes (which layer, which folder)
- Which layer can import from which other layer
- How requests flow through the system end-to-end
- Where business truth lives vs what's rebuildable
- What the system optimizes for (latency, scale, cost discipline)

---

## 2. What this step produces

### Documents updated

- **`docs/engineering/ARCHITECTURE.md`** — every BOOTSTRAP marker filled with content specific to the project. Fixed content sections (Internal layers, Cross-cutting decisions, What this document explicitly does not cover) reviewed and adjusted where they conflict with the product.

- **`docs/engineering/STRUCTURE.md`** — Repository layout BOOTSTRAP marker filled with the concrete folder tree the project will use. Principles section reviewed. The `.ai/` folder and `docs/` folder reference trees confirmed or updated.

### Documents potentially created

- **`docs/engineering/adr/0001-<slug>.md`** through `NNNN-<slug>.md` — Architecture Decision Records for any architecturally significant choice that surfaces during BS-03. Common examples: choice of architectural style (Clean vs vertical slice vs other), departure from default port placement, idempotency mechanism, observability stack. The Bootstrapper does not pre-decide which ADRs are needed — they emerge from the conversation and are surfaced for the Maintainer to confirm.

  ADRs use `docs/engineering/adr/TEMPLATE.md` as a floor.

### What is NOT produced in BS-03

- Concrete library/framework choices (FastAPI vs Litestar, SQLAlchemy vs Django ORM, Anthropic vs OpenAI) — those are BS-04 (Tools)
- Code style conventions, naming patterns, async rules — those are BS-05 (Conventions)
- Security policy detail beyond architectural level — auth/observability are touched at the structural level here; specific algorithms (JWT RS256 vs HS256, token lifetime values) land in BS-05
- Test strategy (pyramid shape, marker conventions, fake adapter patterns) — BS-05
- Deployment topology, CI/CD specifics — emerge later during implementation or BS-04 if relevant
- The actual code skeleton (any `app/` files) — bootstrap doesn't write code, only docs

### Critical premise — BS-03 reuses Decisions Captured

By BS-03, the Maintainer has likely already committed to some technical choices during BS-01 and BS-02 (e.g., "the language is Python", "we're using Telegram as the channel", "we need vector search"). These are in the Bootstrapper's Decisions Captured mental register.

The Bootstrapper **uses these decisions when naming components in the diagram and flows**. The system is "FastAPI app" not "API gateway" if FastAPI was captured. The queue is "Arq + Redis" if Arq + Redis was captured. If nothing was captured for a slot (e.g., the Maintainer hasn't decided on async orchestration yet), the Bootstrapper uses the abstract name ("queue", "background worker") and notes it as deferred to BS-04.

This is per the BS-03 calibration: tech stack as **input where already decided, output where still open**.

---

## 3. Exit criteria

BS-03 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

- [ ] **Section 1 of ARCHITECTURE.md (What this project is)** is filled with one-paragraph description, core asset, actors table, and v1 constraints. Content sourced from PRODUCT.md and BS-01/BS-02 docs.

- [ ] **Section 2 of ARCHITECTURE.md (High-level component view)** is filled with a Mermaid diagram showing major components and a "Component responsibilities" sub-section listing each component with its location and ownership.

- [ ] **Section 3 of ARCHITECTURE.md (Main flows)** is filled with at least the critical user flow (the main thing the product does end-to-end) plus any async or recurring flow that defines product behavior. Each flow uses a numbered list naming the components that participate.

- [ ] **Section 4 of ARCHITECTURE.md (Internal layers)** has been reviewed against the product. Either the default Clean Architecture layering is confirmed, or specific adjustments are documented (carve-outs, alternative styles, modified port placement rules).

- [ ] **Section 5 of ARCHITECTURE.md (Cross-cutting decisions)** has been reviewed against the product. Each of the six sub-sections (auth/authz, observability, error handling, idempotency, rate limiting, development environment) is confirmed as applicable or adjusted with specific rationale.

- [ ] **Section 6 of ARCHITECTURE.md (Data architectural view)** has the BOOTSTRAP marker filled with the concrete stores the project will use and which is the source of truth for each kind of data.

- [ ] **Section 7 of ARCHITECTURE.md (Integrations)** has the BOOTSTRAP marker filled with the external integrations the project will use, organized by phase and the port/adapter pair that isolates each.

- [ ] **Section 8 of ARCHITECTURE.md (Quality attributes and trade-offs)** is filled with concrete latency targets, scale targets, cost discipline notes, and accepted trade-offs.

- [ ] **Section 10 of ARCHITECTURE.md (Open questions)** has any unresolved structural questions captured for future ADR.

- [ ] **STRUCTURE.md Principles section** has been reviewed. Either the defaults are confirmed or specific adjustments are documented.

- [ ] **STRUCTURE.md Repository layout BOOTSTRAP marker** is filled with a concrete folder tree appropriate for the project's chosen shape.

- [ ] **STRUCTURE.md `.ai/` folder reference tree** has been confirmed as matching the actual `.ai/` structure.

- [ ] **STRUCTURE.md `docs/` folder reference tree** has been confirmed as matching the actual `docs/` structure.

### Soft requirements (worth pushing for but not blockers)

- [ ] ADRs created for architecturally significant choices made or confirmed during BS-03 (architectural style if non-default, idempotency mechanism if non-trivial, observability stack)

- [ ] Cross-references between ARCHITECTURE.md and `docs/product/ft-*.md` are accurate (e.g., features mentioned in flows match the ft-*.md files that own them)

### What does NOT block exit

- Specific library versions, framework names, provider names beyond what was already decided in BS-01/BS-02 — those land in BS-04
- Detailed schema design — `DATA_MODEL.md` is a future doc, not BS-03's concern
- Detailed endpoint design — `API_CONVENTIONS.md` is a future doc
- Detailed prompt management — `AI_INTEGRATION.md` is a future doc
- Test strategy specifics — BS-05

The Bootstrapper does not advance to BS-04 silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before moving on, I want to address X. We haven't yet captured the data view section despite mentioning a primary database and pgvector during the conversation."

---

## 4. Anchor questions

These are the four questions that **must be answered with confidence** before BS-03 closes. They are not asked in order — they emerge through the section-by-section walk (see Section 7 — Conversation flow). The Bootstrapper keeps mental track of which ones are still open, and surfaces any that remain unanswered before declaring the step complete.

### Anchor 1 — What is the high-level component shape of the system?

This becomes the **Component view diagram** in ARCHITECTURE.md Section 2. The Maintainer must be able to point at each major component and say what it owns. Components in scope for v1 include (typical):

- One or more entry points (HTTP API, webhook receiver, CLI, scheduled trigger)
- One or more workers or background processes (if the product has async work)
- The primary database
- Any derived stores (search index, cache, queue, vector store)
- External integrations the system depends on (LLM provider, payments, messaging, etc.)
- Static asset storage (if the product handles files)

If the Maintainer cannot place a major component or argues that "everything is just the API", that may be true for very simple products but is worth probing. Async work often exists implicitly (a retry on a failed webhook, a delayed email, a background sync) and gets accidentally pushed into the request lifecycle when not surfaced.

### Anchor 2 — What is the canonical main flow end-to-end?

Every product has at least one **main flow** — the core thing the product does that justifies its existence. This becomes ARCHITECTURE.md Section 3 (Main flows). The Maintainer must be able to walk through it step-by-step, naming each component that participates.

The flow should answer:
- What event triggers it (user action, scheduled job, external event)?
- What components touch the data on the way through?
- Where are decisions made (which component owns each policy)?
- Where does it end (response to user, written to DB, sent to third party)?

If the Maintainer struggles to articulate the main flow end-to-end, the product spec from BS-02 is too abstract — surface this as a gap. Possibly route back to the relevant ft-*.md to clarify.

### Anchor 3 — Where does business truth live?

This becomes ARCHITECTURE.md Section 6 (Data architectural view). The Maintainer must be able to point at each data store and say whether it is source-of-truth or rebuildable.

The principle is fixed in the template (anything rebuildable does not own data; vector indexes, full-text indexes, caches can be wiped and regenerated; the primary store and binary object storage are the only places where business truth lives). BS-03 applies this principle to the project's specific stores.

Probe: "If I wiped your Redis cache right now, would the system recover by rebuilding from the primary database? If I wiped your pgvector embeddings, would the system rebuild from canonical descriptions? What can't be wiped without losing real business data?"

### Anchor 4 — What does the system optimize for?

This becomes ARCHITECTURE.md Section 8 (Quality attributes and trade-offs). The Maintainer must commit to:

- **Latency targets** — at minimum, the p95 budget for the main flow's user-visible response. Other budgets (background work, recurring jobs) as relevant.
- **Scale targets** — expected volume for a defined horizon (6 or 12 months). Order of magnitude, not exact numbers.
- **Cost discipline** — whether cost-per-operation is tracked (e.g., LLM cost per task), whether infrastructure has a budget ceiling.
- **Accepted trade-offs** — explicit choices where alternatives were considered (monolith vs microservices, single-tenant vs multi-tenant, single DB vs polyglot persistence).

The forcing function here is **specificity**. "We want it fast" is not a target — "p95 < 500ms for the webhook ack" is. The Bootstrapper pushes for numbers, not adjectives.

---

## 5. How BS-03 differs from BS-01 and BS-02

BS-01 (Product discovery) and BS-02 (Product document) both followed a **capture-aloud + single consolidation** pattern: substantive content was anchored in the chat during 7.1 through 7.6, and a single consolidation moment in 7.7 produced all the file content in batches for the Maintainer to apply at once.

**BS-03 uses a different persistence pattern: section-by-section, with immediate application.**

### Why section-by-section in BS-03

BS-03 touches **two files** (ARCHITECTURE.md + STRUCTURE.md) plus possibly a few ADRs. Compared to BS-01 (8 files) and BS-02 (5-16 files), the volume is smaller. But each section of ARCHITECTURE.md is **conceptually independent and content-dense**:

- Section 4 (Internal layers) is a self-contained set of rules
- Section 5 (Cross-cutting decisions) has five independent sub-decisions
- Section 6 (Data view) is a stand-alone table
- Section 8 (Quality attributes) has four independent commitments

Each section deserves focused review. Stacking 8+ sections into a single consolidation makes the Maintainer's review shallow — they skim instead of engage. Section-by-section forces deliberate engagement with each one.

### The pattern in practice

For each section of ARCHITECTURE.md (and then STRUCTURE.md) in order:

1. **Present what's there.** Bootstrapper summarizes the existing content: fixed text (with key principles called out) and any BOOTSTRAP markers awaiting content.

2. **Check conflicts.** Bootstrapper compares the existing content with what was captured in PRODUCT.md, pd-*.md, ft-*.md. Surface any tension explicitly.

3. **Discuss adjustments and markers.** Conversation flows naturally between adjusting defaults and filling markers. Use forcing functions (A/B/C options with recommendation, listing implications, hidden complexity).

4. **Present the final section content.** Bootstrapper composes the final content for that section in a fenced markdown block and presents it.

5. **Maintainer applies immediately.** The Maintainer writes the change to the actual file. Confirms back in chat.

6. **Move to the next section.** Bootstrapper does not start the next section until the previous one is confirmed applied. This preserves coherence: section N+1's decisions may reference what was just decided in section N.

### What does NOT change

- Capture-aloud still happens within each section for the duration of the discussion (so the Maintainer sees the substantive content emerging before the final block). Capture-aloud anchors short-lived state across the section's discussion; the section block is the durable artifact.
- The Universal Step Pattern (BOOTSTRAPPER section 9) still applies — recap, load context, present, identify conflicts, discuss, fill, verify, bridge. BS-03's section-by-section flow is how those 8 sub-steps materialize when the work is structurally periodic.
- The forcing functions (BOOTSTRAPPER section 10) still apply in every section.
- Decisions Captured still accumulate (any tech-stack signals that emerge during BS-03 get carried to BS-04).

### What this requires from the Maintainer

The Maintainer needs to be **at the keyboard** during BS-03, not just in the chat. They will apply 10+ small edits (one per section), in sequence, throughout the step. The Bootstrapper announces this in the opening (sub-block 7.1):

> "BS-03 works a bit differently than BS-01/BS-02. Instead of capturing everything and applying it at the end, we go section-by-section: I show you what's currently in the section, we discuss what needs to change (adjustments to defaults + filling any markers), I present the final content for the section, you apply it to ARCHITECTURE.md (or STRUCTURE.md), and we move on. Plan for ~10-15 small edits across this step. We can pause between sections if you need."

If the Maintainer can't apply edits during the conversation (away from keyboard, in a meeting, etc.), they should pause BS-03 and resume when they can — section-by-section without immediate application breaks the pattern.

---

## 6. Coverage strategy

Unlike BS-01 (fixed 7-dimension checklist) and BS-02 (dynamic inventory built in 7.2), BS-03's coverage is **fixed by the structure of ARCHITECTURE.md and STRUCTURE.md**. The Bootstrapper walks the sections in a defined order, doing the appropriate work at each.

The order matters because some sections inform later ones. The Bootstrapper does NOT rearrange this order unless the Maintainer explicitly requests it.

### Walk order

**ARCHITECTURE.md:**

| Order | Section | Type | What BS-03 does there |
|---|---|---|---|
| 1 | §1 What this project is | Marker | Fill from PRODUCT.md and BS-01/BS-02. Quick — the content is downstream of work already done. |
| 2 | §4 Internal layers | Fixed | Review against product. Confirm or adjust the layering rules. |
| 3 | §5 Cross-cutting decisions | Fixed | Review the five sub-sections against product. Confirm or adjust. |
| 4 | §2 High-level component view | Marker | Build the Mermaid diagram + Component responsibilities. Requires §1, §4, §5 to be settled (components are the materialization of the layering + cross-cutting choices in the context of the product). |
| 5 | §3 Main flows | Marker | Walk the critical flows. Requires §2 (components must be named before flows can be drawn through them). |
| 6 | §6 Data architectural view | Marker (with fixed lead-in) | Fill the table of stores. Requires §2 and §3 to be settled (we know what data lives where based on what components touch what). |
| 7 | §7 Integrations | Marker | Fill the integrations table. Requires §6 (some integrations are stores). |
| 8 | §8 Quality attributes and trade-offs | Marker | Latency, scale, cost, accepted trade-offs. Independent of earlier sections but worth doing late so the Maintainer has full architectural context to set realistic targets. |
| 9 | §9 What this document does not cover | Fixed | Quick review. Usually no changes. |
| 10 | §10 Open questions | Marker | Capture anything unresolved. Last because items accumulate through the walk. |

**STRUCTURE.md:**

| Order | Section | Type | What BS-03 does there |
|---|---|---|---|
| 11 | Principles | Fixed | Review against product. Quick — defaults rarely change. |
| 12 | Repository layout | Marker | Fill with the concrete folder tree. Requires ARCHITECTURE.md §4 to be settled (layers map to folders). |
| 13 | `.ai/` folder | Reference tree | Confirm or update. Static — the `.ai/` structure is project-independent. |
| 14 | `docs/` folder | Reference tree | Confirm or update. Static — the `docs/` structure is project-independent. |
| 15 | Naming conventions section | Fixed | Quick review. Defers to CODE_STYLE.md (BS-05) for language-specific detail. |
| 16 | When the structure must change | Fixed | Quick review. Usually no changes. |

This order produces 16 section visits. Some are quick (§9 What this doc doesn't cover, Naming conventions) — under a minute. Others are substantive (§2 Component view, §3 Main flows, Repository layout) — 10-15 minutes each. Total BS-03 time: 60-90 minutes typical.

### What's constant within each section visit

Regardless of section type (Fixed vs Marker), the Bootstrapper applies the same six steps per section (see Section 5 "The pattern in practice"):

1. Present what's there
2. Check conflicts with product
3. Discuss adjustments and markers
4. Present the final section content
5. Maintainer applies
6. Confirm applied, move to next

The depth of step 3 varies by section type. Fixed sections often have minimal discussion (defaults stand). Marker sections have substantive discussion (the marker has to be filled with project-specific content).

### When the Maintainer wants to deviate from this order

Acceptable on request, but the Bootstrapper flags the cost:

> "We can visit §6 Data view before §2 Component view if you prefer, but normally I do components first because data view references what each component owns. If we flip the order, we might revisit §6 after §2 to adjust. OK with that?"

The Maintainer's call. Some Maintainers prefer to see the data architecture first because data is what they think about most — that's fine, just be aware of the ripple cost.

---

## 7. Conversation flow

The BS-03 conversation has **three macro-phases**: opening (7.1), section-by-section walk (7.2), and bridge to BS-04 (7.3). Compared to BS-01 and BS-02, BS-03 has fewer named sub-blocks because the bulk of the step is one repeated pattern (the section walk).

### 7.1 — Opening, Decisions Captured recap, pattern announcement (10-15 minutes)

**Purpose:** Re-establish context after BS-02. Recap Decisions Captured. Explain the section-by-section pattern. Get the Maintainer ready to apply edits during the conversation.

**Technique:**

1. **Recap BS-01 + BS-02 output briefly.** Two or three sentences:

   > "BS-02 closed with <N> ft-*.md files documented, PRODUCT.md complete, core entities diagram in place. Now in BS-03 we look at the architectural shape: how the components fit together, where business truth lives, how data flows, what the system optimizes for. The result is `ARCHITECTURE.md` and `STRUCTURE.md` reflecting both the template's structural opinions and the specific shape of your product."

2. **Recap Decisions Captured relevant to architecture.** List explicitly:

   > "Architectural decisions surfaced in earlier steps that I'm carrying into BS-03:
   >
   > - Language: <X> (BS-01)
   > - Primary channel: <Y> (BS-01/BS-02)
   > - Database family: <Z> (BS-02)
   > - <Anything else captured>
   >
   > These shape how I'll name components in the diagram. Confirm or revise any of them?"

   The Maintainer either confirms or adjusts. Adjustments are recorded.

3. **Announce the section-by-section pattern.** Match the wording from Section 5 of this BS-03 file:

   > "BS-03 works a bit differently than BS-01/BS-02. Instead of capturing everything and applying it at the end, we go section-by-section: I show you what's currently in the section, we discuss what needs to change (adjustments to defaults + filling any markers), I present the final content for the section, you apply it to ARCHITECTURE.md (or STRUCTURE.md), and we move on. Plan for ~10-15 small edits across this step. We can pause between sections if you need."

4. **Confirm Maintainer is at the keyboard.** Cannot section-by-section if the Maintainer can't apply.

   > "Ready to apply edits as we go? If you can't right now (in a meeting, away from desk), we can pause and resume."

5. **Preview the walk order.** Brief — names the major sections, not every minor one:

   > "We'll go through ARCHITECTURE.md in this order: §1 What this project is, §4 Internal layers, §5 Cross-cutting, §2 Component view, §3 Main flows, §6 Data view, §7 Integrations, §8 Quality attributes, §10 Open questions. Then STRUCTURE.md: Principles, Repository layout, and a quick confirmation of the static reference trees. Total around 60-90 minutes."

**Signal to move on:** the Maintainer is oriented, Decisions Captured are confirmed, the pattern is understood, the Maintainer is at the keyboard. Start the section walk.

### 7.2 — Section-by-section walk (variable, ~50-75 minutes total)

**Purpose:** Walk ARCHITECTURE.md and STRUCTURE.md in the order defined in Section 6. For each section, apply the six-step pattern (present, check conflicts, discuss, present final, apply, confirm).

This sub-block is not one conversation — it's 16 micro-conversations chained together. Each section visit has the same scaffold; what varies is the substance.

**Generic per-section pattern (applies to all 16 visits):**

#### Step A — Present what's there

Bootstrapper opens the visit by orienting the Maintainer:

> "Now we're at §<N> — <title>. The current content is:
>
> <brief summary of fixed content + listing of any BOOTSTRAP markers still empty>
>
> Let me check what we know from your product before discussing."

If the section is fixed (all content already written, no markers), the summary captures the key rules in 2-4 bullets. The Maintainer sees what's there without re-reading the whole section.

If the section is a marker (BOOTSTRAP block waiting for content), the Bootstrapper names the marker and what it expects to contain.

If the section is mixed (some fixed text + a marker for project-specific extension), the Bootstrapper summarizes the fixed and names the marker.

#### Step B — Check conflicts with the product

Bootstrapper looks at the section's content against PRODUCT.md, pd-*.md, ft-*.md, and the Decisions Captured. Surfaces any tension:

> "Looking at this against your product: the default says X. Your `ft-quotation-collection.md` describes Y, which fits X cleanly. No conflict."

Or:

> "Looking at this against your product: the default says strict layering with no carve-outs. Your `ft-realtime-chat.md` mentioned latency targets that would be hard to hit with strict layering on the chat path. That's a potential conflict — want to address it?"

If no conflict, this step is fast (one sentence). If there's a conflict, this step opens the discussion.

#### Step C — Discuss adjustments and fill markers

This is where the substance happens. The shape varies by section type.

**For fixed sections:**

If no conflict, this step is almost empty. Bootstrapper might still ask one audit question:

> "The default cross-cutting decision for idempotency is webhook deduplication via stable message ID. Your product spec confirms this. Anything you want to add or change?"

Maintainer says "looks good", and Step C is done.

If there's a conflict, Bootstrapper presents options:

> "Three options for the layering carve-out:
> **A)** Strict layering everywhere, accept the chat latency cost.
> **B)** Carve out the chat path — direct adapter-to-domain calls in that one flow.
> **C)** Different architectural style entirely (e.g., chat as a separate service).
>
> Recommendation: **B**. Localized carve-out documented in §4 keeps the rule clear elsewhere while accepting reality on the chat path. Which?"

Maintainer decides. Bootstrapper captures the decision via capture-aloud:

> "Capturing for §4: Strict layering applies, with one documented carve-out — the chat path in `ft-realtime-chat.md` allows direct adapter-to-domain calls. Reason: latency targets. This will be added to §4 as an explicit exception clause."

**For marker sections:**

Bootstrapper conducts focused questioning to extract the marker's content. The questions vary by marker (see Section 8 — Forcing functions specific to BS-03 for per-marker probing patterns).

Example for §2 (Component view marker):

> "The Component view marker wants a Mermaid diagram + Component responsibilities list. Let me build the diagram from what we know.
>
> Components so far based on BS-01/BS-02:
> - <Inbound channel> — entry point for buyer messages
> - <API HTTP> — internal REST surface
> - <Worker> — background processing for async tasks
> - <Primary DB> — source of truth
> - <Vector store> — derived from primary
> - <Cache/queue> — ephemeral
> - <LLM provider adapter> — external integration
>
> Did I miss anything? Is anything wrong?"

Maintainer adjusts. Bootstrapper iterates until the component list is complete. Then captures via capture-aloud:

> "Capturing for §2:
>
> Components (final):
> - <list>
>
> Component responsibilities (one bullet each):
> - <bullet>
>
> I'll compose the Mermaid in step D."

**For mixed sections:**

Combine both patterns. Discuss fixed parts first if there are conflicts, then move to filling the marker.

#### Step D — Present the final section content

Bootstrapper composes the final markdown for the section in a fenced block, ready to apply. The fence is `~~~markdown` (matches BS-01/BS-02 pattern for nested-code safety):

> "Here's the final content for §<N>. Apply this to the section in ARCHITECTURE.md (replacing the existing content from the section header down to the next `---`):
>
> [fenced block with section content]
>
> Apply and confirm?"

For fixed sections with no changes, this step is skipped — Bootstrapper says "no changes needed, moving on" and goes to Step F.

For fixed sections with small adjustments, the fence contains the entire section as it should now read (not just the diff).

For marker sections, the fence contains the section with the marker replaced by the actual content.

#### Step E — Maintainer applies

The Maintainer applies the change to the file. They confirm in chat:

> "Applied."

Or:

> "Applied, but I changed X to Y because <reason>."

If the Maintainer adjusted during application, Bootstrapper acknowledges:

> "Got it. Updated my mental state for the rest of the walk."

#### Step F — Move to next section

Bootstrapper transitions without ceremony:

> "Next is §<N+1> — <title>. <Brief link to why this comes next: 'It builds on §<N> because...'>"

If the previous section's decisions inform the next one (e.g., §4 layering decisions affect §2 component view), the Bootstrapper carries that context forward and uses it in Step B of the next section.

---

**Per-section ordering inside 7.2 (the 16 visits):**

The Bootstrapper visits sections in the order from Section 6's tables (Walk order). Visits 1-10 are ARCHITECTURE.md. Visits 11-16 are STRUCTURE.md.

A few visits deserve special operational notes:

**Visit 1 (§1 What this project is):** Fast. Content is downstream of PRODUCT.md + ft-*.md. Bootstrapper builds the one-paragraph description, the core asset statement, the actors table, the v1 constraints directly from BS-01/BS-02 docs and presents for confirmation. The Maintainer usually approves with minor adjustments. 5-10 minutes typical.

**Visit 4 (§2 High-level component view):** Slowest. Building the Mermaid diagram requires deciding component names (using Decisions Captured where possible), confirming what each owns, and drawing relationships. 10-20 minutes typical.

**Visit 5 (§3 Main flows):** Slow. At minimum the main user flow has to be walked step by step. If the product has multiple critical flows (async work, recurring jobs, webhook receivers), each gets its own walk. 10-20 minutes per flow.

**Visits 6-8 (§6, §7, §8):** Medium. Data view and Integrations leverage §2's component list. Quality attributes requires the Maintainer to commit to specific numbers (latency, scale, cost). 5-15 minutes each.

**Visit 12 (STRUCTURE.md Repository layout):** Slow. Building the concrete folder tree. The template ships with `PYTHON_REFERENCE_EXAMPLE` in a comment block — the Bootstrapper uses it as a starting point and adapts to the chosen language and the project's actual needs. 10-15 minutes typical.

**All other visits:** Fast. Defaults usually stand. Each is 2-5 minutes.

**Total time for 7.2:** 50-75 minutes typical, with the heavy visits (4, 5, 12) eating most of the budget.

### 7.3 — Verify outcomes, capture Decisions Captured for BS-04, bridge to BS-04 (10-15 minutes)

**Purpose:** Close BS-03 cleanly. Walk the exit criteria checklist. Recap what's been captured for BS-04 (the next step). Set the Maintainer up to start BS-04 or pause.

**Technique:**

1. **Walk the Exit criteria checklist (Section 3).** For each:
   - Section X filled?
   - Section Y adjusted?
   - Any markers still empty?

   Surface any unmet outcome. Per Section 10.2 below, the gap is either covered now (option A) or deferred with structure (option B).

2. **Recap Decisions Captured for BS-04.** BS-03 surfaces tech-stack signals naturally as components get named. Recap them:

   > "Decisions Captured during BS-03, to formalize in BS-04:
   >
   > - **Web framework**: <X> (mentioned during §2 Component view)
   > - **Database**: <Y> + <Z extensions> (mentioned during §6 Data view)
   > - **Async work**: <queue + worker library> (mentioned during §3 Main flows)
   > - **LLM provider**: <provider> (mentioned during §7 Integrations)
   > - **Observability stack**: <tools> (mentioned during §5 Cross-cutting)
   >
   > These get formalized in BS-04 (TECH_STACK.md), where we also pin versions and create ADRs for non-obvious choices."

3. **Preview BS-04.** Two sentences:

   > "BS-04 takes the architectural shape we just defined and pins it to specific tools: framework versions, database extensions, library choices, provider names. By the end of BS-04, `TECH_STACK.md` is filled and any architecturally significant tool choice has its own ADR."

4. **Ask for readiness.**

   > "BS-04 typically takes 30-60 minutes. We continue in this same chat. Ready to start BS-04, or want a break?"

5. **If the Maintainer pauses:** all ARCHITECTURE.md and STRUCTURE.md edits are already in the repo (applied during 7.2), so nothing additional to save. The next session starts by reading what was captured, recapping briefly, and confirming nothing has changed before opening BS-04.

**Signal that BS-03 is done:** the recap is agreed, Decisions Captured for BS-04 are confirmed, the Maintainer either kicks off BS-04 or explicitly pauses. The Bootstrapper does NOT silently roll into BS-04 — the transition is an explicit acknowledgment.

---

## 8. Forcing functions specific to BS-03

The forcing functions from BOOTSTRAPPER section 10 apply across all BS steps. The following are calibrated variations specifically for architecture review — situations that come up frequently in BS-03.

### 8.1 — When the Maintainer says "the default looks fine" without engagement

**Symptom:** Bootstrapper presents §4 (Internal layers) or §5 (Cross-cutting) defaults, and the Maintainer responds "looks good" without engaging with whether it actually fits their product.

**Why it's a problem:** the template defaults are good defaults, but they make assumptions (clean architecture suits multi-actor backends, port placement assumes services + repositories pattern, etc.). A Maintainer skimming past defaults loses the chance to challenge them where they don't fit.

**Response pattern:**

> "Before we accept the default for §X, let me test it against your product. Your `ft-realtime-chat.md` has latency targets that imply <implication>. Does strict layering on that path concern you? If no, the default holds. If yes, we should carve out an exception now while it's fresh."

Force one specific consideration tied to a specific feature. Don't accept "looks fine" without at least one product-grounded probe.

### 8.2 — When the Maintainer wants to invent architectural decisions during BS-03

**Symptom:** During §2 (Component view), the Maintainer says "let me also add a Kafka topic for event sourcing" or "let me throw in a service mesh".

**Why it's a problem:** BS-03 reviews and confirms architecture, it doesn't invent layers that weren't surfaced in BS-01/BS-02 product needs. Inventing during BS-03 expands scope, possibly speculatively, and the Maintainer may regret it during BS-04 (cost) or implementation (complexity).

**Response pattern:**

> "You just surfaced <new thing>. Two questions:
>
> 1. Is this driven by a real product need from BS-01/BS-02, or is it a 'might be useful' addition?
> 2. If it's real, which feature in `docs/product/` requires it?
>
> If you can name the feature, we add the component. If not, capture as an Open Question for §10, address later via ADR when the need is concrete."

This filters speculation from genuine missed components.

### 8.3 — When the Maintainer can't articulate the main flow end-to-end

**Symptom:** At §3 (Main flows), the Maintainer struggles to walk through the canonical flow step by step. They describe the user side, then jump to a vague "and then stuff happens".

**Why it's a problem:** if the main flow can't be articulated, the architecture isn't grounded in a real user journey. ft-*.md files may describe the feature in isolation but not how it threads through the system.

**Response pattern:**

> "Let me try walking it for you. <Step 1>. Then? <Step 2>. Then? ...
>
> I'm getting stuck at step <N>. What component handles that, and what does it produce as output?"

Walking the flow with explicit prompts at each step surfaces the gap. If the gap is real (the Maintainer genuinely hasn't thought about a piece of the flow), capture as Open Question for §10 and proceed. If the gap is just articulation, the prompting unstucks it.

### 8.4 — When the Maintainer slides into engineering detail during architecture

**Symptom:** At §6 (Data view) the Maintainer starts describing exact column types, index strategies, partitioning rules.

**Why it's a problem:** BS-03 is structural. Specific schema design is `DATA_MODEL.md` territory (a future doc), not BS-03. Capturing schema detail in ARCHITECTURE.md couples architecture to specific design choices, making both harder to evolve.

**Response pattern:**

> "Got it on <detail> — schema-level decisions land in future `DATA_MODEL.md` (when you have actual entities to model). For BS-03, what I need is the architectural view: which store holds what kind of data, and which is source of truth. Save the schema specifics for when you start modeling entities in code."

Push back to the structural level.

### 8.5 — When the Maintainer can't commit to quality attributes

**Symptom:** At §8 (Quality attributes), the Maintainer says "we'll figure latency out when we measure" or "we don't have scale targets yet".

**Why it's a problem:** Quality attributes without targets are not commitments. The architecture has no acceptance criteria. Decisions later (cache vs no cache, async vs sync, sharded vs single DB) have no anchor.

**Response pattern:**

> "I get not having precise numbers yet, but we need at least a starting commitment. Let me propose and you adjust.
>
> For latency, a typical AI-first webhook backend targets p95 < 500ms for the user-facing ack. Reasonable for you?
>
> For scale, a typical v1 target is 100 active users in month 1, 10x by month 6. Reasonable, or different shape?
>
> For cost, a typical AI-first product tracks LLM cost per task. Reasonable to track from day one?
>
> These can be revised in BS-07 or via ADR. For now, what's your starting commitment?"

Force a number. The number can be revised later — but having no number means later decisions have no anchor.

### 8.5.5 — When the development environment posture goes implicit

**Symptom:** At §5 (Cross-cutting → Development environment), the Maintainer skims the section without picking a posture, or treats it as "we'll just use Docker for the DB and run the app local" without explicit acknowledgment.

**Why it's a problem:** the development environment posture has real downstream consequences. It decides whether the Dockerfile lands in phase 1 (fully containerized dev) or phase 2 (native dev); whether a Dev Container config exists; whether the README's "how to run locally" section mentions `docker compose up app` or `uv run uvicorn`. Skipping the explicit choice means surprise at TSK-001 or TSK-013 when the question can no longer be deferred.

**Response pattern:**

> "Two postures for dev environment:
>
> **A) Native dev + containerized infra.** Application runs via `uv run` / `npm run dev` directly on your machine. DB, cache, queue in Docker Compose. Dockerfile exists for prod/CI but isn't used daily by devs. Faster hot-reload, easier debugging, but the dev's OS/toolchain can drift from production.
>
> **B) Fully containerized dev.** Application runs inside a container in dev too, via `docker compose up app`. Live-reload via volume mount. Stronger reproducibility ('works on my machine' is basically eliminated) but slower iteration and more setup friction. Often paired with a Dev Container config for one-click VSCode/Codespaces onboarding.
>
> Default recommendation: **A** unless your team has been burned by environment drift before, or contributors will be onboarded via Codespaces.
>
> Which one?"

Force a commitment. The choice ripples to BS-04 (which Dockerfile TSK is created, when) and BS-07 (TSK ordering). It's not a 'figure it out later' question.

If the Maintainer picks **B**, capture: 'Dockerfile is a phase 1 TSK; consider Dev Container config alongside it.' If **A**, capture: 'Dockerfile is a phase 2 TSK (or near phase 2 → phase 3 transition, before first deploy).'

### 8.6 — When component naming gets stuck

**Symptom:** At §2 (Component view), the Maintainer can't decide whether to name a component "Worker" or "AsyncProcessor" or "JobRunner".

**Why it's a problem:** naming bikesheds eat time. Architecture review isn't a naming workshop.

**Response pattern:**

> "Let me pick. Based on conventions in your stack and what the component does, I recommend `<name>`. We can rename in CODE_STYLE.md (BS-05) if a different convention applies. For now, this lets us move on."

Recommend with reasoning, move on. Real bikeshedding deserves an ADR if it later becomes contested, but not in BS-03.

### 8.7 — When STRUCTURE.md Repository layout doesn't match Decisions Captured

**Symptom:** At Visit 12 (STRUCTURE.md Repository layout), the Bootstrapper proposes a Python folder tree, but the Maintainer says "wait, we're going TypeScript".

**Why it's a problem:** the Decisions Captured for language got mixed up earlier. The folder tree is language-specific.

**Response pattern:**

> "My fault for assuming Python. Two questions:
>
> 1. Confirm the language: TypeScript with which runtime (Node, Deno, Bun)?
> 2. Any framework Decision Captured I missed (Nest, Fastify, Hono)?
>
> Once confirmed, I'll regenerate the folder tree against the right conventions."

Re-anchor and proceed. Don't paste over the wrong tree.

### 8.8 — When two architectural sections contradict each other mid-walk

**Symptom:** §4 (Layering) was set with carve-outs for chat latency. §5 (Cross-cutting) discussion surfaces that all use cases must own the transaction boundary. The chat carve-out + transaction-at-use-case combo is potentially inconsistent.

**Why it's a problem:** sections fill in sequence; a later section's decisions may invalidate an earlier section's assumption.

**Response pattern:**

> "Spot check — §4 we carved out the chat path for direct adapter-to-domain calls. §5 says use cases own the transaction. If the chat path skips the use case, where does its transaction boundary live? Two options:
>
> A) Chat path still goes through a use case, just with carve-out on the layer below.
> B) Chat path is exempt from the transaction-at-use-case rule too, accepting it doesn't need transactional atomicity (read-only, idempotent).
>
> Which holds?"

Resolve before continuing. If the resolution changes §4, the Bootstrapper goes back, updates §4 in the chat content (capture-aloud), surfaces the change, applies the revision, and continues.

### 8.9 — When Open Questions section ends up empty

**Symptom:** By Visit 10 (§10 Open questions), the Bootstrapper has nothing to capture there. Everything was resolved during the walk.

**Why it's a problem:** an empty Open Questions section in a real product architecture is suspicious. Real systems have unresolved structural questions — the question is whether the Maintainer is hiding them or has already addressed them.

**Response pattern:**

Probe one or two times:

> "Empty Open Questions feels too clean. Two things to check:
>
> 1. Anything about scaling beyond v1 that we deferred but didn't capture? E.g., 'how do we shard if the primary DB hits write ceiling?'
> 2. Anything about external integrations that might change provider? E.g., 'we're locked to <provider> in v1 but want optionality in v2'
>
> Anything come to mind?"

If after probing the section still ends up empty, that's acceptable — but rare for a real project.

### 8.10 — When the Maintainer wants to skip STRUCTURE.md

**Symptom:** After finishing ARCHITECTURE.md, the Maintainer says "STRUCTURE is fine, let's go to BS-04".

**Why it's a problem:** STRUCTURE.md Repository layout marker is one of the hardest required outcomes. Skipping it means engineers (and Claude Code) don't know where files go.

**Response pattern:**

> "STRUCTURE.md isn't optional — without the Repository layout filled, your first execution prompt doesn't know where to put files. Two options:
>
> A) Do it now, 10-15 minutes.
> B) Skip BS-03 closure and come back to STRUCTURE.md before TSK-001 is ever planned.
>
> Recommendation: A. We're already in flow. Want to push through?"

Default to A. If the Maintainer really wants B, they can — but note it as a blocker for TSK-001 planning.

---

## 9. Pre-fill rules (section-by-section)

The Bootstrapper does **not** consolidate at the end of BS-03. Instead, content is composed and applied **per section**, with the Maintainer applying each section's final block before the next section begins.

This section defines:

- The capture-aloud pattern adapted to per-section discussion
- What the final section block looks like in fenced markdown
- Special cases (ADRs created during BS-03, BOOTSTRAP marker syntax)

### 9.1 — Capture-aloud during section discussion (Step C)

Same fundamental pattern as BS-01/BS-02, but the destination is "the section being discussed" rather than a specific file:

**Format:**

> "Capturing for §<N>: <what + structured anchor>. This goes into the final block for this section."

**Examples:**

- "Capturing for §4: Strict layering applies, with one documented carve-out — the chat path in `ft-realtime-chat.md` allows direct adapter-to-domain calls. Reason: latency targets."

- "Capturing for §2 Component responsibilities: `<TelegramBot adapter>` — owns inbound webhook receipt, signature validation, and outbound message send. Located in `app/infrastructure/messaging/telegram/`."

- "Capturing for §6 Data view: `<Postgres>` is source of truth for buyers, orders, quotations. `<pgvector>` derives from canonical descriptions in Postgres — rebuildable. `<Redis>` holds idempotency keys and rate limit counters — ephemeral."

**Frequency:** 3-8 captures per section during discussion, calibrated to section complexity. Fixed sections with no changes may have zero captures. Marker sections may have 5-10.

### 9.2 — The final section block (Step D)

When the discussion for a section is complete, the Bootstrapper composes the final markdown for that section in a fenced block. The fence is `~~~markdown` (same convention used in BS-01/BS-02 patches), and inside it goes:

- The section header (`## N. <title>`)
- The full final content as it should appear in the file
- Ending just before the `---` separator that comes between sections (the separator stays where it is in the file; don't include it in the fence)
- Comments (HTML comments) included if part of the final content; removed if they were only BOOTSTRAP marker scaffolding

The opening line the Bootstrapper uses:

> "Here's the final content for §<N>. Apply this to the section in ARCHITECTURE.md (replacing the existing content from the section header down to the next `---`)."

Followed by the fenced block, followed by:

> "Apply and confirm?"

**Why `~~~markdown` (not triple backticks):**

The section content may itself contain triple-backtick code blocks (Mermaid diagrams, code examples, JSON snippets). Using `~~~markdown` as the outer fence prevents nesting conflicts. This is the same convention used in BS-01/BS-02 patches.

**What does NOT go inside the fence:**

- The horizontal rule separators (`---`) between sections — those stay in the file as-is
- Comments to the Maintainer about why the content was chosen — those go in chat, outside the fence

### 9.3 — BOOTSTRAP marker handling

BOOTSTRAP markers in the template look like HTML comments with the pattern `BOOTSTRAP:MARKER_NAME:START` and `BOOTSTRAP:MARKER_NAME:END` wrapping a placeholder description.

When a marker is filled during BS-03, the entire marker block (including the START/END comments) is **replaced** by the actual content. The Bootstrapper does NOT keep the marker comments alongside the new content.

The transformation is:

- **Before:** the section contains the marker block with the placeholder description inside
- **After:** the section contains the actual content (e.g., the Mermaid diagram + Component responsibilities list), with the marker block entirely gone

The marker is gone. The content replaces it.

### 9.4 — Handling fixed sections with no changes

If a section is fixed (all content already in the template) and the discussion concludes that no adjustments are needed:

- Bootstrapper skips Step D (no final block needed)
- Bootstrapper says: "§<N> stays as is. No changes needed. Moving to §<N+1>."
- Maintainer doesn't need to touch the file

This applies most often to §9 (What this document does not cover) and the STRUCTURE.md "When the structure must change" section.

### 9.5 — Handling fixed sections with small adjustments

If a fixed section needs small adjustments (a few lines edited, a clause added, an example tweaked):

- Bootstrapper presents the **entire updated section** in the final block, not just the diff
- Maintainer replaces the entire section in the file
- This is simpler than diff-based application and avoids partial-edit mistakes

The exception is when only one line changes deep in a long section. In that case, the Bootstrapper can present just the line change with explicit before/after:

> "In §5 Cross-cutting decisions, sub-section 'Error handling', change the line:
>
> Before: `<exact text before>`
>
> After: `<exact text after>`
>
> Apply and confirm."

The decision (whole-section replacement vs targeted line edit) is judgment. When in doubt, whole-section replacement.

### 9.6 — ADRs created during BS-03

If an architecturally significant choice is made or confirmed during BS-03 that warrants its own ADR (per BOOTSTRAPPER.md guidance: "would the next contributor need context to understand why we chose this?"), the Bootstrapper surfaces this and proposes creating the ADR.

**When to propose an ADR:**

- Non-default architectural style adopted (e.g., vertical slice instead of Clean)
- Port placement rule modified from default
- Idempotency mechanism non-trivial (custom store, custom TTL strategy)
- Observability stack chosen (specific tracing + metrics + logs tools)
- Quality attribute commitment that materially constrains implementation (e.g., "p95 < 100ms" — that's a real architectural commitment)

**ADR creation flow during BS-03:**

The ADR is **drafted by the Bootstrapper in chat** (using `docs/engineering/adr/TEMPLATE.md` as floor), the Maintainer applies it to a new file in `docs/engineering/adr/`, and references to the ADR are added to the relevant ARCHITECTURE.md sections as cross-references.

The Bootstrapper does NOT pre-create ADRs. They emerge from the conversation. Typically BS-03 produces 0-3 ADRs.

### 9.7 — Decisions Captured for BS-04

BS-03 surfaces tech-stack signals naturally — components in §2 get named, libraries in §5 (observability) get mentioned, providers in §7 (Integrations) get named. The Bootstrapper does NOT write these into ARCHITECTURE.md verbatim (the file is structural, not stack-specific) but holds them mentally for the Bridge to BS-04 (sub-block 7.3).

Same pattern as BS-01/BS-02 Decisions Captured handling.

Common signals during BS-03:

- **Web framework**: surfaces in §2 Component view (e.g., FastAPI, Nest, Hono)
- **Database**: surfaces in §6 Data view (e.g., PostgreSQL with pgvector, MongoDB)
- **Async work**: surfaces in §3 Main flows (e.g., Arq + Redis, Celery, BullMQ)
- **LLM provider**: surfaces in §7 Integrations (e.g., Anthropic, OpenAI)
- **Observability stack**: surfaces in §5 Cross-cutting (e.g., OpenTelemetry + structlog + Tempo)
- **Object storage**: surfaces in §6 / §7 (e.g., S3, R2)
- **Development environment posture**: surfaces in §5 Cross-cutting (native dev + containerized infra vs fully containerized dev). Determines whether the Dockerfile is a phase 1 TSK or a phase 2 TSK, and whether a Dev Container config exists.

These get formalized in BS-04 (TECH_STACK.md) with versions and provider-specific ADRs.

---

## 10. Required outcomes (checklist for closing BS-03)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step, per BOOTSTRAPPER section 9 sub-step 7 ("Verify required outcomes"). The checklist mirrors Section 3 (Exit criteria) but reformatted for the closing conversation.

### 10.1 — Outcomes that must be true to close BS-03

#### ARCHITECTURE.md

- [ ] §1 What this project is — filled with one-paragraph description, core asset, actors table, v1 constraints
- [ ] §2 High-level component view — Mermaid diagram + Component responsibilities filled
- [ ] §3 Main flows — at minimum the main user flow + any critical async/recurring flow
- [ ] §4 Internal layers — reviewed; defaults confirmed or adjustments documented
- [ ] §5 Cross-cutting decisions — six sub-sections reviewed; defaults confirmed or adjustments documented. **Development environment posture (native+containerized infra vs fully containerized) explicitly committed.**
- [ ] §6 Data view — table of stores with source-of-truth designation
- [ ] §7 Integrations — table of external integrations with port/adapter pairs
- [ ] §8 Quality attributes — latency, scale, cost, accepted trade-offs filled with specific commitments
- [ ] §9 What this document does not cover — reviewed (usually no changes)
- [ ] §10 Open questions — unresolved structural items captured

#### STRUCTURE.md

- [ ] Principles section — reviewed; defaults confirmed or adjustments documented
- [ ] Repository layout — concrete folder tree filled
- [ ] `.ai/` folder reference — confirmed matches actual `.ai/` structure
- [ ] `docs/` folder reference — confirmed matches actual `docs/` structure
- [ ] Naming conventions — reviewed (usually defers to CODE_STYLE.md)
- [ ] When the structure must change — reviewed (usually no changes)

#### ADRs (if any were created)

- [ ] ADRs for non-default architectural choices created in `docs/engineering/adr/`
- [ ] Cross-references from ARCHITECTURE.md to relevant ADRs added

#### Anchor questions answered

- [ ] **Anchor 1 (Component shape)** — captured in §2 Component view
- [ ] **Anchor 2 (Main flow end-to-end)** — captured in §3 Main flows
- [ ] **Anchor 3 (Source of truth vs rebuildable)** — captured in §6 Data view
- [ ] **Anchor 4 (Quality attributes)** — captured in §8 with specific numbers

#### Decisions Captured for BS-04

- [ ] BS-03 contributed N additional Decisions Captured beyond what BS-01/BS-02 produced. The Bootstrapper holds these in mental state for BS-04 (TECH_STACK.md formalization)

### 10.2 — When a required outcome is unmet

Same options as BS-01 and BS-02:

**Option A — Cover it now.** Pick up the conversation thread on the gap. Most gaps in BS-03 take 5-15 minutes to fill (e.g., one Open Question forgotten; one quality attribute not yet committed).

**Option B — Defer it with structure.** Document the gap explicitly:
- Missing section content → Open Question entry in §10
- Missing Open Question content that should be there → captured as a future-ADR candidate
- Quality attribute genuinely unknown → captured as "TBD at <event>" in §8 with the event named (e.g., "TBD after first load test")

The Bootstrapper recommends Option A when the unmet outcome is load-bearing for BS-04 (e.g., §2 Component view missing — BS-04 can't pin tools without knowing what components exist). Option B when the outcome is genuinely premature to resolve.

### 10.3 — What's explicitly NOT required to close BS-03

To prevent perfectionism:

- Specific library versions, framework versions — BS-04
- Provider-specific configuration (regions, endpoints, SDKs) — BS-04
- Detailed schema for any entity — future `DATA_MODEL.md`
- Detailed endpoint design — future `API_CONVENTIONS.md`
- Detailed prompt design or model selection per task — future `AI_INTEGRATION.md`
- Test pyramid shape, marker conventions — BS-05
- Coding conventions, async patterns specifics — BS-05
- Security policy detail beyond architectural (auth/observability checks at structural level only) — BS-05
- Deployment topology, CI/CD specifics — emerge during implementation or BS-04
- Production runbooks, monitoring dashboards — emerge during implementation

The Bootstrapper resists pulling BS-04+ content into BS-03. Structural architecture is BS-03's scope; everything more concrete lives downstream.

---

## 11. Bridge to BS-04

When the Required outcomes checklist is complete (or consciously deferred with notes), the Bootstrapper transitions to BS-04 with the following sequence:

### 11.1 — Recap

Three or four sentences:

> "BS-03 captured the architecture. `ARCHITECTURE.md` has the component view (<N> components), main flows (<count>), data view (<N> stores), integrations (<N>), quality attributes (p95 <X>ms, <N> users in 6 months, <cost discipline>). `STRUCTURE.md` has the concrete folder tree. <N> ADRs created for non-default choices. Open questions remain on <list 2-3 main ones>."

### 11.2 — Confirm Decisions Captured for BS-04

Recap all tech-stack-relevant decisions surfaced across BS-01/BS-02/BS-03:

> "Tech stack signals carried into BS-04:
>
> From BS-01/BS-02:
> - Language: <X>
> - Primary channel: <Y>
> - Database family: <Z>
>
> From BS-03:
> - Web framework: <A>
> - Async work: <B>
> - LLM provider: <C>
> - Observability stack: <D>
> - Object storage: <E>
>
> In BS-04, we formalize each of these in TECH_STACK.md (with versions) and create ADRs for the non-obvious choices."

### 11.3 — Preview BS-04

Two sentences:

> "BS-04 takes the architecture and pins it to specific tools and versions. By the end of BS-04, `TECH_STACK.md` is filled, dependency versions are pinned, and architecturally significant tool choices have their own ADRs."

### 11.4 — Commit BS-03 before continuing or pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

Before moving to BS-04 (or pausing), the Maintainer commits the work BS-03 produced:

```
git add docs/engineering/ARCHITECTURE.md \
        docs/engineering/STRUCTURE.md \
        docs/engineering/adr/0001-<slug>.md \
        docs/engineering/adr/0002-<slug>.md
        # ... one line per ADR created during BS-03

git commit -S -m "docs(bootstrap): complete BS-03 — architecture"
git push origin main
```

The ADR lines are included only if BS-03 produced ADRs. The Bootstrapper presents the exact list based on what was actually created.

After the commit lands, proceed to 11.5 (continue) or 11.6 (pause).

### 11.5 — Continue in the same chat

> "BS-04 typically takes 30-60 minutes. We continue in this same chat — the architecture context from BS-03 carries forward. Ready to start BS-04, or want a break?"

If the Maintainer pauses, the same recovery pattern applies as BS-01/BS-02 — the docs are already in the repo (applied during 7.2), so resumption just re-reads ARCHITECTURE.md and STRUCTURE.md to refresh state.

### 11.6 — If the Maintainer wants to stop entirely

Same handling as BS-01/BS-02:

> "Understood. A few things before we pause:
>
> 1. Everything is saved in the docs — PRODUCT.md, pd-*.md, ft-*.md, ARCHITECTURE.md, STRUCTURE.md, ADRs (if any). They're not lost.
> 2. When you come back, stay in this same chat session. Starting fresh means rebuilding context, which loses the Decisions Captured I'm holding for BS-04+.
> 3. When you resume, say 'resuming bootstrap' and I'll re-orient.
>
> Take the break. The work is preserved."

---

## 12. Notes for the Bootstrapper LLM running BS-03

These are operational reminders specific to BS-03, on top of universal BOOTSTRAPPER guidance.

### 12.1 — The template's defaults are opinionated for a reason

The template ships with strong opinions: Clean Architecture with strict layering, port placement rules, repository pattern, Unit of Work with caller-owned transactions, specific cross-cutting decisions. These are not arbitrary — they reflect battle-tested choices for AI-first backend projects.

The Bootstrapper does NOT challenge defaults preemptively. Defaults stand until the Maintainer's specific product context surfaces a real conflict. "We might not need clean architecture" is not a conflict; "our chat path has 50ms latency targets that strict layering makes hard" is a conflict.

When in doubt, defaults hold. The Bootstrapper challenges the Maintainer to confirm fit, not to invent alternatives.

### 12.2 — Decisions Captured drive component naming

By the time BS-03 starts, the Bootstrapper has accumulated tech-stack signals from BS-01 and BS-02. Use them in §2 (Component view). If FastAPI was captured, the API box says "FastAPI app". If Anthropic was captured, the LLM adapter is named `AnthropicLLMAdapter` in the Component responsibilities list.

This makes the diagram immediately recognizable to the Maintainer as **their system**, not a generic template. If nothing was captured for a slot (e.g., async worker library), use the abstract name ("background worker") and note as deferred to BS-04.

### 12.3 — Section ordering matters; respect it unless asked otherwise

The walk order in Section 6 is designed so each section's decisions inform the next:

- §1 (What this project is) → grounds everything
- §4 (Layering) → defines the structural rules before components are drawn
- §5 (Cross-cutting) → defines auth/observability/etc before flows can name them
- §2 (Component view) → materializes the layers + cross-cutting choices
- §3 (Main flows) → walks data through the components
- §6 (Data view) → identifies what stores back each component
- §7 (Integrations) → identifies the external partners (some of which are stores)
- §8 (Quality attributes) → commits to targets, informed by everything before
- §10 (Open questions) → captures unresolved items that accumulated

Don't rearrange without reason. If the Maintainer requests a different order, accept but flag the ripple cost.

### 12.4 — Heavy sections deserve more time; don't rush them

§2 (Component view), §3 (Main flows), and STRUCTURE.md Repository layout are the heaviest sections. They produce the most substantive content and have the highest impact on downstream BS-04 and implementation. Plan for 10-20 minutes each.

Light sections (§9 What doesn't cover, Naming conventions) can be 2-5 minutes. Don't pad them.

### 12.5 — Pacing

BS-03 typically takes 60-90 minutes:

- **First 10-15 minutes**: opening, Decisions Captured recap, pattern announcement (7.1)
- **Middle 50-75 minutes**: section walk (7.2) — 16 visits, calibrated to heavy/medium/light
- **Final 10-15 minutes**: outcomes verification, Decisions Captured for BS-04, bridge (7.3)

If the conversation runs long (90+ minutes), check energy. Same pattern as BS-01/BS-02 — offer pause, offer lighter mode for remaining sections, never force exhausted Maintainers through full depth.

### 12.6 — ADRs are not pre-decided

The Bootstrapper does NOT come into BS-03 with a checklist of "ADRs to create". ADRs emerge from the conversation when an architecturally significant choice is made or confirmed. Typical BS-03 produces 0-3 ADRs; some products produce zero (defaults stand fully); some produce more (multiple non-default choices accumulate).

When an ADR-worthy choice emerges, surface it immediately:

> "This feels like ADR-worthy. The choice of <X> over <Y> would be hard for the next contributor to reconstruct without context. Want me to draft an ADR for this, applied after we finish §<N>?"

Maintainer decides. If yes, the ADR draft is presented after the current section's application, and applied as a new file in `docs/engineering/adr/`. Then continue the walk.

### 12.7 — Resist completionism on Component view

§2 (Component view) is the most tempting section to over-engineer. The Maintainer may want to draw every adapter, every utility, every helper class. Push back:

> "The Component view is **high-level**. 5-10 components is the right granularity. If you're naming individual classes, you're at the wrong layer — that lives in STRUCTURE.md's Repository layout or in code, not in ARCHITECTURE.md. What's the highest-level shape we need to show here?"

A good Component view has every reader able to point at any box and say "I know what that does and where it lives". More boxes than that, and the diagram becomes a directory listing.

### 12.8 — Flows beyond the main one are optional but valuable

§3 (Main flows) requires **at least** the canonical user flow end-to-end. Additional flows (async work, recurring jobs, webhook receivers, batch operations) are valuable but optional in BS-03 — they can be added later via ADR or in a future doc as they become relevant.

If the Maintainer's product has 3+ critical flows, walk all of them. If only the main flow is truly critical (others are simple/derivative), one flow is sufficient.

### 12.9 — The PYTHON_REFERENCE_EXAMPLE comments in the template

ARCHITECTURE.md and STRUCTURE.md ship with reference examples inside HTML comments tagged `PYTHON_REFERENCE_EXAMPLE`. These are deliberately scoped: they show the author's reference stack (FastAPI + SQLAlchemy + structlog + OTel) as a concrete materialization of the abstract rules.

The Bootstrapper:

- **References the example to ground discussions** when the Maintainer's stack is Python
- **Adapts the structure but preserves the spirit** when the Maintainer's stack is different (e.g., TypeScript with Nest, or Go with Echo)
- **Removes the PYTHON_REFERENCE_EXAMPLE comment block** at the end of BS-03 if the Maintainer's stack is genuinely different — leaving Python examples in a TypeScript project's docs is noise
- **Keeps the comment block as-is** if the Maintainer's stack matches Python — it's useful reference

The decision (keep/adapt/remove) is captured during the relevant section visits and applied at the appropriate moment.

### 12.10 — Some BOOTSTRAP markers in ARCHITECTURE.md and STRUCTURE.md reference old step names

The template currently has some BOOTSTRAP marker descriptions referencing the old step file naming (`01-domain.md`, `02-product.md`, `03-architecture.md`, `04-tools.md`). These were renamed to `BS-01..BS-07` during template development.

The Bootstrapper:

- **Ignores the stale step name references** when interpreting the marker's intent (the meaning is clear)
- **Replaces the marker block entirely** during application (the new content replaces both marker comments and any stale text inside)
- **Does not "fix" the old references** as a side mission — that's a template-maintenance pendency, not BS-03's concern
- **Flags the pattern as a known issue** at the end of BS-03 if the Maintainer notices it

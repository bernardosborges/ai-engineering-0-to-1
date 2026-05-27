# BS-01 — Product discovery

> **Step file scope**
>
> This document is read by the Bootstrapper LLM during BS-01 of the bootstrap workflow. It is **not** a document for the Maintainer to read directly — it is the operating instruction for the Bootstrapper as it conducts the product discovery conversation with the Maintainer.
>
> The Bootstrapper reads `.ai/workflows/BOOTSTRAPPER.md` for the overall methodology (Decisions Captured, forcing functions, ripple effects, universal step pattern) and reads this file for the specific content of BS-01.

---

## 1. Goal

Conduct a deep conversation with the Maintainer that captures **everything they currently know about the product** in a structured way, so the rest of the bootstrap (and the rest of the project's life) can build on a clear, written foundation rather than reconstructed memory.

The goal is not to design the product from scratch — it is to **extract, structure, and persist what the Maintainer already knows but has not yet articulated in writing**. The Maintainer arrives with a product idea, often partially formed. BS-01 is where that idea becomes documentation that survives the session.

The conversation must cover seven dimensions (see Coverage checklist below) and produce concrete answers to five anchor questions. By the end, the Maintainer should be able to read what was captured and recognize their own product back — clearer than they could have written it alone, but unmistakably theirs.

---

## 2. What this step produces

By the end of BS-01, the following exists in the repository:

### Documents written or pre-filled

- **`docs/PRODUCT.md`** — sections filled by this step:
  - **How it works (one paragraph)** — the user-facing flow end to end
  - **Core actors** — every actor that interacts with the system, one to two lines each
  - **Glossary** — first 5 to 10 domain terms with definitions
  - Remaining sections (Core entities, Phase strategy, Document map, Open questions, Inconsistencies) stay as BOOTSTRAP markers — those get filled in BS-02 (entities, document map) and BS-07 (phase strategy).

- **`docs/product/pd-*.md`** — seven Product Discovery files, one per dimension, created during the conversation:
  - `pd-motivation.md`
  - `pd-audience.md`
  - `pd-scope.md`
  - `pd-business-model.md`
  - `pd-risks.md`
  - `pd-workflows.md`
  - `pd-mvp.md`

  Each follows the template at `docs/product/TEMPLATE.md` (Purpose / Actors / Vocabulary / Entities / Policies / Edge cases / Open questions / Related).

  Not every `pd-*.md` will be deeply detailed at the end of BS-01 — some dimensions are richer than others depending on what the Maintainer knows. What matters is that **every dimension has at least a starting file with the high-level content captured**. Empty `pd-*.md` files are not acceptable; either fill them or explicitly defer with an "Open questions" entry inside the file.

### Decisions captured for later steps

By the end of BS-01, the Bootstrapper should be holding a mental list of decisions surfaced during the conversation that affect later steps. Common examples (will be recapped at the start of BS-04 and BS-05):

- Language preferences ("I want to use Python")
- Channel choices ("Telegram is the primary surface")
- Stack hints ("we'll need vector search", "we'll need a job queue")
- Architectural leanings ("I like Clean Architecture", "we need event-driven for X")
- Compliance signals ("GDPR matters", "we handle health data")

These are not formalized in BS-01 — they will be confirmed and acted on in BS-04 and BS-05. But they are noted so the Bootstrapper does not ask redundant questions later.

### What is NOT produced in BS-01

- `docs/product/ft-*.md` files (feature specs) — those come in BS-02
- `docs/PRODUCT.md` Core entities section — comes in BS-02 as features are spec'd
- Any `docs/engineering/*.md` updates — those come in BS-03 onwards
- Any ADRs, PDRs, or LDRs — recorded as they emerge in their own steps
- `docs/ROADMAP.md` updates — comes in BS-07

If the Maintainer pushes toward technical decisions during BS-01, the Bootstrapper defers them politely ("I'll capture that for BS-04 — for now let's stay on the product"). Tech decisions made before the product is clear lead to architecture that doesn't fit.

---

## 3. Exit criteria

BS-01 is complete when **all** of the following are true. The Bootstrapper walks through this checklist explicitly with the Maintainer at the end of the step (per BOOTSTRAPPER section 9, sub-step 7 "Verify required outcomes").

### Hard requirements

- [ ] The five anchor questions (next section) have been **answered in writing** — either in the corresponding `pd-*.md` file or, if the answer is broad, in `PRODUCT.md` directly. None of them remain only in the chat transcript.

- [ ] `docs/PRODUCT.md` has the **"How it works (one paragraph)"** section filled with a concrete, sequential narrative — not abstractions. A reader unfamiliar with the project should be able to follow it.

- [ ] `docs/PRODUCT.md` has the **"Core actors"** section listing every actor the Maintainer identified, including non-human actors (AI agents, the system itself, scheduled jobs) when relevant.

- [ ] `docs/PRODUCT.md` has the **"Glossary"** section with at least 5 domain-specific terms defined. If fewer than 5 surfaced, the Bootstrapper probes for more — there are almost always domain terms the Maintainer is using without realizing they're project-specific.

- [ ] **All seven `pd-*.md` files exist** with content reflecting what was captured. None are empty. If a dimension genuinely has little content (e.g., MVP scope is wide open), the file states that explicitly and lists open questions, rather than being a stub.

### Soft requirements (worth pushing for but not blockers)

- [ ] Cross-references between `pd-*.md` files are in place where natural (e.g., `pd-mvp.md` references actors defined in `pd-audience.md`).

- [ ] Open questions surfaced during the conversation are captured in the relevant `pd-*.md` file's "Open questions" section, not lost.

- [ ] At least one Decision Captured for a later step (language, stack, channel, architectural leaning, compliance signal).

### What does NOT block exit

- Unanswered second-order questions (those go to "Open questions" sections and stay alive)
- Lack of a chosen business model (it's enough to have mapped the options and noted which one the Maintainer leans toward — see the "Not accepting 'I don't know'" forcing function)
- Sparse content in `pd-*.md` for genuinely uncertain dimensions, as long as the file documents the uncertainty explicitly

The Bootstrapper does not advance to BS-02 silently. If any hard requirement is unmet, the Bootstrapper surfaces it: "Before moving on, I want to address X. We haven't yet captured Y in writing."

---

## 4. Anchor questions

These are the five questions that **must be answered with confidence** before BS-01 closes. They are not asked in order — they emerge through the conversation flow (see Conversation flow in Bloco 2). But the Bootstrapper keeps mental track of which ones are still open, and surfaces any that remain unanswered before declaring the step complete.

These questions were chosen because each one, if left unanswered, leaves a load-bearing gap in the product foundation that will surface as confusion or rework later.

### Anchor 1 — Who owns the data the system generates over time?

The system will accumulate data: user interactions, transactions, content, model outputs, audit trails. Who owns that data? The user who created it? The platform? Both, with carve-outs? Is the data the strategic asset of the project, or a byproduct?

Why it matters: this answer drives privacy decisions, monetization options, legal posture, and architectural choices about replication and export.

If the Maintainer hasn't thought about this, the Bootstrapper does NOT accept "we'll figure it out later." Map the options:

- A) User owns their data, platform has limited rights (typical for productivity tools, journals, creator platforms)
- B) Platform owns the data, user has rights of access and deletion (typical for marketplaces, aggregators, B2B SaaS)
- C) Shared ownership with explicit carve-outs (typical for social networks, collaborative tools)

Pick one even if loosely. Document the choice in `pd-business-model.md` or `pd-risks.md`.

### Anchor 2 — What is this system explicitly NOT?

For every product there are adjacent things it could be but isn't. Naming them explicitly prevents scope creep and clarifies positioning.

Why it matters: founders frequently waste cycles on features that look like they belong but actually belong to a different product. Naming non-goals is the cheapest way to keep focus.

The Bootstrapper probes: "You described what the product does. Now: what does it NOT do? Specifically, what would a user ask for that you would say no to, and why?"

Examples to push for:
- Is it a marketplace or a directory? (Different operational complexity)
- Is it a tool or a service? (Different business model)
- Is it self-serve or assisted? (Different scaling profile)
- Is it for individuals or teams? (Different feature set)
- Does it replace something existing, or sit alongside?

Document in `pd-scope.md` under "What this system is NOT."

### Anchor 3 — How does the product make money?

Even if the answer is "free for now, monetize later," the Bootstrapper insists on mapping the options. A product whose monetization is "we'll figure out later" has its architecture, terms of service, and data model shaped by an invisible assumption that will later need to be unwound.

Why it matters: monetization decisions shape entity modeling (accounts, billing, subscriptions, transactions), the actor list (does a "buyer" become a "subscriber"?), and the legal posture (consumer contracts vs B2B agreements).

Apply the "Not accepting 'I don't know'" forcing function. Map the typical options for the category:

- Subscription (flat, tiered, per-seat)
- Transaction fee (percentage, flat, per-event)
- Marketplace take-rate
- Lead generation / referral
- Data licensing
- Freemium with paid upgrades
- One-time purchase with optional add-ons
- Paid services on top of free tool
- Enterprise sales / B2B contracts

Pick the closest fit even if loosely. Document in `pd-business-model.md`.

### Anchor 4 — Who operates the system in production?

This question separates "the product" from "the production reality of the product." Many founders have a clear product vision but no model for who handles failures, edge cases, support, and quality assurance in production.

Why it matters: a fully automated product has a very different cost structure, failure mode, and trust profile than a human-in-the-loop product. The architecture, the observability, the escalation paths all depend on this.

The Bootstrapper asks: "When the system encounters a case it can't confidently handle — a failure, an ambiguous input, an unhappy user — what happens? Who steps in?"

Common patterns to surface:
- Fully automated, no humans (high scale, low margin per transaction)
- Hybrid with human reviewers for edge cases (typical for AI-driven products)
- Hybrid with human concierge for high-value cases (typical for B2B / enterprise)
- Fully assisted (every interaction touches a human, like consulting)

Document in `pd-business-model.md` (Operations section) and/or `pd-risks.md` (escalation paths).

### Anchor 5 — What accumulates over time and creates compounding value?

The most defensible products have something that becomes more valuable as more time passes or more usage happens. A database of normalized prices. A trained model. A network of suppliers. A library of templates. A repository of decisions. A reputation graph.

Why it matters: this is the project's strategic asset. Knowing what it is shapes which features get prioritized (those that build the asset faster) and which get deprioritized (those that don't compound).

The Bootstrapper asks: "Imagine the product is two years old and has a healthy user base. What does it have then that competitors couldn't replicate in a weekend by copying the surface? That is the strategic asset."

If the Maintainer says "the software itself," push back: software is easy to copy. The asset is usually data, network effects, accumulated trust, or workflow lock-in.

Document in `pd-motivation.md` (Strategic asset section, prominent).

---

## 5. Coverage checklist

The conversation must touch all seven dimensions below before BS-01 closes. The Bootstrapper does NOT present this checklist as a questionnaire to the Maintainer — it would turn the conversation into a form. Instead, the Bootstrapper holds the checklist mentally and steers the conversation to cover what's still uncovered.

"Each dimension corresponds to one pd-*.md file. The Bootstrapper anchors captured content via capture-aloud during the conversation (see Section 9) and writes the actual files at consolidation in 7.7."

### Dimension 1 — Motivation and context (`pd-motivation.md`)

Captures why this product exists at all, why this Maintainer is the one to build it, and why now.

- [ ] **1.1** The personal trigger — what experience, observation, or frustration led the Maintainer to consider this product
- [ ] **1.2** Founder-market fit — what makes the Maintainer (and team, if any) positioned to build this specifically, vs. someone else
- [ ] **1.3** Why now — what changed recently that makes this product viable, needed, or differentiated. If "nothing changed", probe whether the timing is actually right
- [ ] **1.4** Strategic asset — the answer to Anchor 5 (what compounds over time). Sometimes the Maintainer surfaces this clearly here; sometimes it emerges in Dimension 3 or 5
- [ ] **1.5** Why this exists vs. existing alternatives — what specifically is broken or missing in current options that justifies a new product (not just "ours is better" — name the specific gap)

### Dimension 2 — Audience and need (`pd-audience.md`)

Captures who the product is for, with enough specificity that a stranger could recognize the target user.

- [ ] **2.1** Primary user — a concrete description of the main user persona, with role/context/situation (not "small businesses" — "operations managers at factories companies with 5-50 employees, handling 3-10 RFQs per week")
- [ ] **2.2** Secondary users / actors — anyone else who interacts with the system, even if not the primary buyer (e.g., suppliers in a marketplace, reviewers in a content platform, parents of student users)
- [ ] **2.3** First 10 concrete users — the actual people or organizations the Maintainer would approach as the very first users. If they can't name them, the audience is not yet concrete enough
- [ ] **2.4** Evidence of need — what data points (conversations, observations, signals, attempted solutions) support that this user has the problem the product solves
- [ ] **2.5** What the user is doing today — without this product, how does the user currently address the problem? Be specific — what tools, what workarounds, what manual processes
- [ ] **2.6** Pain intensity — on a scale from "nice to have" to "I'd pay tomorrow", where does this sit for the target user? Critical for sizing the wedge

### Dimension 3 — Scope and differentiation (`pd-scope.md`)

Captures what the product does, what it doesn't, and how it differs from alternatives.

- [ ] **3.1** What the product does — a clear, scoped description in product terms (not technology terms). What problem it solves and how, at a behavioral level
- [ ] **3.2** What the product does NOT do — the answer to Anchor 2. Explicit non-goals, with reasoning
- [ ] **3.3** Closest alternatives — what competitors, substitutes, or DIY approaches exist today. Name them concretely (specific products, not "the market")
- [ ] **3.4** Differentiation — what makes this product distinct from those alternatives. Not "we're better" — specifically what trade-off this product makes that others don't
- [ ] **3.5** Positioning sentence — if the product were on a shelf next to alternatives, what one sentence would distinguish it. The Bootstrapper helps draft this if the Maintainer doesn't have it

### Dimension 4 — Users and interactions (`pd-workflows.md`)

Captures the actors and how they interact with the system, at a workflow level (not implementation level).

- [ ] **4.1** Complete actor list — every actor that interacts with the system, including non-human actors (AI agents, scheduled jobs, the system itself when it acts autonomously)
- [ ] **4.2** Per-actor goals — for each actor, what they're trying to accomplish when they interact with the system
- [ ] **4.3** Entry points — how each actor first encounters the system (signup flow? invitation? API? scheduled trigger?)
- [ ] **4.4** Core workflow per actor — at high level, the sequence of interactions a typical actor has from entry to outcome. Not screen-level — flow-level
- [ ] **4.5** Cross-actor interactions — where actors' workflows intersect (e.g., a buyer's request triggers a supplier's notification). This is often where the product's complexity lives
- [ ] **4.6** Frequency profile — for each actor, how often they interact. Daily? Weekly? One-time? This affects architecture and engagement model

### Dimension 5 — Business model and operations (`pd-business-model.md`)

Captures how the product makes money and how it runs in production.

- [ ] **5.1** Revenue model — the answer to Anchor 3. The chosen (or leading) monetization approach
- [ ] **5.2** Unit economics (sketch) — for the chosen revenue model, what does one transaction or one user cost vs. produce. Doesn't need to be exact — needs to be defensible
- [ ] **5.3** Pricing levels (sketch) — even loose. "Around $50/month" or "10% of transaction" or "free until X scale". Captures the order of magnitude
- [ ] **5.4** Production operator model — the answer to Anchor 4. Fully automated, hybrid, fully assisted?
- [ ] **5.5** Quality assurance approach — how the product knows when it's working well, when it's failing, and what happens when it fails
- [ ] **5.6** Data ownership and rights — the answer to Anchor 1. Who owns what; what rights each party has
- [ ] **5.7** Compliance signals — any regulatory or compliance regime the Maintainer mentions or implies (LGPD, GDPR, HIPAA, financial regulation, age restrictions, content moderation). Surfaced here and noted for `docs/legal/POLICY_CONSIDERATIONS.md` in BS-05

### Dimension 6 — MVP and validation (`pd-mvp.md`)

Captures the smallest version that produces value, and how the Maintainer will know it works.

- [ ] **6.1** MVP definition — what's in scope for the very first version that real users will touch. Specific features and flows, not abstract goals
- [ ] **6.2** Explicitly out of MVP — what's deferred and to which later phase (this echoes Anchor 2 but at a temporal level)
- [ ] **6.3** Distribution plan for first 100 users — how the Maintainer will actually get the MVP in front of users. "We'll do marketing" is not acceptable — specific channels, specific actions
- [ ] **6.4** Validation hypothesis — what specific outcome would tell the Maintainer the MVP is working. "Users like it" is not acceptable — what behavior, what retention, what transaction
- [ ] **6.5** Time to first signal — when does the Maintainer expect to see the validation signal? Two weeks after launch? Two months? Two quarters? Affects how Phase 3+ is sized in BS-07
- [ ] **6.6** Kill criteria — what would tell the Maintainer the product isn't working and they should stop. Healthy products need a defined "stop" signal as much as a "go" signal

### Dimension 7 — Risks and success (`pd-risks.md`)

Captures what could go wrong and what success looks like.

- [ ] **7.1** Top 3 risks — the things most likely to kill or seriously hurt the product. Specific, not generic ("competition will copy us" is generic; "if Stripe changes their fee structure we lose 40% margin" is specific)
- [ ] **7.2** Mitigation strategies (or acknowledged absence) — for each risk, either a plan or an explicit "no mitigation, accepted risk"
- [ ] **7.3** External dependencies — what the product depends on that the Maintainer doesn't control (APIs, regulations, third-party platforms, partner relationships). Each is a risk vector
- [ ] **7.4** Pre-mortem — if the product fails in 12 months, what would the post-mortem say? Forces the Maintainer to articulate failure modes they're avoiding
- [ ] **7.5** Success criteria at 12 months — what does success look like concretely. Number of users? Revenue? Retention? Specific outcomes for specific users
- [ ] **7.6** Personal commitment level — how much of the Maintainer's life is going into this. Side project / part-time / full-time / venture-backed. Affects pace and scope expectations

---

## 6. How the Bootstrapper uses the checklist

The 32 items above are the **map**, not the **script**. The Bootstrapper:

1. **Holds the map mentally** while running the conversation flow (Bloco 2).
2. **Marks items as covered** silently as the conversation surfaces content. The Maintainer doesn't see this scoring.
3. **Steers the conversation** toward uncovered items when an opening appears (e.g., the Maintainer just finished talking about actors, so the Bootstrapper naturally pivots to entry points and frequency).
4. **Surfaces gaps before closing the step** — if any items remain uncovered, the Bootstrapper explicitly raises them: "Before we close BS-01, we haven't talked about X. Can we cover it briefly?"

A few items will sometimes be **conscious gaps** rather than missed coverage. For example, a Maintainer might say "I don't have unit economics for revenue yet — I want to validate the demand first." That's acceptable IF documented: the item gets an "Open questions" entry in `pd-business-model.md` rather than a fabricated number.

The bar for "covered" is not "has a complete answer." The bar is "has a written entry in the relevant `pd-*.md` file that captures either the answer or the explicit uncertainty."

---

## 7. Conversation flow

The BS-01 conversation follows an arc, not a checklist. The Bootstrapper moves through nine sub-blocks in sequence. Each sub-block has a purpose, techniques to apply, examples of how to execute it, and signals that indicate readiness to move to the next.

The arc matters. Forcing structure too early kills the conversation; staying loose too long loses depth. The nine sub-blocks balance both — wide and exploratory at the start, structured and exhaustive at the end.

The Bootstrapper does NOT announce these blocks to the Maintainer ("Now we're in sub-block 3 — Mirror"). The transitions happen naturally through the conversation's flow.

### 7.1 — Opening (5-10 minutes)

**Purpose:** Get the Maintainer talking about the product in their own words, without imposing a structure on the first response. The first thing they say reveals what's salient to them — that's diagnostic information.

**Technique:**

- Single open-ended question. Resist the temptation to scaffold.
- After the answer, ask **one** follow-up that goes deeper into whatever they emphasized.

**Opening question (verbatim suggestion):**

> "Tell me about the product you want to build. Start wherever feels natural — what it does, who it's for, what made you decide to build it, whatever comes first."

**What to do with the response:**

- If the Maintainer leads with the **user problem** ("people struggle to X"), they likely have user-side clarity. Probe the product side next.
- If they lead with the **product mechanic** ("it's an app that does Y"), they likely have feature clarity. Probe the user side next.
- If they lead with the **technology** ("I'm using LLMs to..."), gently redirect: "Got it on the tech — let's come back to that. Tell me first what someone using the product would experience." Tech-first openings often hide a thin user understanding.
- If they lead with the **business** ("there's a $5B market for..."), redirect: "Market size matters, but let's start with the actual user. Who, specifically, do you want to serve first?"

**Signal to move on:** The Maintainer has produced 3-5 substantive paragraphs (or the equivalent in chat back-and-forth) covering the rough shape of the product, the user, and the motivation. You don't need full coverage yet — just enough to have a thread to pull on.

### 7.2 — Motivational probe (10-15 minutes)

**Purpose:** Surface the personal "why" before getting into the product mechanics. Anchor 1 (founder-market fit) and Dimension 1 (motivation/context) live here.

**Why this comes early:** the personal trigger reveals what the Maintainer **actually knows** vs. what they're guessing. A founder who saw a specific problem in a specific context has anchored knowledge; a founder who's chasing a category has theoretical knowledge. The product foundation needs to know which one it's standing on.

**Questions to ask (pick the ones that fit, don't ask all):**

- "What's the specific moment that made you think 'this needs to exist'?"
- "Why you? What makes you positioned to build this, vs. someone else?"
- "Why now? What changed in the world (or in you) recently that makes this the right time?"
- "What have you tried before in this space — built, considered, walked away from?"

**Forcing function — push for specificity:**

If the Maintainer answers in generalities ("I noticed that small businesses struggle with..."), push:

> "That's a category-level observation. What was the actual scene? Where were you, what were you doing, who was the person you saw struggling?"

The goal is to extract a concrete origin story. If the Maintainer can't produce one, that's diagnostic — the product is theoretical, and BS-01 needs to flag that gap rather than paper over it.

**Anchor via capture-aloud:** as substantive content surfaces, the Bootstrapper anchors it in the chat — not by writing to a file (consolidation happens in 7.7), but by stating back what was just captured.

Example:

> "Capturing the trigger: you saw <specific scene> and realized <X>. That goes into `pd-motivation.md` Personal trigger section at consolidation."

Anchor 3-5 times during this sub-block — the trigger story, founder-market fit elements, timing rationale. Items 1.4 (strategic asset) and 1.5 (vs. alternatives) often surface later; anchor them when they come up, even if it's in a later sub-block.

**Signal to move on:** the Maintainer has articulated a concrete personal connection to the problem, OR has acknowledged they don't have one (in which case it's documented as a known risk in `pd-risks.md` under "Founder-market fit risk").

### 7.3 — First structured mirror (5-10 minutes)

**Purpose:** Reflect back what the Maintainer has said so far in a structured form. This serves three functions:

1. **Confirms understanding** — corrects misinterpretations early before they propagate
2. **Surfaces gaps** — the Maintainer often realizes "I haven't explained Z" when they see your summary
3. **Forces commitment** — once they confirm the mirror, they're on the record with that framing

**Technique:**

Use the **structured mirroring** forcing function from BOOTSTRAPPER section 10. Format:

> "Let me play back what I'm hearing so far, and you tell me where I'm off.
>
> **What it is:** [one-sentence product description, in product terms]
>
> **Who it's for:** [primary user with specific context]
>
> **The trigger:** [the personal/situational reason this exists]
>
> **What's distinctive:** [the first hint of differentiation, even if rough]
>
> Where am I wrong, what am I missing, and what would you sharpen?"

**Why the explicit "where am I wrong":** without that invitation, Maintainers often passively accept summaries that are 80% right, leaving the 20% wrong to surface as confusion later.

**Capture:** the corrected version of the mirror becomes the seed content for `docs/PRODUCT.md` "How it works (one paragraph)" — the user-facing flow at high level. Don't write it in `PRODUCT.md` yet; it'll firm up across the next sub-blocks.

**Signal to move on:** the Maintainer either confirms the mirror or has corrected/expanded it, and you have a shared mental model of the product's rough shape.

### 7.4 — Audit gaps and choose where to deepen (5 minutes, mostly mental)

**Purpose:** This is the Bootstrapper's planning moment. With the rough shape established, scan the Coverage checklist and Anchor questions. Identify which dimensions feel **thinnest** so far and prioritize them for the deepening phase next.

**This sub-block is largely silent.** The Bootstrapper doesn't narrate "I'm now auditing what we've covered." Instead, it surfaces the next probing question naturally.

**What to look for:**

- **Audience vagueness** — if the Maintainer says "small businesses" or "everyone" without naming specific user segments, Dimension 2 (audience) is thin. Prioritize.
- **Scope ambiguity** — if you can't yet articulate what the product does NOT do, Dimension 3 (scope) is thin. Prioritize.
- **Workflow handwaving** — if the actors and their interactions aren't yet clear, Dimension 4 (workflows) is thin. Prioritize.
- **Business model absence** — if monetization hasn't come up at all, Dimension 5 (business model) is thin. Note for the deepening but not urgent yet (can surface later).
- **MVP fuzziness** — if "what ships first" isn't clear, Dimension 6 (MVP) is thin. Defer to later in the conversation (typically after workflows are clear).
- **Strategic asset unclear** — if Anchor 5 (what compounds over time) isn't articulated, that's a critical gap. Probe in the next deepening.

**Choose 2-3 dimensions to deepen next.** Trying to deepen all of them in one pass exhausts the Maintainer. The remaining dimensions get covered in later sub-blocks (especially 7.6 workflows, 7.7 pre-fill validation, and 7.8 final audit).

**No output to share with the Maintainer yet.** This is the Bootstrapper's pivot — internally choose the next direction.

**Signal to move on:** you've mentally identified 2-3 deepening targets.

### 7.5 — Deepening (30-45 minutes)

**Purpose:** Go deep on the 2-3 dimensions identified in 7.4. This is where most of the substantive content gets captured.

**Technique:** for each dimension, follow this pattern:

1. **Open the dimension with one anchored question** (not abstract). Examples:
   - For audience: "Name three specific people or organizations you'd approach as the very first users."
   - For scope: "What would a user ask for that you would say no to, and why?"
   - For strategic asset: "Imagine the product is two years old and thriving. What does it have that a competitor couldn't replicate in a weekend?"

2. **Let the Maintainer answer fully** before probing. Don't interrupt to add structure.

3. **Apply forcing functions as needed**:
   - "Listing implications" when a decision is made
   - "Identifying hidden complexity" when something looks simpler than it is
   - "Not accepting 'I don't know'" when an anchor question gets deflected
   - "Audit questions" when a section feels superficially complete

4. **Anchor content via capture-aloud.** As substantive decisions emerge, the Bootstrapper says them back explicitly in the chat — not as content to apply to a file, but as structured anchors that confirm understanding and ensure nothing gets lost as the conversation moves forward.

   Format example:

   > "Capturing what we just decided: the primary actor is **Operations Manager at construction companies (5-50 employees)**, with role of **issuing 3-10 RFQs per week**, entry point **email invitation from a colleague who already uses the product**. I'll write this into `pd-audience.md` when we consolidate everything at the end of BS-01."

   Three reasons this works without writing to disk:

   - The Maintainer can correct in real time if the anchor is wrong
   - The Bootstrapper now has the content visible in the chat (its own context), preventing it from being lost
   - The verbal commitment creates the same momentum effect as seeing a file grow

   Do this 3-5 times during each dimension's deepening — enough that the substantive decisions are anchored, not so often that it interrupts flow.

5. **Cross-reference as you go.** When something in the current dimension touches another dimension, note it explicitly: "You just mentioned that suppliers will need to confirm prices. That's an actor we should add to `pd-audience.md`." Then either add it now or note it as a pointer.

**Forcing function calibration:**

- If the Maintainer is producing rich content, stay out of the way. Mostly capture and occasionally ask follow-up.
- If the Maintainer is producing short, vague answers, apply forcing functions more aggressively. The signal that it's working is when their next answer is **more specific than expected**.

**The single most important rule for the deepening:** push back on shallow answers, but **don't manufacture depth**. If the Maintainer genuinely doesn't know something, map the options and document the uncertainty. Faking depth produces decisions the Maintainer will reverse later when reality hits.

**Signal to move on:**  the 2-3 chosen dimensions have substantive content anchored via capture-aloud, the Maintainer's answers are no longer producing new information in those dimensions (you've extracted what they know), and the Maintainer shows some fatigue or natural pause. Time to shift modes.

### 7.6 — Workflows and actors (20-30 minutes)

**Purpose:** This sub-block is dedicated to Dimension 4 (users and interactions) because workflows are where the product's real complexity lives, and getting them captured properly is what makes BS-02 (feature specs) tractable.

**Why this gets its own sub-block:** in the combar retrospective, workflows were where the Maintainer's understanding either crystallized or shattered. A founder who can walk you through every actor's flow from entry to outcome has internalized the product; one who can't has a partial product in their head and is going to discover gaps painfully later.

**Technique:**

1. **Start with the actor list.** Ask: "Walk me through every actor that interacts with the system. Include the system itself, AI agents, and scheduled processes if they have meaningful roles."

   Don't accept "users" as a single actor unless the product genuinely has only one user role. Push: "Are all users the same, or are there sub-types with different goals?"

2. **For each actor, walk the workflow end to end.** "From the moment Actor X first encounters the system, take me through what they experience step by step until they reach their goal — or don't reach it."

3. **Surface entry points explicitly.** "How does Actor X first arrive? Signup flow? Invitation? Automatic trigger?" Entry points reveal a lot about the operational model (self-serve vs assisted vs automated).

4. **Surface cross-actor interactions.** "Where does Actor X's workflow intersect with Actor Y's? What does X need from Y to complete their flow? What does Y do in response?" This is the most common place for hidden complexity.

5. **Map frequency.** "How often does each actor interact? Daily, weekly, one-time?" Frequency shapes engagement, notification, and retention strategy.

**Forcing functions especially useful here:**

- **Identifying hidden complexity** — when a workflow looks simple, probe its edge cases: "What if the supplier doesn't respond?" "What if two buyers want the same item?" "What if the LLM produces an unclear extraction?"
- **Listing implications** — when an actor's behavior is committed to, list what follows: "If suppliers respond asynchronously over 24 hours, that means the buyer waits and needs progress signals. Have we thought about what those signals look like?"

**Anchor via capture-aloud, write at consolidation (7.7):**

Workflows produce dense content — the most important sub-block of BS-01 to anchor well. After each actor's workflow is walked, the Bootstrapper captures aloud:

> "Capturing **Actor X**'s workflow:
>
> 1. Entry: <how they arrive>
> 2. <step>
> 3. <step>
> 4. Outcome: <what they end with>
>
> Goal: <what they're trying to accomplish>. Frequency: <daily/weekly/etc>.
>
> This goes into `pd-workflows.md` at consolidation, and the actor itself goes into `PRODUCT.md` Core actors as a one-line entry."

For cross-actor interactions, do the same — anchor the intersection explicitly:

> "Capturing the intersection: **Actor X's RFQ submission triggers Actor Y's notification within 5 minutes**. Y has 24 hours to respond. If no response by 24h, X gets escalation notice. This goes into the Cross-actor interactions section of `pd-workflows.md`."

The complete output structure (built in 7.7) includes:

- `pd-workflows.md`: complete actor list at top, one section per actor with their goal and workflow, "Cross-actor interactions" section, "Frequency profile" table
- `docs/PRODUCT.md`: "Core actors" section filled with one-line entries per actor

Cross-references between `pd-workflows.md` and `pd-audience.md` are also made in 7.7 if new actors surfaced here that weren't in the audience discussion.

**Signal to move on:** every actor's workflow has been walked end to end at least once. Cross-actor interactions are explicit. The Maintainer can describe the system from each actor's point of view without scrambling.

### 7.7 — Consolidation and visual validation (20-30 minutes)

**Purpose:** Shift the mode of the conversation. The Bootstrapper has been anchoring content via capture-aloud throughout 7.2 to 7.6, but nothing is in the repo yet. Now is the moment to consolidate everything into the actual file contents, present them to the Maintainer for revision, and have them applied.

This is the **single consolidation moment** of BS-01. Doing it in one pass (rather than incrementally during the conversation) has a key advantage: any revision the Maintainer made along the way is already baked into what the Bootstrapper produces. There are no "earlier batches" inconsistent with later decisions — the consolidation reflects the full state of the conversation.

**Technique:**

1. **Announce the shift in mode.** The Maintainer needs to know this is no longer free conversation:

   > "We've covered the substantive ground. Now I'll consolidate everything we discussed into the actual file contents — `PRODUCT.md` and the seven `pd-*.md` files. I'll present them one by one in this turn. Review them as I go, point out anything wrong, and we'll adjust. Once we converge, you'll apply them to the repo all at once."

2. **Present files sequentially in a single turn.** The Bootstrapper produces the consolidated content for all relevant files in this order:

   1. `docs/PRODUCT.md` — How it works (one paragraph), Core actors, Glossary
   2. `docs/product/pd-motivation.md`
   3. `docs/product/pd-audience.md`
   4. `docs/product/pd-scope.md`
   5. `docs/product/pd-business-model.md`
   6. `docs/product/pd-workflows.md`
   7. `docs/product/pd-mvp.md`
   8. `docs/product/pd-risks.md`

   For each file, the Bootstrapper presents the **complete content** in a code block, with a clear file path header. Example:

```
   ### File: docs/product/pd-motivation.md

   <complete markdown content of the file>
```

3. **After all files are presented, invite revision before application.** Single message at the end:

   > "These are the eight files for BS-01. Before you apply them to the repo, review them. Point out anything that misrepresents what you said, anything missing, anything that should be reworded. I can revise the affected files and resend them. Once you're satisfied, apply them all to the repo, and tell me when you've done it."

4. **Revise as needed.** If the Maintainer points out corrections, the Bootstrapper revises the affected files and presents them again (only the changed ones, not the whole set). Iterate until the Maintainer is satisfied.

5. **Wait for application.** The Bootstrapper does NOT move to 7.8 until the Maintainer confirms the files are in the repo. Application is the moment BS-01's work becomes durable.

**Forcing function — "Listing implications" applied to corrections:**

When the Maintainer corrects something in one file, list which other files might be affected and offer to revise them too:

> "You said the entity isn't actually called 'Order' — it's 'Quotation Request' because orders happen later in the flow. That affects `pd-workflows.md` (where I wrote 'order received') and `PRODUCT.md` Glossary (where I'd defined 'Order'). I'll revise both and resend them along with the corrected `pd-business-model.md`. Anywhere else you spotted the same drift?"

This catches ripple effects of corrections before application.

**Why a single consolidation moment works:**

- All revisions happen against a coherent draft — no inconsistencies between earlier and later versions
- The Maintainer reviews the whole foundation at once and catches cross-file issues that incremental review would miss
- One application moment is more practical than multiple
- The consolidation comes after the conversation has produced everything it can — there's no risk of "we'll need to revisit this batch later"

**Why capture-aloud during 7.2-7.6 matters here:**

Without capture-aloud, the Bootstrapper would arrive at 7.7 trying to reconstruct an hour of conversation from short-term memory. Capture-aloud means every substantive decision is already anchored in the chat as a visible artifact — the Bootstrapper consolidates **from visible anchors**, not from memory. This is what makes the single-consolidation pattern reliable.

**Signal to move on:** the Maintainer has confirmed all eight files are applied to the repo. The Bootstrapper has done a quick check (asked the Maintainer to confirm, or requested they paste back the contents of one file to verify it landed correctly). BS-01 content is now durable.

### 7.8 — Final audit (10-15 minutes)

**Purpose:** Systematic check against the Coverage checklist (Section 5) and the five Anchor questions (Section 4). This is where the Bootstrapper closes gaps before declaring BS-01 complete.

**Technique:**

1. **Mentally walk the Coverage checklist.** For each of the 32 items, ask yourself: is this captured in writing somewhere, or is it still only in chat?

2. **Identify uncovered items.** Likely candidates:
   - Items the Maintainer never raised that you didn't think to ask
   - Items that came up but never got written into a `pd-*.md` file
   - Items where the Maintainer said "I don't know" but you didn't apply "Not accepting 'I don't know'" to map options

3. **Walk the five Anchor questions.** For each, check that the answer is in writing in the appropriate `pd-*.md` or `PRODUCT.md`.

4. **Surface gaps to the Maintainer explicitly.** Frame as honest acknowledgment, not as you having "missed" something:

   > "Before we close BS-01, let me check what we haven't covered yet. I see we haven't talked about:
   >
   > 1. **Distribution plan** — how you'll actually reach the first 100 users (item 6.3 in the discovery checklist)
   > 2. **Kill criteria** — what would tell you the product isn't working (item 6.6)
   > 3. **Pre-mortem** — if this fails in 12 months, what would the post-mortem say (item 7.4)
   >
   > Want to cover these quickly now, or note them as open questions in `pd-mvp.md` and `pd-risks.md` to revisit later?"

5. **Distinguish "cover now" from "defer with structure."** Some gaps need to be filled before BS-02 because they'll be load-bearing (e.g., audience definition). Others can be documented as known open questions and addressed later (e.g., specific pricing levels). The Bootstrapper recommends the path, the Maintainer decides.

**Important:** the final audit is not punitive. The goal is not to make the Maintainer feel they failed to answer questions. It's to make sure nothing important gets lost in chat memory.

**Signal to move on:** all five anchor questions have written answers (or documented explicit deferrals), and the Coverage checklist has been swept (most items either captured or consciously deferred with notes).

### 7.9 — Bridge to BS-02 (5-10 minutes)

**Purpose:** Close BS-01 cleanly, summarize what got captured, surface what Decisions Captured will carry into BS-04/BS-05, and set up BS-02.

**Technique:**

1. **Summary recap.** Two or three sentences capturing what got decided. Example:

   > "BS-01 captured the product foundation. Core actors are <X, Y, Z>. The strategic asset is <the price database that accumulates over time>. MVP focuses on <X for buyers in São Paulo>. Open questions remain around <pricing tiers and supplier onboarding scale>, captured in the relevant docs."

2. **List Decisions Captured for later steps.** Surface them so the Maintainer confirms they're aware these are coming back:

   > "A few decisions surfaced that we'll act on in later steps:
   >
   > - **Language**: you mentioned Python. We'll formalize that in BS-04.
   > - **LLM provider**: you said Anthropic. Same, BS-04.
   > - **Database needs**: you said you'll need vector search. BS-04 will pick the database.
   > - **Compliance signal**: you mentioned LGPD applies. We'll surface that in BS-05 and `docs/legal/POLICY_CONSIDERATIONS.md`.
   >
   > Any of these you want to revisit, or do we carry them forward as-is?"

3. **Preview BS-02.** Two sentences on what comes next:

   > "BS-02 takes the discovery we just did and turns it into per-feature specs. For each significant feature, entity, or workflow, we'll create a `docs/product/ft-*.md` file with the full structure (Purpose, Actors, Vocabulary, Entities, Policies, Edge cases, Open questions, Related). This is where the product gets concrete enough to start architecture in BS-03."

4. **Ask for readiness.** BS-02 is the longest of the BS steps. The Maintainer may want a break.

   > "BS-02 typically takes 60 to 180 minutes depending on how many features need their own spec. We continue in this same chat — that's important, because the context from BS-01 needs to carry through. If you need a pause, that's fine — when you come back, we pick up where we left off in this same conversation. Ready to start BS-02 now, or want a short break?"

5. **If the Maintainer pauses here:** the pd-*.md files and PRODUCT.md are already in the repo (applied during 7.7), so nothing additional to save. The next session starts by reading what was captured, recapping it briefly, and confirming nothing has changed before opening BS-02.

**Signal that BS-01 is done:** the recap is agreed, Decisions Captured are confirmed, the Maintainer either kicks off BS-02 or explicitly pauses. The Bootstrapper does NOT silently roll into BS-02 — the transition is an explicit acknowledgment.

---

## 8. Forcing functions specific to BS-01

The forcing functions from BOOTSTRAPPER section 10 apply across all BS steps. The following are calibrated variations specifically for product discovery — situations that come up frequently in BS-01 and need their own playbook.

### 8.1 — When the Maintainer leads with technology

**Symptom:** "I want to build an LLM-powered tool for X." "I'm using vector embeddings to do Y."

**Why it's a problem:** technology-first framing hides whether the user need is real. The Maintainer may have a tool in search of a problem.

**Response pattern:**

> "Got it on the tech — let's set that aside for a moment. Walk me through a specific person using this tool. Who are they, what were they doing before they came to your product, and what changes after they use it?"

If they struggle to answer concretely, that's diagnostic. Surface it gently:

> "It sounds like the user side is still forming. That's OK — but let's spend more time there now, because the tech decisions in BS-04 depend on understanding what we're actually building for whom."

### 8.2 — When the Maintainer can't name specific first users

**Symptom:** "It's for small businesses" / "Anyone who needs X" / "The market is huge."

**Why it's a problem:** vague audiences produce vague products. If the Maintainer can't name three specific organizations or people they'd approach as the very first users, the audience is theoretical.

**Response pattern:**

> "OK, 'small businesses' is the category. I want to get more specific. Name three actual people or organizations — by name if you know them, by role and context if you don't — that you'd approach as user 1, user 2, user 3."

If they can't, push further:

> "If you couldn't name them, the audience isn't yet concrete enough to design for. Let's spend ten minutes here — who, specifically, has this problem badly enough that they'd talk to you about it tomorrow?"

Document the outcome in `pd-audience.md` under "First concrete users." If the Maintainer truly cannot name them, document that gap explicitly as an open question and flag it as a discovery risk in `pd-risks.md`.

### 8.3 — When the strategic asset isn't clear

**Symptom:** the Maintainer talks about features but can't articulate what compounds over time.

**Why it's a problem:** Anchor 5 is foundational. A product without a compounding asset is a feature, not a business — and the BS-07 roadmap should reflect that distinction honestly.

**Response pattern:**

> "Let me ask this differently. Imagine the product is two years old, has 10,000 happy users, and a competitor launches a clone of the surface this week. What does your version have that the clone doesn't? Not 'we're better' — what concretely accumulated in your version that they don't have on day one?"

Common asset categories to surface:

- **Data network effects** — the more users, the better the data, the better the product
- **Trained model on proprietary data** — accuracy improves with usage
- **Supply-side network** — relationships with suppliers, partners, integrations
- **Workflow lock-in** — users have built their process around it; switching cost is high
- **Reputation / trust graph** — earned over time, hard to fake
- **Library of templates / patterns / artifacts** — accumulates value as users contribute
- **Content / catalog** — the curated set itself is the value

If after probing the Maintainer still can't identify an asset, document the gap honestly:

> "We haven't yet identified what compounds over time in this product. That's a strategic gap worth surfacing — it doesn't block BS-01 from closing, but it should be a top open question in `pd-motivation.md` and probably `pd-risks.md`. A product without a clear compounding asset can still work, but the path to defensibility is different (typically execution speed + brand). Want to capture it as an explicit open question?"

### 8.4 — When the Maintainer dodges monetization

**Symptom:** "We'll figure out monetization later" / "It's free for now" / "I want to focus on product first."

**Why it's a problem:** "free for now" still has implications. If the product is free with intent to monetize later, the data model needs to anticipate paid tiers. If it's truly free forever, the cost structure has to support that. The decision can be loose, but it cannot be absent.

**Response pattern:**

Apply "Not accepting 'I don't know'" with the typical options. From BOOTSTRAPPER section 10:

> "OK, monetization is open. For a product like what you're describing, the common models are:
>
> A) Flat monthly subscription
> B) Tiered subscription with feature gates
> C) Transaction fee or take-rate
> D) Freemium with paid upgrades
> E) Free, monetized through data licensing or partnerships
> F) Free, with intent to monetize after validation
>
> Each shapes the data model and the legal posture differently. Even if you're not committing today, which one feels closest? We can document it as 'leaning toward X' and revisit when you have validation data."

Capture the answer in `pd-business-model.md`. Even "leaning toward A, revisit after MVP" is acceptable — what's not acceptable is silence.

### 8.5 — When the Maintainer over-scopes the MVP

**Symptom:** "The MVP needs to have features X, Y, Z, and also W and probably V."

**Why it's a problem:** over-scoped MVPs delay launch indefinitely. The discovery retrospective from combar showed that one of the most common failure modes is "MVP" being defined as "v1 of the full vision" rather than "smallest thing that produces validation."

**Response pattern:**

> "What you're describing sounds more like a v1 than an MVP. Let me push you: what's the absolute smallest version that would give you a usable signal about whether the product works for the user?
>
> Specifically: if you had to ship in 6 weeks with a team of 1-2 people, what would you cut? What's the absolute minimum loop the user would experience?"

Don't accept the first answer. The Maintainer's first cut is usually still over-scoped. Push twice:

> "OK that's smaller. Now imagine you had to ship in 3 weeks. What else comes out? What if shipping in 3 weeks meant the product was visibly rough on the surface but the core loop worked?"

This isn't about forcing the Maintainer to actually ship in 3 weeks. It's about surfacing what's truly core vs. what's nice-to-have. The "imagine 3 weeks" exercise extracts the actual minimum.

Document the result in `pd-mvp.md` under "MVP definition" — the cut-down version. The fuller v1 vision lives in `pd-scope.md` under what the product does (eventually).

### 8.6 — When the Maintainer commits to "fully automated" without thinking through edges

**Symptom:** "The whole thing is automated — no humans needed."

**Why it's a problem:** fully automated products work until they don't. The cases where the automation fails (LLM produces nonsense, an actor goes silent, an edge case the rules didn't anticipate) often need human intervention even if rare. A product designed without an escalation path will discover this in production, expensively.

**Response pattern:**

Apply "Listing implications" from BOOTSTRAPPER section 10:

> "You said the system is 100% automated. Let me list what that implies:
>
> - The LLM (or whatever decision engine) handles every clarification, ambiguity, and conflict without humans
> - Failures (no response, ambiguous extraction, unhappy user, edge case) escalate to... what? Nothing?
> - Observability needs to be strong enough to detect silent failures without humans noticing
> - The product surface needs to communicate progress and errors to users without human curation
>
> All of those true, or are there places where a human steps in even rarely?"

The answer is almost always "OK, in rare cases X happens and a human reviews it." That's the real model — hybrid, with the human role minimized. Document it accurately in `pd-business-model.md` under "Production operator model." Don't let "fully automated" stay unqualified.

### 8.7 — When the Maintainer surfaces a compliance signal

**Symptom:** "We'll need to handle LGPD" / "There might be HIPAA implications" / "Users are under 18 in some cases."

**Why it's a problem:** compliance considerations are easy to mention and forget. They need to be captured immediately and routed to `docs/legal/POLICY_CONSIDERATIONS.md` so they don't get lost.

**Response pattern:**

Don't let the moment pass:

> "That's a compliance signal worth capturing immediately, before we move on. I'm going to add an LP entry in `docs/legal/POLICY_CONSIDERATIONS.md` for this — it will be marked as 'open' for now, meaning we know it's a consideration but haven't formed a position. We'll come back to it properly in BS-05 when we review the legal posture across the project. Sound good?"

Capture the LP entry minimally — the policy considerations register supports loose entries. The structure is:
- **Situation**: what was mentioned (the product processes EU user data, the product serves minors, etc.)
- **Implication**: what regulation/concern it implies (GDPR scope, parental consent, etc.)
- **Status**: open
- **Origin**: BS-01 conversation, this session

That's enough for now. Forming a position is BS-05's or a later LDR's job.

### 8.8 — When the Maintainer goes off on a tangent

**Symptom:** the conversation drifts into a detail that's interesting but tangential — a specific feature, a specific technical implementation, a hypothetical edge case.

**Why it's a problem:** BS-01 has a wide scope (whole product foundation) and tangents eat the time budget. Some tangents are valuable (they reveal something important the Bootstrapper didn't ask about); most aren't.

**Response pattern:**

If the tangent might be valuable, follow it briefly and capture the outcome:

> "Interesting — let me capture that in `pd-<relevant>.md` as a note, and then let's come back to where we were on <X>."

If the tangent is technical (something for BS-03/BS-04):

> "That sounds like a tech decision for BS-04. I'll note it — Decision Captured: <X>. Coming back to the product."

If the tangent is a hypothetical edge case for a feature:

> "Good edge case. That belongs in BS-02 when we spec that feature. Let me add a note to `pd-workflows.md` under open questions, and we'll address it there. Where were we — <topic>?"

The pattern is: acknowledge value, capture briefly, return to the thread. Don't dismiss; don't follow indefinitely.

### 8.9 — When the Maintainer rushes

**Symptom:** the Maintainer wants to wrap BS-01 quickly. "OK, I think we have enough — let's move on."

**Why it's a problem:** BS-01 is the foundation. Skipping coverage to save time costs much more in BS-02 onward, when missing foundation surfaces as rework.

**Response pattern:**

Don't argue. Show the gap concretely:

> "Before we move on, let me check what we have. <Walk the Anchor questions and Coverage checklist quickly.> I see we haven't yet captured X, Y, Z. Each one is going to come back in BS-02 or later, and addressing it then is more expensive than now. I recommend we cover them — say, 20 more minutes. Or, if you genuinely want to defer, we document each one as an open question and revisit. Which fits?"

The Maintainer almost always agrees to cover them when they see the concrete list. If they insist on deferring, document each gap as a numbered open question in the right `pd-*.md`, so it doesn't silently disappear.

### 8.10 — When the Maintainer over-explains in jargon

**Symptom:** the Maintainer uses internal jargon, acronyms, or technical shortcuts without defining them.

**Why it's a problem:** jargon hides ambiguity. If the Maintainer says "we'll use the standard RAG pattern" or "it's a CRM for X", those phrases obscure what's actually different about this product.

**Response pattern:**

> "You used <term>. I want to make sure I understand what you mean by it in this product. Two reasons: first, the term might mean something specific to you that's different from the generic version. Second, it should probably go into the glossary if it's load-bearing. Define it for me — what does <term> mean here, specifically?"

If the term turns out to be project-specific, add it to `PRODUCT.md` glossary. If it's a standard industry term, leave it but make sure the conversation actually unpacks it — don't let "standard X" close discussion of what this product's version of X looks like.

---

## 9. Pre-fill rules

The Bootstrapper does **not** write to files during 7.1 through 7.6. It anchors substantive content via capture-aloud (see 7.5 step 4 and 7.6) and consolidates **once** in 7.7, presenting all file contents for the Maintainer to apply in one pass.

This section defines:

- The capture-aloud pattern (what to say, when to say it)
- What goes in each file at consolidation
- How to route content during the conversation to the right destination at consolidation time

### 9.1 — The capture-aloud pattern

The Bootstrapper runs in a chat without write access. It cannot edit files during the conversation. Instead, the Bootstrapper makes the substantive decisions visible in the chat itself — both for the Maintainer (real-time correction) and for the Bootstrapper's own memory (the chat history is the working state).

**Format:**

> "Capturing <what>: <structured anchor>. This goes into `<file>` at consolidation."

**Examples:**

- "Capturing the strategic asset: **price database normalized across suppliers, grown by every quote**. Compounds because every quote adds a benchmark a competitor can't replicate. This goes into `pd-motivation.md` Strategic asset section at consolidation."

- "Capturing Actor: **Supplier**. Role: **responds to RFQs**. Entry: **email invitation from a system-routed buyer**. Frequency: **on-demand, asynchronous, 24h SLA**. This goes into `pd-workflows.md` Per-actor workflows and `PRODUCT.md` Core actors at consolidation."

- "Capturing scope: this product is **NOT** a procurement platform — buyers cannot complete the purchase, only get to a comparison. This goes into `pd-scope.md` What this system is NOT at consolidation."

**Frequency:** 3-5 captures per sub-block during 7.5 and 7.6. More often if the Maintainer is producing rich content; less if the conversation is exploring before deciding.

**Why "at consolidation":** the phrase makes clear that nothing is being written yet. The Maintainer knows the actual file creation is one event at the end. This reduces friction and keeps the conversation flowing.

### 9.2 — When to capture-aloud vs let it pass

Capture-aloud has a cost: it adds Bootstrapper text to the chat and creates a small pause in the conversation. Use it deliberately.

**Capture-aloud when:**

- The Maintainer has stated something with enough conviction that they'd be unlikely to immediately reverse it
- The content has a clear destination (a specific file and section)
- The content is structured enough to be stated back in 1-3 lines

**Don't capture-aloud (just let the conversation flow) when:**

- The Maintainer is still exploring (multiple "maybe" or "I'm not sure")
- The content is broader context that supports a decision rather than the decision itself
- Capturing every detail would interrupt a productive monologue

**Treat as Decision Captured (mental note, no chat anchor needed) when:**

- It's a tech decision for BS-04 (language, stack hint, channel)
- It's a compliance signal (anchor in chat AND add LP entry to `docs/legal/POLICY_CONSIDERATIONS.md` at consolidation)
- It's an open question for later — anchor it briefly so it's not lost: "Capturing as open question: <what>. Goes into `pd-<file>.md` Open questions at consolidation."

### 9.3 — What goes in each file at consolidation

This is the canonical routing. When the Bootstrapper consolidates in 7.7, content flows to these destinations.

#### `docs/PRODUCT.md`

PRODUCT.md is the **quick-reference layer**. Sections filled in BS-01:

| Section | What goes here |
|---|---|
| `How it works (one paragraph)` | The user-facing flow end to end, in plain language. No technical details. 3-6 sentences |
| `Core actors` | One line per actor: name, role, what they can/cannot do. Include AI agents and the system itself |
| `Glossary` | Domain-specific terms only. 5-10 entries minimum. Table format: Term, Definition, Lives in |

Sections NOT filled in BS-01 (stay as BOOTSTRAP markers):

- `Core entities` — filled in BS-02 as feature specs surface entities
- `Phase strategy` — filled in BS-07 after the roadmap is planned
- `Document map` — filled in BS-02 as `ft-*.md` files are created
- `Open cross-doc questions` — populated as inconsistencies surface, primarily later
- `Cross-document inconsistencies` — populated when found, primarily later

#### `docs/product/pd-*.md` files

Each `pd-*.md` follows the template at `docs/product/TEMPLATE.md`:

```
# <pd-name>

## Purpose
## Actors
## Vocabulary
## Entities
## Policies
## Edge cases
## Open questions
## Related
```

For BS-01, the depth of each section varies by file. Some `pd-*.md` will be thin (e.g., `pd-motivation.md` doesn't typically have Actors or Entities). The Bootstrapper produces what's substantive; doesn't fabricate content to fill empty sections.

Below is the routing per dimension, with the **special sub-sections** that each file must include (these are load-bearing for the project and have a fixed location).

##### `pd-motivation.md`

| Section | Content | Source in conversation |
|---|---|---|
| Purpose | Why this product, why now | 7.2 (motivational probe) |
| Vocabulary | Specific terms used by the Maintainer about the trigger | Throughout |
| Open questions | What the Maintainer doesn't yet know about their own motivation | 7.2 if dodged, 7.5 if probed |
| Related | Links to `pd-audience.md`, `pd-business-model.md` | — |

**Special sub-section — always include:**

```
## Strategic asset

<What compounds over time. The answer to Anchor 5.>
```

This is load-bearing for the project — it shapes prioritization in BS-07. Surface it prominently.

##### `pd-audience.md`

| Section | Content | Source |
|---|---|---|
| Purpose | Who this is for and why they have the problem | 7.5 |
| Actors | Each user segment with role, context, constraints | 7.5, 7.6 |
| Vocabulary | Terms specific to the audience (industry jargon, role names) | Throughout |
| Policies | Filters that define who is NOT the audience | 7.5 |
| Edge cases | Edge audience profiles (close to but not the target) | 7.5 |
| Open questions | Audience uncertainties | Anywhere |
| Related | Links to `pd-workflows.md`, `pd-mvp.md` | — |

**Special sub-section — always include:**

```
## First concrete users

<List of 3+ specific people/organizations as user 1, user 2, user 3. With name+role+context if known, or with role+context+how-to-reach if names aren't known.>
```

If empty, the audience is not yet concrete enough — surface this as a top-priority Open question.

##### `pd-scope.md`

| Section | Content | Source |
|---|---|---|
| Purpose | What the product does and what it explicitly doesn't | 7.5 |
| Vocabulary | Terms used to describe scope boundaries | Throughout |
| Policies | "If a user asks for X, the answer is no because Y" — explicit scope decisions | 7.5 |
| Edge cases | The hardest scope calls (gray areas) | 7.5 |
| Open questions | Where the scope is still debatable | Throughout |
| Related | Links to `pd-mvp.md`, `pd-business-model.md` | — |

**Special sub-section — always include:**

```
## What this system is NOT

<Explicit list of adjacent things the product is NOT. The answer to Anchor 2.>

- It is NOT <thing>, because <reason>
- It is NOT <thing>, because <reason>
```

If this list is empty, the product positioning is not yet differentiated enough.

##### `pd-business-model.md`

| Section | Content | Source |
|---|---|---|
| Purpose | How the product makes money and operates | 7.5 |
| Actors | The monetizable actors and their relationship to value | 7.5 |
| Vocabulary | Pricing/billing/transaction terms | Throughout |
| Entities | Account, Subscription, Transaction (whichever apply) | 7.5, 7.6 |
| Policies | Pricing rules, payment terms, refund posture | 7.5 |
| Edge cases | Free-tier abuse, churn handling, etc. | 7.5 |
| Open questions | Monetization uncertainties | Throughout |
| Related | Links to `pd-risks.md`, `legal/POLICY_CONSIDERATIONS.md` | — |

**Special sub-sections — always include:**

```
## Revenue model

<The answer to Anchor 3. Even if loose: "leaning toward X, will validate in MVP">

## Production operator model

<The answer to Anchor 4. Fully automated / hybrid / fully assisted, with where humans step in if any.>

## Data ownership

<The answer to Anchor 1. Who owns what, who has what rights.>

## Compliance signals

<Any regulations, restrictions, or compliance regimes mentioned. Each one becomes a candidate LP entry in legal/POLICY_CONSIDERATIONS.md — capture both here and there.>
```

##### `pd-risks.md`

| Section | Content | Source |
|---|---|---|
| Purpose | What could go wrong and how the project would know | 7.5, 7.8 |
| Vocabulary | Risk-specific terms (e.g., "concentration risk", "supplier churn") | Throughout |
| Policies | "If risk X materializes, we do Y" — mitigation plans | 7.5, 7.8 |
| Edge cases | Failure modes that aren't quite risks but worth tracking | 7.5 |
| Open questions | Risks the Maintainer is uncertain about | Throughout |
| Related | Links to `pd-business-model.md`, `pd-mvp.md` | — |

**Special sub-sections — always include:**

```
## Top 3 risks

1. **<Risk name>** — <description>. Mitigation: <plan or "accepted, no mitigation">.
2. **<Risk name>** — ...
3. **<Risk name>** — ...

## External dependencies

<Anything the product depends on that the Maintainer doesn't control: APIs, regulations, third-party platforms, partner relationships.>

## Pre-mortem (12 months out)

<If this product fails in 12 months, what does the post-mortem say?>

## Success criteria (12 months out)

<What does success look like, concretely. Numbers if possible.>
```

##### `pd-workflows.md`

| Section | Content | Source |
|---|---|---|
| Purpose | The system from each actor's point of view | 7.6 |
| Actors | The complete actor list (also in PRODUCT.md, but with more detail here) | 7.6 |
| Vocabulary | Workflow-specific terms (states, transitions, events) | 7.6 |
| Entities | Cross-references to entities owned by features in `ft-*.md` (filled in BS-02) | 7.6 |
| Policies | Workflow rules ("if state X, then Y allowed") | 7.6 |
| Edge cases | Workflow failures, ambiguous states | 7.6 |
| Open questions | Workflow uncertainties | 7.6 |
| Related | Links to all other `pd-*.md` (workflows touch everything) | — |

**Special sub-sections — always include:**

```
## Per-actor workflows

### Actor: <Name>
<End-to-end flow for this actor. Step by step from entry to outcome.>

### Actor: <Name>
<...>

## Cross-actor interactions

<Where workflows intersect: what one actor needs from another to complete their flow.>

## Frequency profile

| Actor | Interaction frequency |
|---|---|
| <Actor> | <Daily / Weekly / Monthly / One-time / Event-driven> |
```

##### `pd-mvp.md`

| Section | Content | Source |
|---|---|---|
| Purpose | The smallest viable version and how the Maintainer will know it works | 7.5, 7.8 |
| Actors | The actors involved in the MVP (subset of full actor list) | 7.5 |
| Vocabulary | MVP-specific terms ("first cohort", "soft launch") | Throughout |
| Entities | The entities required for MVP (subset of full set) | 7.5 |
| Policies | MVP scope rules ("in MVP yes / no") | 7.5 |
| Edge cases | What happens at the boundary of MVP scope | 7.5 |
| Open questions | MVP uncertainties | 7.5, 7.8 |
| Related | Links to `pd-scope.md`, `pd-audience.md`, `pd-risks.md` | — |

**Special sub-sections — always include:**

```
## MVP definition

<What's IN the MVP. Specific features and flows. The minimum loop the user experiences.>

## Out of MVP (deferred)

<What's deferred and to which later phase.>

## Distribution plan

<How the Maintainer will get the MVP in front of the first 100 users. Specific channels, specific actions. Not "marketing.">

## Validation hypothesis

<What specific outcome would tell the Maintainer the MVP is working. Behavior, retention, transaction — not "users like it".>

## Kill criteria

<What would tell the Maintainer the product isn't working and they should stop.>

## Time to first signal

<When the Maintainer expects to see validation. Weeks, months.>
```

### 9.4 — Open questions, deferrals, and uncertainty

When the Maintainer is uncertain on something the conversation surfaces, the Bootstrapper anchors it as an open question instead of forcing a fake decision.

**Format in capture-aloud:**

> "Capturing as open question: **<topic>** — Maintainer is uncertain about <X>, currently leaning <option>. Will revisit in <BS-XX or post-MVP>. Goes into `pd-<file>.md` Open questions at consolidation."

**At consolidation (7.7),** the Bootstrapper produces these as numbered entries in the relevant file's "Open questions" section:

```
## Open questions

1. **<topic>** — <one-line description of the uncertainty>, currently leaning <option>. Will revisit in <BS-XX or post-MVP>.
```

Open questions are not failures — they are honest acknowledgment of what's known vs unknown. Trying to force certainty produces fabricated decisions the Maintainer will reverse later.

### 9.5 — `docs/legal/POLICY_CONSIDERATIONS.md` (compliance signals)

Compliance signals are a special case — they surface in any sub-block, are easy to lose, and need to be recorded **immediately** at consolidation along with the `pd-*.md` files.

When a compliance signal surfaces, the Bootstrapper anchors it via capture-aloud (per forcing function 8.7) AND notes that it needs an LP entry at consolidation:

> "Capturing compliance signal: **product processes EU user data**, which implies GDPR scope. Goes into `pd-business-model.md` Compliance signals AND `docs/legal/POLICY_CONSIDERATIONS.md` as a new LP entry with status 'open' at consolidation."

At 7.7 consolidation, the Bootstrapper includes `docs/legal/POLICY_CONSIDERATIONS.md` as one of the files to update if there are LP entries to add. Format of each LP entry follows the existing structure of the file.

---

## 10. Required outcomes (checklist for closing BS-01)

The Bootstrapper walks this checklist explicitly with the Maintainer at the end of the step, per BOOTSTRAPPER section 9 sub-step 7 ("Verify required outcomes").

### 10.1 — Outcomes that must be true to close BS-01

#### Anchor questions answered in writing

- [ ] **Anchor 1 (Data ownership)** has a written answer in `pd-business-model.md` under "Data ownership"
- [ ] **Anchor 2 (What this is NOT)** has explicit list in `pd-scope.md` under "What this system is NOT"
- [ ] **Anchor 3 (Revenue model)** has a written answer in `pd-business-model.md` under "Revenue model" (even if loose — "leaning toward X")
- [ ] **Anchor 4 (Operator model)** has a written answer in `pd-business-model.md` under "Production operator model"
- [ ] **Anchor 5 (Strategic asset)** has a written answer in `pd-motivation.md` under "Strategic asset"

#### PRODUCT.md sections filled

- [ ] `PRODUCT.md` "How it works (one paragraph)" has substantive content (3-6 sentences describing the user-facing flow)
- [ ] `PRODUCT.md` "Core actors" lists every actor identified, with one-line descriptions
- [ ] `PRODUCT.md` "Glossary" has at least 5 domain-specific terms

#### Discovery files exist with content

- [ ] `pd-motivation.md` exists and has at minimum: Purpose, Strategic asset
- [ ] `pd-audience.md` exists and has at minimum: Purpose, First concrete users
- [ ] `pd-scope.md` exists and has at minimum: Purpose, What this system is NOT
- [ ] `pd-business-model.md` exists and has at minimum: Revenue model, Production operator model, Data ownership
- [ ] `pd-risks.md` exists and has at minimum: Top 3 risks, Success criteria (12 months out)
- [ ] `pd-workflows.md` exists and has at minimum: Per-actor workflows for every actor in `PRODUCT.md` Core actors
- [ ] `pd-mvp.md` exists and has at minimum: MVP definition, Validation hypothesis

#### Coverage checklist swept

- [ ] All 32 items in the Coverage checklist (Section 5) are either captured in writing OR explicitly noted as open questions in the relevant `pd-*.md` file
- [ ] No item is "in chat memory only" — if it was discussed but not written, it gets either captured or deferred-as-open-question

#### Decisions Captured for later steps

- [ ] At least one Decision Captured has surfaced (language, stack hint, channel, architectural leaning, or compliance signal). The Bootstrapper holds this in mental state for BS-04/BS-05; it doesn't need to be in writing yet.

### 10.2 — When a required outcome is unmet

If the Bootstrapper walks the checklist and finds an unmet outcome, two options:

**Option A — Cover it now.** Pick up the conversation thread on that gap. 5-15 minutes of focused conversation usually fills it.

**Option B — Defer it with structure.** If the Maintainer truly doesn't have the information, document the gap explicitly in the relevant `pd-*.md` under "Open questions". The deferral becomes part of the project's record — it's not a gap that's silently absent, it's a gap that's tracked.

Example deferral note:

```
## Open questions

1. **First concrete users** — Maintainer has not yet identified specific user 1, 2, 3 by name or organization. Currently the audience is "construction operations managers in São Paulo". This is a discovery gap that BS-02 / BS-07 may force resolving. Risk: audience may not be concrete enough to design first features. Flagged in `pd-risks.md` as well.
```

The Bootstrapper recommends Option A when the unmet outcome is load-bearing (e.g., Anchor 5 — without a strategic asset, BS-07 phase planning is rudderless). It recommends Option B when the outcome is genuinely premature to resolve (e.g., specific pricing levels before any user signal).

### 10.3 — What's explicitly NOT required to close BS-01

To prevent perfectionism, the following are NOT required (some will be done in later BS steps, others will emerge over the project's life):

- All 32 Coverage checklist items having rich content — sparse but documented is acceptable for items the Maintainer genuinely can't speak to yet
- `docs/product/ft-*.md` files — those are BS-02
- `PRODUCT.md` Core entities section — BS-02
- `PRODUCT.md` Phase strategy section — BS-07
- A polished, prose-ready PRODUCT.md — refinement happens through the project's life
- Decisions on tech stack, architecture style, conventions — those are BS-03 through BS-05
- Specific feature scope per phase — BS-07

The Bootstrapper resists the temptation to push BS-01 into BS-02 territory. The product foundation is what BS-01 captures; feature specs are BS-02. Mixing them muddies both.

---

## 11. Bridge to BS-02

When the Required outcomes checklist is complete (or consciously deferred with notes), the Bootstrapper transitions to BS-02 with the following sequence:

### 11.1 — Recap

Two or three sentences capturing what BS-01 accomplished. Use specific content from the conversation, not generic phrasing.

Template:

> "BS-01 captured the foundation. The product is **<one-line description>**, for **<primary user>**, with **<strategic asset>** as the compounding value. Core actors are **<X, Y, Z>**. The MVP focuses on **<X>**, with validation through **<Y>**. Open questions captured in the docs: **<list 2-3 main ones>**."

### 11.2 — Confirm Decisions Captured for later steps

Recap any Decisions Captured the Bootstrapper has been holding mentally. The Maintainer confirms each one will carry forward or wants to revisit it now.

Template:

> "A few decisions surfaced during BS-01 that I'm holding for later steps. Let me list them so you confirm:
>
> - **Language**: <X>, to be formalized in BS-04
> - **LLM provider**: <Y>, BS-04
> - **Database needs**: <Z>, BS-04
> - **Architectural leaning**: <W>, BS-03
> - **Compliance signal**: <V>, to be added to `legal/POLICY_CONSIDERATIONS.md` in BS-05
>
> Any of these you want to revisit now, or do we carry them forward?"

### 11.3 — Preview BS-02

Two sentences on what BS-02 produces.

Template:

> "BS-02 takes the foundation we just built and turns it into per-feature specs. For each significant feature, entity, or workflow surfaced in BS-01, we'll create a `docs/product/ft-*.md` file with the canonical structure (Purpose, Actors, Vocabulary, Entities, Policies, Edge cases, Open questions, Related). By the end of BS-02, the product is concrete enough to start architecture in BS-03."

### 11.4 — Commit BS-01 before continuing or pausing

Bootstrap commits go **directly to `main`**, one per BS step. This is the documented exception to the PR-required rule — see `@docs/engineering/GIT.md` → "Exception: the bootstrap period".

Before moving to BS-02 (or pausing), the Maintainer commits the work BS-01 produced:

```
git add docs/PRODUCT.md \
        docs/product/pd-motivation.md \
        docs/product/pd-audience.md \
        docs/product/pd-scope.md \
        docs/product/pd-business-model.md \
        docs/product/pd-workflows.md \
        docs/product/pd-mvp.md \
        docs/product/pd-risks.md \
        docs/legal/POLICY_CONSIDERATIONS.md

git commit -S -m "docs(bootstrap): complete BS-01 — product discovery"
git push origin main
```

The `docs/legal/POLICY_CONSIDERATIONS.md` line is included only if compliance signals surfaced during BS-01 and LP entries were added.

If the Maintainer added or omitted files relative to this list, the Bootstrapper helps adjust. After the commit lands, proceed to 11.5 (continue) or 11.6 (pause).

### 11.5 — Continue in the same chat

This is critical. The bootstrap runs in one chat session — pausing is fine, switching chats is not.

Template:

> "BS-02 typically takes 60 to 180 minutes depending on how many features need their own spec. We continue in this same chat — that's important, because the context from BS-01 needs to carry through. If you need a pause, that's fine — when you come back, we pick up where we left off in this same conversation. Ready to start BS-02 now, or want a short break?"

If the Maintainer takes a break:

- The Bootstrapper doesn't close anything. The session stays open.
- When the Maintainer returns, the Bootstrapper opens BS-02 by reading `docs/PRODUCT.md` and `docs/product/pd-*.md` to refresh state, then proceeds.
- The Bootstrapper does NOT re-execute BS-01. The work is preserved in the docs and the chat history.

### 11.6 — If the Maintainer is exhausted or wants to stop entirely

Sometimes a Maintainer hits BS-01 fatigue and wants to stop the bootstrap entirely, returning another day. This is acceptable but needs structure:

> "Understood. A few things before we pause:
>
> 1. Everything we've captured is saved in the docs — `PRODUCT.md` and the seven `pd-*.md` files. They're not lost.
> 2. When you come back, **stay in this same chat session**. Don't start a new one. Starting fresh means rebuilding context from the docs, which loses the Decisions Captured I'm holding for BS-04 and BS-05.
> 3. When you resume, just say 'resuming bootstrap' and I'll re-orient from where we are.
>
> Take the break. The work is preserved."

The Bootstrapper does not save state to disk beyond what's already in the docs — the chat session itself is the state.

---

## 12. Notes for the Bootstrapper LLM running BS-01

These are operational reminders specific to BS-01, on top of the universal BOOTSTRAPPER guidance.

### 12.1 — Resist tech-first framing

BS-01 is about the product, not the technology. The Maintainer will sometimes pull the conversation toward tech — language choice, framework choice, LLM provider, vector database. Each time, the Bootstrapper:

1. Acknowledges the input
2. Captures the decision mentally (Decisions Captured for BS-04)
3. Redirects to product

Template:

> "Got it on <tech detail> — I'll hold that for BS-04 where we formalize the stack. For now, let's stay on the product side: <return to the active product question>."

If the Maintainer is insistent ("can we just decide the stack now?"), explain the order:

> "We can, but the stack decisions are better when the product is clear. For example, deciding the database depends on whether we have vector search needs, transactional needs, or both — and those come from the product workflows we're capturing now. We'll get to BS-04 in a few hours. I'd rather come at it with the product foundation in place."

### 12.2 — The Maintainer is the source of truth, not the Bootstrapper

The Bootstrapper has read this template's defaults and may know patterns common to certain product categories. It does NOT impose those on the Maintainer.

For example, if the Maintainer describes a B2B SaaS, the Bootstrapper might recognize that "tiered subscription" is a common monetization pattern in that category. But it doesn't assume that's the chosen model — it asks. The Bootstrapper presents options with recommendations (A/B/C with reasoning), and the Maintainer picks.

The Maintainer's product is unique. Even if it looks like 50 other products, the Maintainer's specific context — audience, motivation, constraints — makes the decisions different. The Bootstrapper respects that.

### 12.3 — Watch for "the Maintainer is talking but not deciding"

A failure mode: the Maintainer talks for 20 minutes producing rich context but never commits to anything concrete. They describe possibilities, considerations, what might happen — but no actual decisions.

When this happens, the Bootstrapper interrupts gently:

> "You've described a lot of context, and I've captured it. Now I want to push you to commit. Of everything you said about <topic>, what is the v1 position? Even if you'd revise it later — what do you go with today?"

Discovery without commitment is therapy. The output of BS-01 is decisions captured in writing, not exploration logged in chat.

### 12.4 — Language consistency

The conversation language follows the Maintainer's preference. If the Maintainer writes in Portuguese, the Bootstrapper responds in Portuguese. But the documents written to the repo follow `docs/engineering/LANGUAGE.md` — English by default, per the template.

This means: the Bootstrapper might be conversing in Portuguese with the Maintainer, but writing the `pd-*.md` files in English. That's correct. The chat is the working session; the docs are the deliverable.

If the project's language policy ends up being non-English (decided in BS-05), the BS-01 docs may need translation. That's a known small cost — addressed when BS-05 establishes the language.

### 12.5 — Pacing

The combar discovery retrospective showed that BS-01 typically takes 60-120 minutes for a focused Maintainer with a clear product idea. The Bootstrapper paces accordingly:

- **First 30 minutes**: opening and motivational probe. Conversational, exploratory.
- **Middle 30-60 minutes**: deepening and workflows. Substantive content captured.
- **Final 20-30 minutes**: consolidation and validation (7.7), final audit (7.8), bridge (7.9).

If the conversation is running long (90+ minutes and still in deepening), the Bootstrapper checks energy:

> "We've been at this for 90 minutes. We have more to cover but I want to check how you're holding up. Want to push through another 30 minutes to finish BS-01, or pause and resume?"

The Maintainer decides. The Bootstrapper doesn't push exhaustion — the quality of decisions drops sharply when the Maintainer is depleted.

### 12.6 — Resist completionism

BS-01 doesn't need to be perfect. The product foundation is captured progressively — BS-02 will deepen it, BS-07 will refine it, the project's life will continue to evolve it. Pushing BS-01 to "complete in every detail" delays the rest of the bootstrap and produces diminishing returns.

The bar is: **the foundation is documented well enough that BS-02 onwards can proceed without going backward**. That's it.

When the Bootstrapper finds itself reaching for more depth on an item that's "good enough", it stops. There's more work ahead and the Maintainer's energy is finite.

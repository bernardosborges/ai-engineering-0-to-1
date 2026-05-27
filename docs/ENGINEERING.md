# Engineering overview

> **Status:** v1
> **Owner:** engineering

This document is the entry point to understanding **how this project is engineered** — the architecture style, the conventions, the processes, and the way decisions get recorded. It is the technical counterpart to [PRODUCT.md](PRODUCT.md).

The complete catalog of engineering documents lives in [INDEX.md](INDEX.md). This document covers the **principles and rituals** that connect those documents.

---

## What lives in `docs/engineering/`

The engineering folder holds documents that describe how the codebase is structured, built, tested, secured, and evolved. Each file owns one concern; together they form the policy layer that every contribution must respect.

For the full list of files with one-line descriptions, see [INDEX.md → Engineering](INDEX.md#engineering).

The most important entry points:

- **[ARCHITECTURE.md](engineering/ARCHITECTURE.md)** for the layered architecture, components, and main flows
- **[STRUCTURE.md](engineering/STRUCTURE.md)** for the folder and file layout
- **[TECH_STACK.md](engineering/TECH_STACK.md)** for the concrete technologies in use
- **[CODE_STYLE.md](engineering/CODE_STYLE.md)** for how code is written in this project

Read these four in order if you are new to the project. The remaining files (SECURITY, TESTING, GIT, LANGUAGE, TECH_DEBT) cover specific concerns and are consulted as needed.

---

## Architecture Decision Records (ADRs)

ADRs are the durable record of significant architectural decisions made on this project. Each ADR captures:

- **Context** — what forced the decision (constraints, options, pressure)
- **Decision** — what was chosen
- **Consequences** — what becomes easier, harder, or off-limits as a result
- **Alternatives rejected** — what was considered and why it lost

ADRs live in `docs/engineering/adr/`. The template for new ADRs is at [engineering/adr/TEMPLATE.md](engineering/adr/TEMPLATE.md).

### When to create an ADR

Create an ADR when a decision is:

- **Significant** — a future maintainer reading the code would want the context to understand it
- **Likely to be re-evaluated** — someone may be tempted to undo or change it later, and you want the original rationale preserved
- **Specifically architectural** — affects layering, data flow, integration points, or major technology choices

Routine choices (which utility to extract, how to name a variable, which CSS class to use) do not warrant an ADR. The bar is "would the next person joining the project benefit from knowing why we chose this?"

### How to create an ADR

1. Copy [engineering/adr/TEMPLATE.md](engineering/adr/TEMPLATE.md) to `engineering/adr/NNNN-kebab-case-title.md` where NNNN is the next number in sequence (zero-padded to four digits)
2. Fill every section: Context, Decision, Consequences (positive + accepted trade-offs), Alternatives rejected. References is optional
3. Remove the `<!-- comment -->` blocks — they are guidance for the author, not part of the published ADR
4. Set Status to `Accepted` when the decision is implemented (or when the PR that implements it merges)

### How to propagate an ADR's consequences

Once an ADR is accepted, its consequences often affect existing documents (`CODE_STYLE.md`, `TECH_STACK.md`, the PR template, the security checklist, etc.).

Do not update those documents by hand — it is error-prone and inconsistent across files. Run the ADR propagation workflow instead:

```
Invoke @.ai/workflows/ADR-PROPAGATOR.md
```

By default this picks up the most recent ADR (highest number) and propagates its consequences across the codebase docs. To target a specific ADR:

```
Invoke @.ai/workflows/ADR-PROPAGATOR.md for ADR 0008
```

The workflow reads the ADR, identifies which documents are affected, presents a plan, and applies the changes after confirmation. See [.ai/workflows/ADR-PROPAGATOR.md](../.ai/workflows/ADR-PROPAGATOR.md) for the full procedure.

### ADRs are immutable

Once an ADR has status `Accepted`, **do not edit it retroactively**. If the decision changes, write a new ADR with status `Supersedes ADR NNNN` and update the original's status to `Superseded by ADR MMMM`. The original stays exactly as it was — it is a record of what was decided at that moment, not a living document.

This is what makes ADRs trustworthy: a reader can rely on them to reflect the truth at the time of the decision, not the latest revision.

### When in doubt

If you cannot decide whether something deserves an ADR, ask: "If a new engineer joined six months from now and disagreed with this choice, would they have enough context to understand why we made it?" If no, write the ADR.

---

## How decisions connect to docs

The engineering documents and the ADRs form a layered system:

- **Documents** describe the current state of the project — what we do today, how we do it
- **ADRs** describe the reasons behind that current state — why we do it this way
- **TECH_DEBT.md** describes what we deferred — what we know we should do but haven't yet

When something feels wrong in a document, the first question is whether the underlying ADR still holds. If the ADR is outdated, write a new ADR superseding it and propagate the consequences. If the ADR is fine but the document drifted from it, update the document.

---

## Principles that recur across engineering docs

A handful of principles underpin most engineering decisions in this project. They are not exhaustive, but they explain the common thread across documents.

### Layering and dependency direction

Code is organized in layers (domain, application, infrastructure, interface). Dependencies flow inward — the domain knows nothing about the database, the framework, or the LLM provider. This is enforced by tooling and documented in [ARCHITECTURE.md](engineering/ARCHITECTURE.md).

### Tests live with the layer they cover

Unit tests cover domain and application logic with fakes; integration tests cover infrastructure with real services in containers; end-to-end tests cover the full stack at the interface boundary. See [TESTING.md](engineering/TESTING.md).

### Security checks belong at every boundary

Authentication at the gateway, authorization in the use case layer, input validation at the schema boundary, output sanitization at the response boundary. No single chokepoint — defense in depth. See [SECURITY.md](engineering/SECURITY.md).

### Documents stay in sync with code

When code changes affect a documented rule or pattern, the PR updates the doc in the same commit. CI does not enforce this — culture does. See [GIT.md](engineering/GIT.md).

### Deferred decisions are tracked, not forgotten

When a task surfaces something that should be addressed but is out of scope right now, an entry goes into [TECH_DEBT.md](engineering/TECH_DEBT.md) with the trigger condition that will promote it back into the active queue. Nothing gets lost.

### AI-assisted development is structured, not ad hoc

The Planner/Executor workflow ([.ai/workflows/TASK-PLANNER.md](../.ai/workflows/TASK-PLANNER.md)) governs how tasks are planned and executed. Every task starts with a planning conversation, ends with a PR, and produces learnings recorded in the roadmap. See [ROADMAP.md](ROADMAP.md) and [GIT.md](engineering/GIT.md).

---

## Related

- [INDEX.md](INDEX.md) — full catalog of all documentation
- [PRODUCT.md](PRODUCT.md) — product overview (the counterpart to this document)
- [ROADMAP.md](ROADMAP.md) — build plan and phase tracking
- [.ai/workflows/TASK-PLANNER.md](../.ai/workflows/TASK-PLANNER.md) — task planning workflow
- [.ai/workflows/ADR-PROPAGATOR.md](../.ai/workflows/ADR-PROPAGATOR.md) — ADR propagation workflow

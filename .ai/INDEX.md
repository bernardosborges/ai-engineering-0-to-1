# `.ai/` — AI-assisted development workflows

This folder contains the methodology for building the project with AI assistance: the workflows that humans and LLMs follow at each stage (bootstrap, planning, execution, propagation) and the prompts those workflows produce.

If you're looking for the project's **knowledge** (what the product does, how the code is structured, what conventions apply), see `docs/INDEX.md`. This folder is about **how the project is built**, not what it is.

---

## Quick orientation

```
.ai/
├── INDEX.md                 ← you are here
├── prompts/                 generated prompts (output of workflows, input to LLMs)
│   ├── execution/             one subfolder per phase; one file per task
│   ├── planning/              planning event prompts (replanning, decomposition)
│   └── TEMPLATE.md            template every execution prompt fills
└── workflows/               operating manuals for each LLM role in the methodology
    ├── BOOTSTRAPPER.md        bootstrap operating manual
    ├── bootstrap/             BS-01..BS-07 step-specific files (read by BOOTSTRAPPER)
    ├── TASK-PLANNER.md        per-task planning conversation
    ├── ROADMAP-PLANNER.md     roadmap structural-change planning conversation
    ├── ROADMAP-EDITOR.md      applies a planning prompt to the roadmap
    ├── ADR-PROPAGATOR.md      propagates an accepted ADR across docs
    ├── PDR-PROPAGATOR.md      propagates an accepted PDR across docs
    ├── LDR-PROPAGATOR.md      propagates an accepted LDR across docs
    └── DOCS-VALIDATOR.md      validates and fixes doc cross-references
```

---

## The two folders, in one paragraph each

### `workflows/` — operating manuals

Each file describes one **role** an LLM plays in this project's methodology. The file is the role's full operating manual: voice, posture, what to read, how to converse, what to produce, when to stop. Workflows are read by LLMs at the start of a session (the human invokes them with `@.ai/workflows/<name>.md`). They are stable instructions; they don't change between sessions.

### `prompts/` — generated artifacts

Each file is the **output** of one planning conversation, ready to be fed into an executor LLM session (typically Claude Code). Prompts are one-shot artifacts: they are written once, executed once, and then live in the repo as a durable record of what was asked of the executor at that moment. Two subfolders: `execution/` (one prompt per task in the roadmap) and `planning/` (one prompt per planning event — replanning, decomposition, convention change).

The folder `prompts/TEMPLATE.md` defines the structure every prompt follows (context, task, requirements, constraints, deliverable, runtime-decisions). All prompts inherit from it.

---

## Workflows in detail

### `BOOTSTRAPPER.md` and `bootstrap/BS-01..BS-07.md`

The **bootstrap** is a one-time onboarding that turns an empty clone of this template into a fully documented greenfield project. It's a single guided conversation in 7 steps (BS-01 through BS-07), each producing concrete docs in the repo. `BOOTSTRAPPER.md` is the operating manual (universal pattern, voice, forcing functions, ripple handling). The `bootstrap/BS-NN-*.md` files contain the step-specific instructions (goal, anchor questions, conversation flow, exit criteria).

Invoke once, at the start of a new project, by saying "let's start the bootstrap" or by referencing `@.ai/workflows/BOOTSTRAPPER.md`. After the bootstrap completes, this workflow is rarely re-invoked — only for major pivots.

### `TASK-PLANNER.md`

The **per-task planner**. Invoked at the start of each task in the roadmap (TSK-NNN). It conducts a planning conversation with the Maintainer — clarifying scope, surfacing unknowns, anchoring decisions — and produces an execution prompt saved at `.ai/prompts/execution/<phase>/TSK-NNN.md`. The execution prompt is then run by an executor LLM (typically Claude Code) in a separate session.

This is the most-used workflow after the bootstrap is complete. Every task goes through it.

### `ROADMAP-PLANNER.md` and `ROADMAP-EDITOR.md`

These workflows handle **structural changes to the roadmap**: decomposing a task that turned out larger than expected, inserting a new task, renumbering after replanning, creating a new phase, adopting a new convention mid-stream.

- `ROADMAP-PLANNER.md` runs in a Claude desktop conversation. The Maintainer discusses the change; the planner produces a detailed planning prompt saved at `.ai/prompts/planning/YYYY-MM-DD-<slug>.md`.
- `ROADMAP-EDITOR.md` runs in a Claude Code session. It picks up the most recent planning prompt, presents an execution plan, and applies the changes to the roadmap files after the Maintainer confirms.

Two-step pattern (planning then editing) so the structural change is reviewed before being applied.

### `ADR-PROPAGATOR.md`, `PDR-PROPAGATOR.md`, `LDR-PROPAGATOR.md`

These workflows handle the **propagation of accepted decision records** across the docs they affect:

- `ADR-PROPAGATOR.md` — Architecture Decision Records (`docs/engineering/adr/`)
- `PDR-PROPAGATOR.md` — Product Decision Records (`docs/product/pdr/`)
- `LDR-PROPAGATOR.md` — Legal Decision Records (`docs/legal/ldr/`)

The flow is the same for all three: a decision record is written (the Maintainer fills the corresponding TEMPLATE.md), and the propagator workflow reads the decision and updates every other doc affected by it. For PDRs in particular, the workflow also handles creating new `ft-*.md` files when the decision introduces a new feature concept.

Invoke by running the corresponding workflow file in a Claude Code session right after the decision record is accepted. Don't edit affected docs manually — let the propagator handle them so cross-references stay consistent.

### `DOCS-VALIDATOR.md`

The **documentation integrity** workflow. It walks every doc under `docs/` and `.ai/`, detects broken links, orphan files, stale section references, cross-folder reference breaks, unfilled BOOTSTRAP markers, and naming inconsistencies, then fixes what is safely auto-fixable and surfaces the rest for a decision.

Invoke by running the workflow file in a Claude Code session at the end of a bootstrap (BS-06), as periodic maintenance, or right after any deliberate doc refactoring (renames, moves, restructuring). It applies fixes in reviewable per-category batches and never commits automatically.

---

## Prompts in detail

### `prompts/TEMPLATE.md`

The structure every prompt follows. Six sections (`context`, `task`, `requirements`, `constraints`, `deliverable`, `runtime-decisions`) covering what the executor needs to know, what to do, the rules, the format of the output, and any decisions the executor had to make on its own while running. Every prompt — execution or planning — fills this template.

### `prompts/execution/`

One subfolder per phase (e.g., `phase-1-foundation/`, `phase-2-infrastructure/`, etc.), each containing one file per task (e.g., `TSK-001.md`). These prompts are produced by `TASK-PLANNER.md` during planning conversations.

The archive of execution prompts is the durable record of what the executor was asked to do at each task — useful for reviewing past decisions, reproducing failures, and onboarding new contributors.

### `prompts/planning/`

One file per planning event, named `YYYY-MM-DD-<slug>.md` (e.g., `2026-05-09-decompose-tsk-010.md`). These prompts are produced by `ROADMAP-PLANNER.md` when the roadmap structure needs to change, and applied by `ROADMAP-EDITOR.md`.

Each planning event is also referenced from `docs/ROADMAP.md` in the "Planning events" section, with a one-line summary.

---

## Common scenarios — which workflow do I invoke?

| Scenario | Workflow |
|---|---|
| Starting a new project from this template | `BOOTSTRAPPER.md` |
| Planning the next task in the roadmap | `TASK-PLANNER.md` |
| Executing a task (writing code) | The execution prompt for that task (no workflow — feed it to Claude Code) |
| Decomposing a task that's too large | `ROADMAP-PLANNER.md` then `ROADMAP-EDITOR.md` |
| Inserting a new task into a phase | `ROADMAP-PLANNER.md` then `ROADMAP-EDITOR.md` |
| Creating a new phase | `ROADMAP-PLANNER.md` then `ROADMAP-EDITOR.md` |
| Adopting a new convention mid-stream (e.g., a marker, a numbering scheme) | `ROADMAP-PLANNER.md` then `ROADMAP-EDITOR.md` |
| Architectural decision being made | Write an ADR (`docs/engineering/adr/`), then invoke `ADR-PROPAGATOR.md` |
| Product decision being made (changes feature behavior, adds/removes a feature, revises a policy) | Write a PDR (`docs/product/pdr/`), then invoke `PDR-PROPAGATOR.md` |
| Legal position being formed or revised | Write an LDR (`docs/legal/ldr/`), then invoke `LDR-PROPAGATOR.md` |
| Validating doc cross-references (after a refactor, periodically, or at end of bootstrap) | `DOCS-VALIDATOR.md` |

For pure documentation edits that don't reflect a decision (typo fix, clarification, formatting), no workflow is needed — open a PR with the edit directly.

---

## Relationship to `docs/`

`docs/` and `.ai/` are complementary:

- `docs/` is **the project's knowledge** — what the product is, how the code is structured, what conventions apply, the durable records of decisions made over time.
- `.ai/` is **the methodology** — how the project is built, the LLM roles involved, the prompts and conversations that produce the knowledge in `docs/`.

When you read `docs/INDEX.md`, you orient yourself in the project's knowledge. When you read this file (`.ai/INDEX.md`), you orient yourself in the methodology that produced it.

A new contributor reads `docs/INDEX.md` first. A contributor about to do a specific kind of work (plan a task, run a propagation, decompose a phase) reads this file to find the right workflow.

# ADR NNNN — Short, imperative title

<!--
Filename: NNNN-kebab-case-title.md (e.g., 0002-clean-architecture.md)
Title: mirrors the filename. Start with a verb or state the chosen option directly.
  Good: "UUIDv7 as primary key", "Adopt pgvector over dedicated vector DB"
  Bad: "Database decision", "Authentication stuff"
-->

**Status:** Proposed | Accepted | Deprecated | Superseded by ADR NNNN
**Date:** YYYY-MM-DD
**Deciders:** Core team | <names if a specific person/group owns this>

## Context

<!--
What forces are at play? Why do we need to decide this now?
Describe the problem, not the solution. Include constraints (technical, regulatory,
team-size, budget) that narrow the option space.
Keep it factual and short — 1-3 paragraphs, or a numbered list of candidates with
one-line summaries of each. The goal is: a new team member reading this in 12 months
understands WHY this decision had to be made.
Avoid: marketing language, long theoretical explanations, irrelevant history.
-->

## Decision

<!--
State the decision clearly and imperatively. Start with "We use...", "We adopt...",
"We reject..." — not "We should" or "We could".
Include concrete implementation details that affect the codebase:
- Library names and versions (if relevant to the decision)
- Where the rule is enforced (config, CI, pre-commit, lint rule)
- Migration path if it replaces something existing
Be specific enough that someone implementing this doesn't need to ask clarifying questions.
-->

## Consequences

### Positive

<!--
What becomes easier, safer, cheaper, or faster? Be specific.
Bad: "Better performance"
Good: "Eliminates B-tree index fragmentation on write-heavy tables"
-->

### Negative / accepted trade-offs

<!--
What becomes harder, more complex, or more expensive? Every real decision has costs.
If you can't think of any, you haven't thought hard enough — go back.
Label each trade-off as "Accepted" with a brief reason. This is what prevents
someone from "fixing" the decision later without understanding why it was accepted.
Bad: "Might be slower"
Good: "16 bytes per key vs 8 bytes for BIGINT. Accepted — impact is negligible at
       projected scale; revisit if index size becomes a bottleneck on specific tables."
-->

## Alternatives rejected

<!--
One bullet per alternative, with the reason it was rejected. Keep it short and factual.
This section is the fastest way for someone to answer "why didn't we just...?"
Bad: "Option X — didn't like it"
Good: "Option X — rejected because it requires a separate service to operate, and
       we lack the ops capacity at current team size."
-->

## References

<!--
Links that support the decision: RFCs, specs, benchmarks, library docs, blog posts
that were actually influential. No link-dumping — only what a future reader would
need to re-evaluate this decision.
Optional. Omit this section entirely if there are no relevant references.
-->

---

## Notes on using this template

When writing a new ADR:

1. **Copy this file**, rename to `NNNN-kebab-case-title.md` where NNNN is the next number in sequence.
2. **Fill every section** except References, which is optional.
3. **Remove all `<!-- comment -->` blocks** before committing — they are guidance for the author, not part of the published ADR.
4. **Update Status to Accepted** when the decision is implemented (or merged into the PR that implements it).
5. **Never edit an Accepted ADR retroactively.** If the decision changes, write a new ADR with status "Supersedes ADR NNNN" and update the original's status to "Superseded by ADR MMMM".
6. **Link the ADR** from the most affected doc (typically `@docs/engineering/ARCHITECTURE.md`, `@docs/engineering/TECH_STACK.md`, or `@docs/engineering/CODE_STYLE.md`) so it is discoverable through the natural doc flow.

ADRs document **significant** decisions — ones that future maintainers will need context to understand or might be tempted to undo. Routine choices (which CSS class to use, which utility function to extract) do not warrant an ADR.

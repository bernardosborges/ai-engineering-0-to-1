<!--
Title must follow Conventional Commits: <type>(<scope>): <description>
Examples:
  feat(orders): add streaming response support
  fix(orders): use UTC for deadline comparison
See @docs/engineering/GIT.md for the full format.
-->

## What

<!-- One or two sentences. What does this PR change? -->

## Why

<!-- Context and motivation. Link roadmap entry, ADRs, or docs if relevant. -->

## Verification

<!-- What did you run? What did it cover? Any manual testing? -->

## Decisions

<!-- Document any decision made during implementation that differs from the
     prompt or reveals new information worth preserving. If the prompt has a
     <runtime-decisions> section that was updated, summarize the key changes
     here and link the prompt. -->

---

## Checklist

### General

- [ ] Branch is up to date with `main`
- [ ] Diff scope is focused (split if it grew)
- [ ] Commits follow Conventional Commits format
- [ ] No `git add .` used — commits are curated
- [ ] No commented-out code, no `print()` statements, no `TODO` without author and context

### Code quality

- [ ] Type annotations on all new functions
- [ ] No new escape hatches (`Any`, `unknown`, etc.) without inline justification
- [ ] New public functions have docstrings when non-obvious

### Security

- [ ] No secrets in diff (`.env`, logs, fixtures, test data)
- [ ] No sensitive data logged (passwords, tokens, personal identifiers, PII)
- [ ] External input validated with size and length limits
- [ ] No database models returned directly in HTTP responses — explicit schemas only
- [ ] If touching authorization: new use cases have authorization tests

### Testing

- [ ] New code has tests appropriate to its layer (unit, integration, e2e)
- [ ] Tests use factories for domain objects, fixtures for infrastructure
- [ ] If touching an LLM-backed feature: uses fake adapter in unit tests

### Documentation

- [ ] Relevant `docs/` updated if behavior changed
- [ ] New architectural decision recorded as ADR in `docs/engineering/adr/`
- [ ] `@docs/ROADMAP.md` updated if this closes a roadmap entry (Status, PR link, Learnings)
- [ ] Deferred decisions added to `@docs/engineering/TECH_DEBT.md` with TD-NNN entry

<!-- BOOTSTRAP:PR_CHECKLIST_PROJECT_SPECIFIC:START -->
> 📝 Extended by bootstrap prompt `05-conventions.md` and grows as the project encounters recurring concerns.
>
> Project-specific checklist items are added here — e.g., "If touching billing flow:", "If modifying privacy-sensitive entity:", etc. The items above are universal; this section captures what's unique to this codebase.
<!-- BOOTSTRAP:PR_CHECKLIST_PROJECT_SPECIFIC:END -->

---

## Related

<!-- Closes #, Refs #, ADR: -->
- Roadmap: TSK XXX
- Prompt: `.ai/prompts/execution/<phase>/TSK-XXX-<slug>.md`

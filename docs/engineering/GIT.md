# Git Workflow

How Git is used in this project. Applies to every commit and every PR.

The rules below are baked into the method this template adopts. They are not arbitrary — each one solves a specific problem (linear history, reproducibility, blast radius, review quality). The bootstrap workflow will show them to you and ask if you want to adjust; most projects keep them as is.

---

## Branching strategy

**Trunk-based development.** There is one long-lived branch: `main`. Everything else is short-lived.

- `main` is always releasable.
- Feature branches live **1–3 days**. Longer than that, break the work down.
- No `develop`, `release`, or `hotfix` branches. Hotfixes follow the same flow as features.
- Feature flags carry incomplete work to production safely — merge early, enable later.

### Branch naming

Pattern: `<type>/<short-kebab-description>`.

| Type | Use |
|---|---|
| `feat/` | New user-facing feature or capability |
| `fix/` | Bug fix |
| `refactor/` | Code change without behavior change |
| `chore/` | Tooling, config, dependencies |
| `docs/` | Documentation only |

Examples: `feat/streaming-responses`, `fix/order-timezone`, `refactor/extract-llm-port`, `chore/bump-framework-version`.

Keep the description short — 3–5 words. Issue IDs are not required in branch names; reference them in the PR body if relevant.

---

## Commits

### Conventional Commits

Format:

```
<type>(<scope>): <description>

<optional body>

<optional footer>
```

### Types

| Type | Use |
|---|---|
| `feat` | New feature visible to users or external consumers |
| `fix` | Bug fix |
| `refactor` | Restructuring without behavior change |
| `perf` | Performance improvement |
| `docs` | Documentation only |
| `test` | Adding or changing tests |
| `chore` | Tooling, config, non-code |
| `build` | Build system, dependencies |
| `ci` | CI/CD configuration |
| `revert` | Revert of a previous commit |

### Scopes

Scopes are free-form but should reflect the area touched. Omit the scope when the change is truly global (rare).

<!-- BOOTSTRAP:COMMIT_SCOPES:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will list the project's suggested scopes, typically grouped as:
>
> - **Domain areas** (the bounded contexts or features the project has)
> - **Infrastructure** (`llm`, `db`, `cache`, `queue`, `storage`, etc.)
> - **Cross-cutting** (`api`, `infra`, `deps`, `ci`)
<!-- BOOTSTRAP:COMMIT_SCOPES:END -->

### Rules

- First line: **72 characters maximum**.
- Description in **English**, imperative mood (`add`, not `added` or `adds`).
- Lowercase first letter. No period at the end.
- Body is **optional but encouraged** for non-trivial changes. Explain *why*, not *what* — the diff shows what.
- Body lines wrapped at 100 characters.
- Reference issues in the footer when relevant: `Closes #123`, `Refs #456`.

### Examples

```
feat(orders): add streaming response support

The orders endpoint now streams updates as they arrive from the worker,
reducing time-to-first-byte from ~2s to ~200ms. Requires client-side SSE
handling.

Closes #142
```

```
fix(orders): use UTC for deadline comparison
```

```
refactor(llm): extract retry logic into dedicated helper
```

```
chore(deps): bump framework to 0.42
```

### Anti-patterns

```
# Too vague
fix: bug
feat: changes

# Not imperative
feat(orders): added streaming

# Describes the what, not why
refactor(db): changed session.py

# Multiple unrelated changes in one commit
feat(orders): add streaming, fix timezone, update readme
```

### Commit granularity

Each commit must represent **one logical change**. If you need "and" in the description, split the commit.

During a feature branch, loose WIP commits are fine — they will be squashed on merge. But push meaningful progress, not every save.

---

## Signed commits

**Required.** Every commit on `main` must be signed with a verified GPG or SSH key.

- Configured via `git config commit.gpgsign true` (or SSH equivalent).
- Repository branch protection rejects unsigned commits to `main`.
- CI verifies signatures on PRs.

Setup for new contributors is a one-time operation, documented in `README.md`. No merge until signing works.

---

## Pull Requests

### Opening

- Branch off latest `main`: `git checkout main && git pull && git checkout -b feat/your-thing`.
- Push the branch and open a PR early, even in draft, to run CI and get visibility.
- Title follows Conventional Commits format — **the PR title becomes the squash merge commit message**.

### PR title convention

The title of a PR uses a scope that references the roadmap entry it implements. The pattern is:

```
<type>(<id>): <description>
```

Where `<id>` is the identifier of the task in `@docs/ROADMAP.md` (e.g., `tsk-033`).

<!-- BOOTSTRAP:PR_TITLE_CONVENTION:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> This section will define the project's specific PR title convention:
>
> - The exact ID format used (default is `tsk-NNN`).
> - How the ID relates to entries in `docs/ROADMAP.md` and prompt filenames under `.ai/prompts/`.
> - Concrete examples of well-formed PR titles.
>
> The squash merge commit message takes the PR title, so this convention also surfaces in `main`'s history.
<!-- BOOTSTRAP:PR_TITLE_CONVENTION:END -->

Internal commits within a feature branch use free-form scopes describing the area changed (`docker`, `roadmap`, `auth`, etc.) — they don't need to carry the PR ID since they'll be squashed.

### PR description

Fill out the template at `.github/PULL_REQUEST_TEMPLATE.md`. Minimum content:

- **What** — short summary of the change.
- **Why** — context, motivation, links to issues or ADRs.
- **How** — non-obvious implementation choices.
- **Testing** — what you ran and what it covered.
- **Checklist** — security, tests, docs (auto-filled by template).

### Size

Target: **under 400 lines of diff** (excluding auto-generated files, lockfiles, migrations).

PRs over 400 lines are not automatically rejected, but reviewers may ask for a split before reviewing. Large PRs hide bugs, exhaust reviewers, and produce shallow reviews.

If a change is genuinely atomic and large (framework upgrade, big refactor), flag it in the description and discuss the split strategy in advance.

### Review

- **1 approving review required** before merge.
- Author may self-merge after approval (no blocking on the reviewer to click).
- Reviewers are expected to respond within **1 business day**.
- LLM code review is accepted as a first pass but does not replace a human approval.
- Disagreements are resolved in the PR thread. Escalate to a call only when a written thread is going in circles.

### CI

All checks must pass before merge:

- Lint
- Type checks
- Architecture rules (if enforced)
- Tests (excluding marked-live and slow tests)
- Dependency audit
- Secret scan

Broken `main` is an emergency — drop everything to fix.

---

## Merging

**Squash merge, always.** Configure as the only option in repo settings.

- One feature, one commit in `main`.
- Commit message = PR title (Conventional Commits format).
- PR body becomes the commit body.
- Branch is **auto-deleted** on merge.

### Keeping your branch up to date

Use **rebase**, not merge, to pull `main` into your feature branch:

```
git fetch origin
git rebase origin/main
```

If you get conflicts, resolve them in the rebase. Do not use `git merge main` — it creates ugly merge commits that end up squashed anyway but pollute the branch history during review.

Force-push is expected after rebase:

```
git push --force-with-lease
```

Never `--force` without `--lease` on a branch others might be on.

### Linear history

`main` is enforced as **linear history** — no merge commits, only squash-merged commits. This makes `git log`, `git bisect`, and incident investigations dramatically easier.

---

## Main branch protection

Branch protection rules on `main`:

- Require pull request before merging
- Require 1 approving review
- Require status checks to pass before merging
- Require conversation resolution before merging
- Require signed commits
- Require linear history
- Delete head branches on merge
- Block force pushes
- Block deletions
- Apply to administrators too (no bypass)

---

## Hotfixes

No special flow. A hotfix is a `fix/` branch off `main`, same PR process, same CI gates.

Difference from a regular fix: urgency. Expect faster review (target: 2 hours) and a named on-call reviewer during business hours.

After merge, tag and deploy immediately. See Versioning below.

---

## Versioning and tags

**SemVer** (`MAJOR.MINOR.PATCH`) for production releases.

- `MAJOR` — incompatible API changes to consumers.
- `MINOR` — new backwards-compatible functionality.
- `PATCH` — backwards-compatible bug fixes.

Tags are **annotated**, signed, and created on the exact commit being deployed:

```
git tag -a -s v1.2.3 -m "Release 1.2.3"
git push origin v1.2.3
```

Release notes are auto-generated from Conventional Commits between tags — another reason to keep commit messages clean.

Versioning is not active until the first production release. Until then, all merges to `main` are deployable but untagged.

---

## Automated PRs (Renovate, Dependabot)

| Change type | Action |
|---|---|
| Patch version bump with green CI | Auto-merge |
| Minor version bump | Requires human review |
| Major version bump | Requires human review + ADR if impact is significant |
| Security patches | Auto-merge if green CI, regardless of version bump |

Auto-merge configured via the bot's rules (Renovate or equivalent), not via the platform's auto-merge feature, to keep a single source of truth.

---

## Reverting

**Use `git revert`, not force-push or history rewrites on `main`.**

```
git revert <commit-sha>
```

This creates a new commit of type `revert:` that undoes the change, preserving history. Open a PR with the revert commit, same review flow (expedited if incident-related).

If the original commit must be reverted because of a secret leak or credential exposure, **reverting is not enough** — rotate the secret first, then revert, then purge from history as a separate operation with explicit team coordination.

---

## Per-PR workflow

End-to-end sequence for every PR, from branch creation to merge. Consolidates the rules above into an operational checklist.

### The two levels of commit granularity

This project uses **trunk-based development with squash merge**. That creates two distinct levels of commit history:

- **Inside the feature branch**: many small, meaningful commits. Each represents one logical change. This is working history — it shows how the change evolved, makes review easier, lets you revert individual pieces while developing.
- **On `main` after merge**: a single squashed commit per PR. Squash merge collapses all branch commits into one with the PR title as message. `main` stays linear and readable at a high level.

Squash merge is a **merge strategy**, not a rule that says "commit less". Commit as often as each logical change warrants while working.

### No `git add .`

Commits are **curated**, not captured. `git add .` pulls in everything modified at once, which typically mixes unrelated changes (code + docs + config) into a single commit. That violates the "one logical change per commit" rule.

Use explicit staging instead:

```
# Stage specific files
git add path/to/file1 path/to/file2

# Or stage interactively (recommended when changes span several files)
git add -p

# Or stage only modified tracked files (excludes new untracked files)
git add -u <paths>
```

The `git add -p` flow walks you through each change hunk and asks whether to stage it. It's the fastest way to build clean commits when many files are modified.

### ROADMAP updates stay local until the end

As you work on a PR, insights and decisions worth recording go into `@docs/ROADMAP.md` — the Learnings subsection of the task's entry.

**Do not commit these edits as you go.** Keep `docs/ROADMAP.md` as a locally modified file through the entire PR. A single final commit at the end captures the complete Learnings plus the PR link and ✅ status.

Why: Learnings are reflection, not code evolution. Spreading them across multiple commits pollutes the branch history with "docs(roadmap): note X", "docs(roadmap): note Y" commits that add noise. One final commit is cleaner and matches the mental model — you're summarizing the PR, not logging each thought as it appears.

### Standard flow

```
# 1. Start from an up-to-date main
git checkout main
git pull

# 2. Create the feature branch
git checkout -b <type>/<short-description>
# e.g., git checkout -b feat/add-user-entity

# 3. Work in small, logical commits. Stage explicitly.
git add path/to/file1 path/to/file2
git commit -m "feat(scope): add X and Y"

git add path/to/test_file
git commit -m "test(scope): add unit tests for X"

# ... continue as many commits as the work needs ...
#
# While working, edit docs/ROADMAP.md to record Learnings as they appear.
# DO NOT commit docs/ROADMAP.md yet — it stays locally modified.

# 4. Push and open the PR.
git push -u origin <type>/<short-description>
gh pr create    # or open via the platform UI

# 5. Final roadmap commit once the PR URL is known.
#    Edit docs/ROADMAP.md:
#    - Set Status to ✅ Complete
#    - Fill the PR link
#    - Finalize the Learnings subsection
git add docs/ROADMAP.md
git commit -m "docs(roadmap): mark <id> as complete"
git push

# 6. Wait for CI to pass and review to approve.

# 7. Squash merge.
gh pr merge --squash --delete-branch
```

### Example branch history

A healthy feature branch might look like this before merge:

```
feat(user): add User entity and Email value object
test(user): add unit tests for User entity
feat(user): add UserRepository port
fix(user): handle empty email edge case
feat(user): add User ORM model and migration
feat(user): add SQL UserRepository
test(user): add integration tests for repository
docs(roadmap): mark <id> as complete
```

Eight commits, each self-contained. The last one captures the complete ROADMAP update (Learnings + Status + PR link). After squash merge, `main` gets a single commit titled with the PR title. The eight commits remain visible in the PR itself, useful for future archaeology.

### When to use amend

Reserve `git commit --amend` for narrow, legitimate cases:

- Fixing a typo in the message of the commit you **just** made, before pushing.
- Adding a file you forgot to stage, still in the same logical change.
- Re-signing a commit whose signature failed.

Do **not** use amend:

- To collapse unrelated commits — that's what squash merge is for.
- On commits that were already pushed and reviewed — rewrites history others may have seen.
- To "clean up" a branch before merge — the branch history is valuable; squash merge handles the final form.

### Keeping docs in sync with code

Documentation and code stay in sync because they **ship in the same PR**. If a change alters something documented, the doc update is a commit in the same branch.

This applies to:

- `@docs/ROADMAP.md` — Learnings and final status commit at the end of the PR (see above).
- `@docs/engineering/ARCHITECTURE.md`, `@docs/engineering/CODE_STYLE.md`, and other policy docs — when the change reflects a new decision or pattern. These go as their own commits during the PR, not held until the end.
- ADRs — if the PR introduces a significant technical decision.

The reverse is also fine: a PR whose only purpose is documentation is legitimate on its own.

### Exception: the bootstrap period

The bootstrap period (BS-01 through BS-07, run via `@.ai/workflows/BOOTSTRAPPER.md`) is the only exception to the branch + PR flow.

During the bootstrap, commits go **directly to `main`** — one commit per BS step, as each step completes. There is no PR because:

- The repository starts empty; `main` has nothing to PR against on the first commit.
- The bootstrap is a single-author, single-session workflow — there's no second reviewer yet.
- Each BS step is a coherent unit of documentation (product discovery, architecture, conventions, etc.) that benefits from being its own checkpoint in history.

**Per-BS commit pattern.** At the end of each BS step (after Required outcomes are satisfied), the Maintainer commits the files that step produced or modified, directly to `main`:

```
git add <files filled or modified in this BS step>
git commit -S -m "docs(bootstrap): complete BS-NN — <step name>"
git push origin main
```

The specific files vary per step. Each BS step file (`.ai/workflows/bootstrap/BS-NN-*.md`) provides the concrete commit command and the typical file list for its step.

**End of the bootstrap = end of the exception.** Once BS-07 closes (the Roadmap is in place, TSK-001 is ready to be planned by `@.ai/workflows/TASK-PLANNER.md`), every subsequent change follows the Standard flow above — branch + PR + signed commits + squash merge — with no exceptions.

The transition is unambiguous: as soon as TSK-001 enters planning, the project is no longer bootstrapping. PR discipline starts.

### Checklist before clicking "Merge"

- [ ] Branch contains only the commits relevant to this PR's scope.
- [ ] Each commit message follows Conventional Commits.
- [ ] All commits are signed.
- [ ] `@docs/ROADMAP.md` final commit is in place: Status ✅ Complete, PR link filled, Learnings written.
- [ ] Any other affected docs were updated as their own commits during the PR.
- [ ] No `git add .` used — commits are curated, not captured.
- [ ] CI is green.
- [ ] At least one approving review.
- [ ] Diff is under ~400 lines (or split was discussed in the description).

---

## Common recipes

### Start a new feature

```
git checkout main
git pull
git checkout -b feat/my-feature
# ... work ...
git push -u origin feat/my-feature
# open PR via gh CLI or platform UI
```

### Update feature branch with latest main

```
git fetch origin
git rebase origin/main
git push --force-with-lease
```

### Fix last commit message before push

```
git commit --amend
```

### Split a commit into multiple

```
git rebase -i HEAD~N
# mark the commit as 'edit', then:
git reset HEAD^
# stage and commit in pieces
git rebase --continue
```

### Fix a typo in a merged PR's commit message

Don't. `main` is immutable. Note the typo, move on. If it matters, the next commit touching that area can reference both.

---

## What this document does not cover

- **Issue tracking** — lives in the project board / issues, not here.
- **Release process** — future `RELEASE.md` when production deploys are active.
- **Hotfix runbook** — future `docs/runbooks/` entry.
- **Changelog maintenance** — auto-generated from tags; no manual `CHANGELOG.md`.

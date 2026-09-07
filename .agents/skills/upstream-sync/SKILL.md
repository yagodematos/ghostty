---
name: upstream-sync
description: >-
  Use when preparing, reviewing, or resolving synchronization between this
  long-lived fork and ghostty-org/ghostty.
---

# Upstream Synchronization

`origin` is the fork and `upstream` is `ghostty-org/ghostty`.

## Branch model

- `main` is the long-lived shared default branch for this fork. Fork feature,
  fix, and chore branches start from it and merge back into it.
- `upstream-main` is the pristine mirror of `upstream/main`: do not put fork
  work on it.
- Do not rebase shared `main`. Merge the updated mirror into it instead,
  preserving a reviewable fork history.
- Upstream sync always flows `upstream/main` -> `upstream-main` -> `main`.

## Inspect before changing history

Fetch both remotes and inspect ancestry and ranges; do not assume a local ref
is current:

```sh
git remote -v
git fetch upstream
git fetch origin
git log --oneline upstream-main..upstream/main
git log --oneline upstream/main..upstream-main
git log --oneline upstream-main..main
git diff --stat upstream-main...main
```

Confirm the active branch, clean working tree, remote URLs, and the merge base
before any merge or push. Stop for direction if `upstream-main` has fork-only
commits, if a force-push/rebase would be needed, or if the remotes do not match
this model.

## Synchronize the mirror, then the fork

Advance the pristine mirror first. A fast-forward-only merge protects the
invariant that `upstream-main` exactly follows upstream:

```sh
git switch upstream-main
git merge --ff-only upstream/main
git push origin upstream-main
```

Then merge the updated mirror into the fork's shared branch and push it.
Resolve conflicts only in fork-owned material where possible; run the relevant
harness checks before publishing:

```sh
git switch main
git merge upstream-main
git push origin main
```

Do not replace either merge with a rebase of shared `main`. Keep the
fork-only delta narrow and recognizable, particularly `.agents/` and concise
top-level overlays, to reduce future conflict surface.

## Start work from the correct base

For fork-only work, branch from `main` and open the PR back to it:

```sh
git switch main
git switch -c <type>/<topic>
```

Keep fork-only harness material isolated in `.agents/` and short top-level
policy overlays. The sole CI exception is the standalone
`.github/workflows/fork-harness.yml` PR harness; keep it separate and do not
alter existing upstream workflows. Do not alter upstream dependency management,
generated artifacts, or unrelated documentation merely to support agents;
those changes increase conflict cost.

For fork work, follow `.agents/POLICY.md`. When a change may be sent upstream,
read and satisfy `AI_POLICY.md` in addition to the fork's normal validation and
review expectations.

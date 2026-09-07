---
name: upstream-sync
description: >-
  Use when preparing, reviewing, or resolving synchronization between this
  long-lived fork and ghostty-org/ghostty.
---

# Upstream Synchronization

`origin` is the fork and `upstream` is `ghostty-org/ghostty`. Before a sync,
inspect remotes and the proposed range rather than assuming branch ancestry:

```sh
git remote -v
git fetch upstream
git log --oneline HEAD..upstream/main
git diff --stat HEAD...upstream/main
```

Keep fork-only harness material isolated in `.agents/` and short top-level
policy overlays. Do not alter upstream dependency management, CI, generated
artifacts, or unrelated documentation merely to support agents; those changes
increase conflict cost.

When a change may be sent upstream, read `AI_POLICY.md` and satisfy its
upstream-submission section in addition to the fork's normal validation and
review expectations.

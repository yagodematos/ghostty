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
# New commits upstream
git log --oneline HEAD..upstream/main
# Fork-only commits
git log --oneline upstream/main..HEAD
# Fork delta
git diff --stat upstream/main...HEAD
```

Keep fork-only harness material isolated in `.agents/` and short top-level
policy overlays. Do not alter upstream dependency management, CI, generated
artifacts, or unrelated documentation merely to support agents; those changes
increase conflict cost.

For fork work, follow `.agents/POLICY.md`. When a change may be sent upstream,
read and satisfy `AI_POLICY.md` in addition to the fork's normal validation and
review expectations.

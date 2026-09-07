---
name: testing
description: >-
  Use when selecting or reporting validation for Ghostty Zig, libghostty-vt,
  Nix, or macOS changes.
---

# Ghostty Testing

Run tools from the direnv-loaded Nix development shell, or use
`nix develop -c <command>`. The CI workflow is the source for platform-wide
coverage; local validation should be targeted to the changed area.

Use `zig build test -Dtest-filter=<name>` during a focused Zig change and
`zig build test` for the core suite. `zig build test` does not include
libghostty-vt module tests; use `zig build test-lib-vt` for those, with the
same `-Dtest-filter` option when appropriate.

Use `.agents/commands/check` for deterministic combined validation:

```sh
.agents/commands/check --test-filter '<name>'
.agents/commands/check
.agents/commands/check --full
.agents/commands/check --macos
```

`--full` adds libghostty-vt tests and `typos`; on macOS it also runs the
Xcode-wrapper validation. `--macos` runs SwiftLint, refreshes the Zig
framework without building the app bundle through Zig, and runs Xcode unit
tests. Report exact commands that ran, their outcome, and meaningful coverage
gaps (including skipped UI tests).

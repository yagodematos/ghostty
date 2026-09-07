# Ghostty Fork Agent Guide

## Environment

- Nix is the source of truth for development dependencies. Enter the project
  shell with `direnv allow` (the normal workflow) or prefix commands with
  `nix develop -c`.
- Do not add mise, Homebrew version pins, or another dependency manager. The
  flake already provides Zig, Nushell, formatters, linters, and test tools.
- On macOS, the Nix shell intentionally leaves Xcode tool selection to the
  system. `macos/build.nu` invokes `xcodebuild` with a clean environment.
  Full Xcode must be installed and selected before macOS validation.

## Repository map

- `src/`: shared Zig core.
- `macos/`: native macOS app and Xcode project.
- `src/apprt/gtk/`: GTK app runtime.
- `include/ghostty/vt/` and `pkg/`: libghostty-vt public interfaces and packages.
- `nix/`, `flake.nix`, and `.envrc`: development shell, packaging, and CI
  dependency definitions.
- `.github/workflows/`: CI command and platform coverage reference.

Read the surrounding implementation, tests, and applicable nested `AGENTS.md`
before changing code. Keep changes small and coherent; avoid unrelated
refactors, formatting churn, and opportunistic cleanup.

## Build and test

Run these from the Nix development shell (or use `nix develop -c <command>`):

- Core build: `zig build`
- Faster macOS core-only build: `zig build -Demit-macos-app=false`
- Targeted Zig test: `zig build test -Dtest-filter=<test name>`
- Core Zig tests: `zig build test`
- libghostty-vt build: `zig build -Demit-lib-vt`
- Targeted libghostty-vt test: `zig build test-lib-vt -Dtest-filter=<test name>`
- Full libghostty-vt tests: `zig build test-lib-vt`
- libghostty-vt WASM build:
  `zig build -Demit-lib-vt -Dtarget=wasm32-freestanding -Doptimize=ReleaseSmall`

On macOS, if shared Zig code changed, first run
`zig build -Demit-macos-app=false`, then use the Xcode wrapper:

- App build: `macos/build.nu`
- macOS unit tests (UI tests are intentionally skipped by the wrapper):
  `macos/build.nu --action test`

Use `.agents/commands/check` as the deterministic validation entrypoint:

- Default checks: `.agents/commands/check`
- Target a Zig test: `.agents/commands/check --test-filter <test name>`
- Add libghostty-vt and broader static checks (and macOS validation on macOS):
  `.agents/commands/check --full`
- Run macOS build and unit tests: `.agents/commands/check --macos`

## Formatting and linting

Validation commands do not rewrite files:

- Zig: `zig fmt --check .`
- Prettier: `prettier --check .`
- Nix: `alejandra --check .`
- macOS Swift: `swiftlint lint --strict`

Use rewriting formatters only on files you intentionally changed. All C enums
in `include/ghostty/vt/` must end with
`_MAX_VALUE = GHOSTTY_ENUM_MAX_VALUE` to preserve pre-C23 enum sizing.

## Validation and review

Choose validation proportional to the changed area: run a targeted test while
developing, then the relevant default check; include `--macos` for macOS or
shared-core changes when practical. Do not describe commands as passing unless
they were actually executed. Report the exact commands, whether each passed,
failed, or was not run, and the reason for any omission.

Use an independent or sub-agent review for changes with meaningful risk or
cross-cutting effects. `.agents/commands/review-branch` is review-only and
must never be used to edit code.

## Fork and upstream maintenance

This is a long-lived fork with `upstream` configured for
`ghostty-org/ghostty`. Prefer fork-specific operational material in
`.agents/` and concise overlays such as this guide. Avoid changing upstream CI,
dependency definitions, or unrelated files solely for agent convenience.
Before a sync, inspect the upstream diff and preserve a small, easily
identifiable fork delta. Code intended for upstream must also meet the
requirements in `AI_POLICY.md`.

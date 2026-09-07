---
name: macos-development
description: >-
  Use when changing Ghostty's macOS app, its Xcode integration, or shared Zig
  code that must be validated with the macOS app.
---

# Ghostty macOS Development

The Nix dev shell provides project tools, but macOS application builds use the
system-selected full Xcode. `macos/build.nu` deliberately runs `xcodebuild`
with a clean environment so Nix compiler flags do not leak into Xcode.

For a change outside `macos/`, refresh the underlying framework first:

```sh
zig build -Demit-macos-app=false
macos/build.nu
```

For native unit tests, use:

```sh
macos/build.nu --action test
```

The wrapper skips `GhosttyUITests` for CLI runs because they require special
permissions. Do not claim UI coverage from that command. Run
`swiftlint lint --strict` for changed Swift code.

For AppleScript work, follow `macos/AGENTS.md`: build the app, target the
absolute app-bundle path in `osascript`, then quit that same app bundle when
the test finishes.

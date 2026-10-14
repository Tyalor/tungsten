# Changelog

All changes relative to the upstream Xenia Edge commit this fork tracks.

## Unreleased

- **macOS: data folder moved to `~/Library/Application Support/Xenia`.** The
  storage root used the XDG path `~/.local/share/Xenia`, which is not where
  macOS apps keep their data. The platform convention is used now, and an
  existing legacy folder is moved across on first launch, so configs, saves and
  caches carry over. If the move cannot happen the legacy folder keeps being
  used and a warning is logged. `XDG_DATA_HOME`, `--storage_root`, and
  `portable.txt` still override it. (Xenia Edge issue #279)
- **macOS: release builds no longer carry `get-task-allow`.** The one
  entitlements file was applied to every configuration, so Release bundles
  shipped a debug-only entitlement that lets any process attach to the
  emulator and that Apple's notarization service rejects outright. Release
  and RelWithDebInfo now sign with `xenia.entitlements` (hardened runtime
  plus the JIT exception); Debug signs with `xenia-debug.entitlements`,
  which keeps `get-task-allow` so lldb can attach.
- **Window: a saved position that is off every display is ignored.** The
  main window restored its last top-left corner without checking that a
  display still contains it. A corrupt value or a disconnected external
  monitor then produced a frame AppKit rejects with
  `NSInternalInconsistencyException` (XeniOS issue #16, same code lineage),
  or an unreachable window elsewhere. The position is validated against the
  connected displays and falls back to the toolkit default.

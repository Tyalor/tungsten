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

# Contributing

Thanks for helping. Two rules come before everything else.

## 1. Clean-room only

Do not submit anything derived from leaked Microsoft source code, the Xbox 360
XDK/SDK, Xbox 360 system software, decompiled retail binaries, or any
non-public documentation. All kernel and hardware behavior in this emulator is
reimplemented from observation of legally owned hardware and games. If you have
had access to leaked material, say so in the pull request so we can decide how
to handle the area you are touching.

Never commit, attach to an issue, or link to:

- game dumps in any form (ISO, GoD, XEX, XBLA, DLC, title updates)
- decrypted or patched XEX files
- NAND images, kernel, dashboard, `xboxkrnl` or `xam` binaries
- CPU keys, key vaults, or any per-console secret
- save files from consoles you do not own
- test fixtures that embed retail game content

Issues asking where to get any of the above are closed without reply.

## 2. Keep fixes upstreamable

Most changes belong in [Xenia Edge](https://github.com/has207/xenia-edge).
Make each fix a single commit that builds on its own, with a message that says
what was wrong and how you know it is fixed (title, scene, before/after
behavior). Commits that mix unrelated fixes will be asked to split.

Keep every existing copyright header. New files get the standard Xenia header
with your name and the year.

## Style

Run `./xb format` before pushing. The project uses clang-format with the
configuration in the repository root.

## Reporting bugs

Include the macOS version, the Mac model, the emulator commit, the title ID,
the log file from `~/Library/Application Support/Xenia/xenia.log`, and what you
expected to see. A screen recording of the failure is worth a lot.

# GUI survey, naming and icon direction (2026-10-14)

Survey of Dolphin, RPCS3, PCSX2, Ryujinx, shadPS4, xemu and OpenEmu main-window UX, distilled into ranked recommendations for this emulator, plus the naming check that produced "Tungsten".

## What the good ones do

- **PCSX2** has the best onboarding: a wizard (Language/Theme > BIOS > Game folders with recursion > Controller auto-map > Done) and the library scans immediately. Per-game settings are the global dialog with a "use global" checkbox per field. https://pcsx2.net/docs/advanced/cli
- **OpenEmu** is the Mac reference: drop a ROM on the window and it is filed; iTunes-style sidebar; grid with zoom slider; Cmd+F search; hover HUD in-game; Cmd shortcuts for everything; Sparkle updates. https://openemu.org
- **Dolphin**: empty list says what to do ("could not find any ISOs", double-click to add folder); big toolbar buttons (Open / Play / Config / Graphics / Controllers); covers from GameTDB opt-in. https://forums.dolphin-emu.org/Thread-cover-art-not-downloading-in-grid-mode
- **RPCS3**: missing firmware called out in the title bar; icons from the game's own ICON0 so no network; compatibility column coloured from the online DB; per-game "Create Custom Configuration". https://retrodeck.readthedocs.io/en/cooker/wiki_emulator_guides/rpcs3/rpcs3-guide/
- **Ryujinx**: the input window (device per player, console-pad picture, press-to-map, profiles) is the one users praise. https://emulation.gametechwiki.com/index.php/Ryujinx
- **shadPS4**: compat colouring, per-game patches and cheats, "check for updates at startup" with changelog. https://www.shadps4.net/quickstart
- **xemu**: single ImGui settings window with inline required-files status; in-app update banner with an Update button. https://xemu.app/docs/required-files/

## Ranked recommendations (each under a day in wxWidgets)

Marked (first-run) are the ones that decide whether a new user reaches a game.

1. (first-run) Empty library state: big "Add Game Folder..." button plus a drag-and-drop hint. **Done in this fork.**
2. (first-run) First-launch wizard (wxWizard): game folder(s) with "scan subfolders" > controller auto-detect > done.
3. (first-run) Controller page that auto-maps any SDL pad and shows an Xbox-pad graphic lighting up as buttons are pressed.
4. (first-run) Non-modal status strip: controller connected, GPU backend, warnings.
5. Grid/List toggle with a zoom slider; cover art from the title's own icon first, then a user-pointed cover folder.
6. Cmd+F focuses search; sort by Title, Last Played, Play Time, Compatibility.
7. Compatibility badge per game from the bundled compatibility JSON, with the issue link in the tooltip.
8. Right-click > Game Settings... with a "use global" checkbox per field, writing the per-title TOML.
9. Native macOS menu bar: App menu with About and Settings (Cmd+,), File > Open (Cmd+O), View > Grid/List, Emulation > Pause, Fullscreen (Ctrl+Cmd+F).
10. Info pane: cover, Title ID, media type, last played, play time, Launch button; Cmd+I toggles.
11. In-game: Esc pauses with Resume / Settings / Quit overlay; FPS/speed OSD toggle listed in Help > Keyboard Shortcuts.
12. Update banner (wxInfoBar) instead of a modal, with "check at startup" toggle.
13. Dark mode: custom grid cells use wxSystemSettings colours only.
14. Notarized DMG with a drag-to-Applications background. **Workflow added in this fork; needs Developer ID secrets.**
15. "Recent" row at the top of the grid plus a Continue button.
16. Title bar shows only the product and the running game; build info lives in About. **Done in this fork.**

## Naming

Bare GitHub orgs for argon, xenon, silicon360, halide, nova360 and orbit are all taken. Collisions found:

| Name | Problem |
|---|---|
| Argon | "Argon: Modern Retro Gaming", a commercial multi-system emulator by Mark/Space. |
| Xenon | Microsoft's Xbox 360 codename and CPU name; reads as Xbox-internal. |
| Silicon360 / Nova360 | "360" invites the Xbox 360 association; weak marks; domains taken. |
| Halide | Registered USPTO word mark in Class 9 software (Lux Optics). |
| Orbit | Expo Orbit (simulator launcher) and an app switcher; crowded. |

Fresh candidates: **Tungsten** (recommended), Osmium, Meridian (Meridian Audio holds an active mark nearby).

Tungsten keeps the periodic-table lineage without the Xenia name, carries no "360" or Microsoft codename, and no registered Class 9 software mark surfaced in the search. The `tungsten-emu` GitHub org and `tungsten-emu.org` showed no DNS. Run a USPTO search for TUNGSTEN in Class 9 (games/software) before any public launch.

## Icon direction

Tungsten-grey squircle, dark graphite-to-charcoal gradient, one bright warm-white filament arc drawn as a single stroke that reads as a D-pad cross or power symbol at small sizes. Build as three Icon Composer layers (background, glass-raised filament, specular) so macOS 26 Default/Dark/Clear/Tinted modes render. No green, no sphere, no X.

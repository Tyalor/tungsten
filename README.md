<h1 align="center">Xenia Silicon</h1>

<p align="center">An Xbox 360 emulator research project, built for Apple Silicon Macs.</p>

Xenia Silicon is a community fork of [Xenia Edge](https://github.com/has207/xenia-edge),
itself a fork of [Xenia Canary](https://github.com/xenia-canary/xenia-canary) and the
original [Xenia](https://github.com/xenia-project/xenia) research project. It exists to
move the native macOS port forward: the AArch64 JIT backend, the Metal renderer, 16 KB
page memory handling, audio pacing on Apple hardware, and a signed, notarized app.

This project is not affiliated with, endorsed by, or sponsored by the Xenia project,
Xenia Canary, Xenia Edge, Microsoft, or Apple. The name "Xenia" is used only to
identify the upstream code this fork is derived from.

## Status

Alpha. It builds and runs on macOS 15+ on Apple Silicon. Expect crashes, missing
features, and large game-to-game variance. The macOS compatibility picture is tracked
in the upstream Edge and XeniOS issue trackers; this fork's own fixes are listed in the
[changelog](CHANGELOG.md).

## What this fork changes

See [CHANGELOG.md](CHANGELOG.md). Fixes are kept as small, independent commits so they
can be sent upstream to Xenia Edge, which is where most of them should live long term.

## Building on macOS

Requirements: macOS 15 or later, Xcode 26 with the Metal Toolchain component,
CMake, Ninja, and a Python 3.10+ with `meson`, `mako`, `pyyaml`, `packaging` for
Mesa's `spirv_to_dxil`.

```sh
git clone --recursive https://github.com/Tyalor/xenia-silicon.git
cd xenia-silicon

xcodebuild -downloadComponent MetalToolchain   # once
python3 -m venv .venv && .venv/bin/pip install meson mako pyyaml packaging
export PATH="$PWD/.venv/bin:$PATH"

./xb slang        # Slang shader compiler
./xb msc          # Apple Metal Shader Converter dylib
./xb fetchdata    # community game patches
./xb setup
./xb build --config=Release --target=xenia-app
open build/bin/macOS/Release/Xenia-edge.app
```

The full upstream guide, including Windows and Linux, is in
[docs/building.md](docs/building.md).

## Running a release build

Release builds are ad-hoc signed with the JIT entitlement. On first launch macOS
will refuse to open the app. Go to System Settings > Privacy & Security and click
"Open Anyway", then launch it again.

Data lives in `~/Library/Application Support/Xenia`. An existing
`~/.local/share/Xenia` folder from older macOS builds is moved there on first run.

## What you need to bring

Xenia Silicon contains no games, no Xbox 360 system software, no firmware, and no
console keys, and none will be provided or linked to. Dump your own legally owned
discs and Xbox Live Arcade titles. Requests for game files, keys, or system
software will be closed, and piracy discussion is not welcome in issues or pull
requests.

## Research notes

Background material gathered while starting this fork lives in
[docs/research](docs/research/): a legal review of forking a BSD-3-Clause emulator
and distributing it on macOS, and a map of the ARM64 and Apple Silicon work across
the Xenia lineage, with the open issues and pull requests worth picking up.

## License

BSD 3-Clause. See [LICENSE](LICENSE). Third-party components and their licenses are
listed in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

## Disclaimer

This project is for research and educational purposes only, to study how the Xbox 360
works and to run software on hardware it was not designed for. The authors do not
condone or enable piracy. Use it only with games and content you legally own.

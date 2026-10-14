# Backend roadmap for Apple Silicon (2026-10-14)

What the CPU, GPU and audio back ends need, in priority order, with the evidence behind each item. See `arm64-landscape.md` for the full survey.

## Where the code stands

- CPU: the AArch64 JIT (`src/xenia/cpu/backend/a64/`, oaknut assembler) is the one every port uses; nobody runs the x64 backend under Rosetta except the Intel build. Edge already carries the lwarx/stwcx software reservation, full Q8-Q15 preservation across host calls, and the 16 KB host page / 4 KB guest page protection sync that the Gears of War fix bundle introduced.
- GPU: the native Metal backend is the default. Guest shaders go ucode -> DXBC -> DXIL (Mesa spirv_to_dxil) -> Apple Metal Shader Converter -> AIR. That three-stage pipeline is the known cost. MoltenVK is the fallback.
- Audio: FFmpeg (LGPL build) decodes XMA; the XMA self-deadlock is already fixed in Edge.
- Memory: MAP_JIT + pthread_jit_write_protect_np; the 0xE0000000 view cannot alias at a 4 KB offset so the JIT adds 0x1000 for that range.

## 1. wmarti's AArch64 optimisation branch (biggest CPU win on the table)

`wmarti/xenia-edge` branch `a64-fixes-on-edge` (92 commits, last push 2026-09-04) is an audited AArch64 backend: precise FPCR tracking with per-edge meets, NZCV fusion for arbitrary nonzero conditions, zip-based vector merges instead of table permutes, constant shift folding, SELECT fed from live ANDS flags, stackpoints stored in guest frames, address zero-extend folded into addressing, and a per-function CNTVCT_EL0 profiler.

It has drifted from Edge: Edge is 634 commits past the merge base. A dry-run `git apply --check` of each commit against current Edge (`a64-cherry-check.txt` in the fork's research folder) shows only 5 of 92 apply cleanly:

- `[A64] Pair the guest/host return-address slots for stp/ldp`
- `[A64] Branch to the epilog directly when the near bound holds`
- `[A64] Fold constant vector shift amounts at compile time`
- `[A64] Feed SELECT from live ANDS flags`
- `[Build] Gate spirv_to_dxil on XENIA_BUILD_EMULATOR`

Plan: land those five first (each is self-contained and builds on its own), then port the FPCR tracker series by hand as one unit, since it is the part with the most measurable effect on float-heavy titles. Everything needs a game to validate; the PPC instruction tests under `src/xenia/cpu/ppc/testing` and the VMX fuzz test cover correctness, and the CNTVCT profiler from the same branch gives the before/after numbers.

## 2. Open Edge pull requests from the XeniOS author that target this hardware

- #247 remap on the effective address and shorten guest addressing (A64)
- #254 release the core from guest wait loops (A64; spin-waits burn a P-core)
- #252 block the timer queue thread on POSIX instead of spinning
- #251 park host-thread guest threads that keep delaying for zero time
- #257 / #248 dead condition-register store elimination and context-store elimination
- #300 Metal tessellation on the DXIL path
- #301 unwatch destroyed textures under the global lock

These are reviewed upstream on their own schedule; tracking them in this fork means cherry-picking once they merge, not before.

## 3. Metal renderer

- `metal_sync_on_swap` / `metal_sync_on_resolve` exist because the debug render-target readback used to serialise CPU and GPU; the real fix is tracking resolve completion with MTLSharedEvent so neither wait is needed.
- Resolve readback to guest RAM (XeniOS #47) fixes CPU-side auto-exposure in Need for Speed: Most Wanted and is small.
- Shader pipeline: a direct DXBC-to-MSL translator would remove the Metal Shader Converter dependency and its compile latency, but that is months of work. Shorter term, persist the converted MTLLibrary per shader hash on disk so second launches skip conversion.

## 4. Audio pacing

XeniOS #45 paces guest audio callbacks to the 5.333 ms Xenos frame because Apple audio drivers release several buffers per IO cycle. Its author measured about 0% silent frames on a Mac already, so it is an iOS fix; keep it in view in case Core Audio on macOS shows the same burst pattern under load.

## 5. Memory protection

`BaseHeap::Protect` with host-unaligned ranges is handled, but NHL Legacy still reports mprotect failures on 16 KB pages (xenia-mac v0.11 notes). Reproduce with that title once a dump is available.

## What was not done here

No game dumps exist on this machine, so nothing above has been runtime-tested in this fork beyond the emulator starting, scanning an empty library and shutting down cleanly. Backend changes without a title to run are not safe to ship, which is why the five clean cherry-picks were not applied yet.

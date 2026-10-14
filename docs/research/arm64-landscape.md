# Xenia on ARM64 / Apple Silicon: landscape and reuse map (2026-10-14)

## Lineage

1. **xenia-project/xenia** (upstream, dormant since 2026-02). x64-only (xbyak). Wunkolo's ARM64 backend unmerged in PR #2259 (oaknut, ARMv8.1 required); Windows ARM64 build in #2258.
2. **xenia-canary/xenia-canary** (`canary_experimental`, active). Merged `src/xenia/cpu/backend/a64/` on 2026-03-20 (commit 883c203), then Wunkolo's optimisation train (#927-#948: FEAT_LSE, CNTVCT_EL0 clock, zva memset, cache-line flush, movi folding). No macOS layer. Windows-on-ARM CI (#938, Snapdragon X Elite) deliberately unmerged.
3. **has207/xenia-edge** (`edge`, active daily, BSD-3). Fork of canary. Where the Mac port lives: wmarti upstreamed the Metal backend (#79), A64 selection on ARM64 (#88) and the backend merge (#77) in Jan-Feb 2026. Every release ships xenia_edge_macos.dmg. Builds on arm64 Linux but maintainer refuses aarch64 Linux releases (#271).
4. **wmarti/xenia-mac** (stale since 2026-02-22). Its v0.2/v0.2.1 releases are Edge builds. wmarti's live A64 work: **wmarti/xenia-edge** branch `a64-fixes-on-edge` (pushed 2026-09-04: FPCR tracking, NZCV fusion, zip merges, stackpoints in guest frames, CNTVCT profiler).
5. **xenios-jp/XeniOS** (`xenios`, last push 2026-06-19). iOS+macOS fork of Edge. xenios-jp is now the most active Apple contributor to Edge (PRs #241-#258, #292-#301, Aug-Oct 2026).

Android forks (aX360e, X360 Mobile, XenDroid) use the same backend lineage, no useful source.

## 1. PPC -> ARM64 JIT

Native a64 backend everywhere; nobody uses FEX. Only translation-layer attempt: canary #839 ("Rosette" x86->NEON via Zig), stalled. Rosetta 2 is used only by the x86_64 macOS build, which xenia-mac v0.2.1 still called "more stable"; the arm64 build had "severe audio problems".

Backend: `src/xenia/cpu/backend/a64/` (a64_emitter, a64_sequences, a64_seq_memory/vector, a64_code_cache_posix.cc with hand-written DWARF CFI). Assembler: oaknut.

Known wrong-code classes still open (xenia-mac PR #25, Oct 13 2026): Q8-Q15 not fully preserved across host calls (AAPCS64 saves only low 64 bits); Q31 clobbering Q30 save slot; lwarx/stwcx exclusive monitor cleared by intervening guest code (needs software reservation + CAS like x64); `SSHR #0` rejected by oaknut. Edge open A64 PRs: #247 (0xE0000000 remap on effective address), #254 (release core from guest spin-waits).

## 2. Graphics

- **Native Metal backend** (`src/xenia/gpu/metal/`, default on macOS). Pipeline: ucode -> DxbcShaderTranslator -> DXBC -> dxbc2dxil (or mesa spirv_to_dxil) -> Apple Metal Shader Converter -> Metal IR -> MTLLibrary. This three-stage translation is the "large performance penalty". "Unable to setup command processor internal state" (xenia-mac #1, #13, Edge #189) was a min-OS pin, fixed in Edge; needs macOS 26 Metal 4 APIs in recent builds.
- **MoltenVK** via the Vulkan backend: canary #1247 (closed), Edge #154 (closed). Fallback renderer.

Open Metal work: Edge #300 (tessellation on DXIL), XeniOS #47 (resolve readback to guest RAM, fixes NFS:MW auto-exposure), XeniOS #46 (shader storage rewind), xenia-mac #25 `metal_sync_on_swap/resolve` (debug waitUntilCompleted was 75% of GPU-thread time).

## 3. Audio

FFmpeg submodule rebuilt for ARM64 (Edge #92). Mac bugs: XMA decoder self-deadlock (Work() -> Clear() re-locking non-recursive mutex, xenia-mac #25); tinny/stuttering audio; XeniOS #45 paces guest callbacks to 5.333 ms because Apple audio drivers release several buffers per IO cycle.

## 4. Memory, 16 KB pages, W^X

Handled in Edge: `memory_posix.cc` uses getpagesize() (16 KB), MAP_JIT + pthread_jit_write_protect_np, mach_vm_region for QueryProtect. `memory.cc` skips mprotect-based commit when host page > 4 KB; 0xE0000000 view cannot alias 0xC0000000 at a 4 KB offset so the JIT adds 0x1000 (Edge #247). Low-4GB mapping disallowed so code cache lives in a 4 GB-aligned region elsewhere.

Still open: BaseHeap::Protect skipping mprotect for host-unaligned ranges (GoW2 freeze; fix = most-permissive-of-covered-pages, xenia-mac #25). mprotect failures in NHL Legacy.

## 5. Windows patching overlap

xenia-canary/game-patches: 501 `.patch.toml` files. Loader (`src/xenia/patcher/`) has no platform ifdefs, so patches apply unchanged on Mac. Windows ARM64 (Snapdragon) shares the identical a64 backend; MSVC-ARM64 fixes (#927) and `xb build --target-arch arm64` plumbing exist in Edge. Snapdragon testers: D3D12 unusably slow, Vulkan fast but unstable.

## 6. Actionable open items

xenia-mac: #25 (Gears fix bundle, unreviewed; split and retarget at Edge), #22 (xb build fails, SPIRV-Headers), #12 (Qt resets backend), #19 Forza Horizon, #24 PGR4, #16 Minecraft. Halo cutscene->gameplay transition, GTA IV menu hang: undiagnosed, no issue filed.

XeniOS: #39 JIT broker gating (iOS), #40 XamLoaderLaunchTitle relaunch C00000BB, #37 Banjo glyph scrambling on Metal, #16 window Y crash, #45/#47/#48 unreviewed PRs, #24/#26 controller/achievement UI.

Edge: #279 move data dir to Application Support, #215 Steam Controller on macOS.

## 7. Recommendation

Fork **has207/xenia-edge@edge**. It carries the a64 backend, Metal backend and 16 KB/MAP_JIT memory layer and is the merge target for both wmarti's and xenios-jp's A64 work. Cherry-pick wmarti/xenia-edge `a64-fixes-on-edge` and xenia-mac #25 (split), copy XeniOS `apple-publish.yml` for signing, send fixes upstream.

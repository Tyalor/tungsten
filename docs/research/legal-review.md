# Legal review: forking Xenia into a macOS/Apple Silicon project

*Research summary compiled 2026-10-14. Not legal advice.*

## 1. BSD-3-Clause obligations

Upstream `xenia-project/xenia` is Copyright (c) 2015 Ben Vanik, BSD-3-Clause. `has207/xenia-edge`, `wmarti/xenia-mac` and `xenios-jp/XeniOS` are all BSD-3-Clause, so a new BSD-3 fork is compatible with all of them.

Must do:
- Retain copyright notice, three conditions and disclaimer in source (clause 1).
- Reproduce the same in binary distributions: About box, LICENSE / THIRD_PARTY file inside the .app, release page (clause 2).
- Do not use the names of the project or contributors (Ben Vanik, has207, wmarti, xenios-jp, Canary/Edge contributors) to endorse or promote the derivative (clause 3). Renaming the fork is permitted and preferable. "Based on Xenia" as a factual statement is fine; "Xenia for Mac" as a product name is not.
- Keep every existing copyright header. Add your own line for new files. Inherited code cannot be relicensed.

Third-party components: xbyak (BSD-3), oaknut (MIT), imgui/fmt/cxxopts/rapidjson/xxhash/cpptoml/utfcpp/stb (MIT/BSD/zlib), SDL (zlib), Vulkan-Headers/VMA/SPIRV-Tools/glslang/DirectX-Headers/FidelityFX (Apache-2.0/MIT/NCSA), MoltenVK (Apache-2.0, keep NOTICE), capstone (BSD), mesa (MIT). **FFmpeg fork is LGPL-2.1+**: the only copyleft dependency. Static linking is fine when source is published; comply with LGPL section 6 and call it out in the NOTICE. No GPL runtime component found; binutils-ppc (GPL) is a dev tool only.

## 2. US law

- Sony v. Connectix (9th Cir. 2000): reverse engineering a BIOS to build a non-infringing emulator was fair use. https://digital-law-online.info/cases/53PQ2D1705.htm
- Sony v. Bleem (9th Cir. 2000): game screenshots in comparative ads were fair use. https://www.copyright.gov/fair-use/summaries/sonycomputer-bleem-9thcir2000.pdf
- DMCA 1201 is the modern risk. Nintendo v. Tropic Haze (Yuzu) settled for $2.4M in 2024 over decrypting titles with leaked keys; Nintendo then had 4,238 Ryujinx fork repos removed from GitHub. No merits ruling, but it is the enforcement playbook. https://www.engadget.com/makers-of-switch-emulator-yuzu-quickly-settle-with-nintendo-for-24-million-203204698.html

Xenia needs no Microsoft binaries: the kernel is HLE (its own C++ reimplementations of xboxkrnl/xam). However `src/xenia/cpu/xex_module.cc` hardcodes the XEX2 retail AES key, public since ~2007, used to decrypt XEX payloads. This is the single highest-exposure inherited line, shared with every Xenia fork, never enforced by Microsoft.

## 3. Microsoft posture

- Xenia has been on GitHub since January 2013. No Microsoft DMCA notice against xenia-project, canary, edge or any fork appears in the public github/dmca log.
- Microsoft enforcement has been platform policy only (Store policy 10.13.10 bans emulators on Xbox consoles). Irrelevant to macOS.
- Xbox Labs memo (2026-10-13, https://news.xbox.com/en-us/2026/10/13/introducing-xbox-labs/): a small group under the CTO exploring decompilation, Apple Silicon ports of Windows builds and browser ports, "building in the open". Mentions neither emulation nor 360, grants no licence, repeats "responsible stewards of IP". Net: neutral to slightly positive tolerance signal, not a safe harbor.

## 4. Apple-specific

- Distributing unsigned or un-notarized apps via GitHub Releases is legal. Users on Sequoia+ need System Settings > Privacy & Security > Open Anyway.
- `com.apple.security.cs.allow-jit` is a documented Hardened Runtime exception; notarization with it is permitted (Dolphin, UTM do it). Prefer allow-jit + MAP_JIT + pthread_jit_write_protect_np over allow-unsigned-executable-memory.
- MoltenVK (Apache-2.0) and Metal (Xcode SDK agreement) raise no redistribution issues.

## 5. Checklist

Include: LICENSE (full BSD-3 with original Vanik notice plus your line), THIRD_PARTY_NOTICES.md covering every submodule with explicit FFmpeg (LGPL) and Apache entries, README with neutral name, "based on Xenia / Xenia Canary / Xenia Edge" attribution, disclaimer (research project, no games/keys/system files, dump your own), CONTRIBUTING banning leaked SDK/firmware/source.

Never commit: game dumps (ISO/GoD/XEX/XBLA), decrypted or patched XEX, NAND/kernel/dashboard/xboxkrnl, leaked XDK, per-console keys (CPU key, KV), other people's saves, test fixtures embedding retail content.

Prebuilt binaries on GitHub Releases: fine and standard for Canary, Edge, xenia-mac and XeniOS. Bundle LICENSE and third-party notices in the .dmg.

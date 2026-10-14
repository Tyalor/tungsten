# Third-party notices

Xenia Silicon is derived from the following projects, all licensed under the
BSD 3-Clause License. Their copyright notices are retained in the source tree and
in `LICENSE`.

- Xenia, Copyright (c) 2015 Ben Vanik and contributors. https://github.com/xenia-project/xenia
- Xenia Canary, Copyright (c) Xenia Canary contributors. https://github.com/xenia-canary/xenia-canary
- Xenia Edge, Copyright (c) has207 and contributors. https://github.com/has207/xenia-edge
- xenia-mac, Copyright (c) wmarti. https://github.com/wmarti/xenia-mac
- XeniOS, Copyright (c) xenios-jp. https://github.com/xenios-jp/XeniOS

None of these projects or their authors endorse or are responsible for Xenia
Silicon.

## Bundled and linked components

Every entry below lives under `third_party/` as a git submodule or vendored copy.
The license text for each is in that directory. This list is for the macOS build;
some entries are only used on other platforms.

| Component | License | Notes |
|---|---|---|
| FFmpeg (libavcodec, libavformat, libavutil) | LGPL-2.1-or-later | Built without `--enable-gpl` (no `CONFIG_GPL` in `third_party/ffmpeg-xenia/config.h`). Used for XMA and WMA Pro audio decoding. Statically linked; full source is in this repository, which satisfies LGPL section 6 for a source distribution. |
| libusb | LGPL-2.1-or-later | Statically linked; same terms as FFmpeg. |
| Apple Metal Shader Converter (`libmetalirconverter.dylib`) | Apple proprietary | Downloaded at build time by `./xb msc` and copied into `Contents/Frameworks` of the app. Redistribution is governed by Apple's Metal Shader Converter license agreement, which permits bundling the runtime library with applications. Not open source. |
| MoltenVK | Apache-2.0 | NOTICE retained in `third_party/MoltenVK`. |
| Vulkan-Headers, SPIRV-Headers, SPIRV-Tools, SPIRV-Cross, glslang | Apache-2.0 / BSD-3 / MIT | Khronos components. |
| VulkanMemoryAllocator | MIT | |
| DirectXShaderCompiler | NCSA (University of Illinois) | Submodule only; not built for the Metal path. |
| DirectX-Headers | MIT | |
| Mesa (`spirv_to_dxil`) | MIT | |
| FidelityFX-CAS, FidelityFX-FSR | MIT | |
| metal-cpp | Apache-2.0 | Apple's C++ bindings for Metal. |
| SDL3 | zlib | |
| wxWidgets | wxWindows Library Licence (LGPL with static-link exception) | |
| imgui | MIT | |
| fmt | MIT | |
| cxxopts | MIT | |
| rapidjson | MIT | |
| tomlplusplus | MIT | |
| pugixml | MIT | |
| disruptorplus | MIT | |
| date | MIT | |
| discord-rpc | MIT | |
| cereal | BSD-3 | |
| zarchive | MIT | |
| xxhash | BSD-2 | |
| zstd | BSD-3 (dual BSD/GPLv2; used under BSD) | |
| zlib-ng | zlib | |
| snappy | BSD-3 | |
| capstone | BSD-3 | |
| mbedtls | Apache-2.0 | |
| asio | Boost Software License 1.0 | |
| boost.context | Boost Software License 1.0 | |
| utfcpp | Boost Software License 1.0 | |
| catch | Boost Software License 1.0 | Tests only. |
| miniaudio | Public domain / MIT-0 | |
| xbyak | BSD-3 | x86-64 JIT assembler. |
| xbyak_aarch64 | Apache-2.0 | |
| oaknut (vendored in the a64 backend) | MIT | AArch64 JIT assembler. |
| cpplint | BSD-3 | Dev tool only, not shipped. |
| Slang shader compiler | Apache-2.0 with LLVM exception | Build-time tool, not shipped. |

## Community game patches

`./xb fetchdata` pulls https://github.com/xenia-canary/game-patches. Those files
are data under that repository's own terms and are not part of this source tree.

## What is deliberately not here

No Microsoft Xbox 360 kernel, dashboard, firmware, NAND images, SDK or XDK files,
per-console keys, or game content are included, and contributions containing them
are rejected. See `CONTRIBUTING.md`.

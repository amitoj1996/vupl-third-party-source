# Vupl Windows playback library sources — 2026-10-16

This distribution provides corresponding source for the separate native
libmpv-2.dll shipped in Vupl for Windows. It contains no Vupl application source,
credentials, customer libraries, or private build logs.

Original builder: https://github.com/zhongfly/mpv-winbuild/actions/runs/38979014717
Builder commit: 8a39c5134b8e16f104ee56a5d85e36a592ad9477
mpv-winbuild-cmake commit: 1f8d2e76255abc48f0456d9be99b4cab9c6566e6
mpv: dde0671072723c311df874a32dc4e48b5197ca62 (LGPL 2.1 or later upstream)
FFmpeg: 3ca52f0e1278b73bafae81d62ae95e213f757d3a (this build LGPL 3 or later)
x64 DLL SHA256: ef34bfb93b55e1490ed00fbb8192dbff7880a6ebc798e11739b6b8e5cf8750c3
ARM64 DLL SHA256: 1275886378ddde253c6876d2d8093c8b837b7c95a9e0024eabba3069ed950bab

## Contents

sources/ contains pinned upstream archives for the native dependency closure.
crates/ contains every registry crate from the subrandr workspace Cargo.lock.
The download-results JSON files record URLs, revisions and verified SHA256 values.
SHA256SUMS covers this distribution. Some included sources are build-only,
optional, test or non-Windows dependencies; inclusion does not imply linkage.

For patched packages, source archives are the upstream bases. Patches in
build-recipes/packages/ supply the remaining source changes. A local hash shown
as "ahead" in a job summary refers to these locally applied patch commits, not a
public upstream revision. Explicit older recipe resets are retained.

build-recipes/ is the recipe tree after the builder's skip-unused-stuff and
compile-lgpl-libmpv patches. builder/ contains original workflow/scripts/patches.
build-logs/ contains public upstream configure/build logs for both architectures.
dependency-revisions.json preserves the two upstream job dependency tables.
LICENSES/ contains the component notices and LGPL/GPL texts. The licences of each
upstream component apply; this distribution does not relicense them.

## Rebuilding

Use a Linux/Arch environment with dependencies/toolchain setup from
builder/.github/workflows/mpv.yml and builder/.github/workflows/llvm.yml.
Recorded compiler: clang 23.1.2, LLVM 03b58ead1a5d7caae4ab76ec69d66cfa896da372.
Recorded Rust nightly: 420ed2a0c3d7225b1744266fd884d431b4d8cfe0 (2026-09-18).
Toolchain recipes are included in build-recipes/toolchain/.

1. Run python3 prepare-sources.py in a fresh extraction. It prepares native source
   snapshots and the Cargo vendor directory without running third-party code.
   It creates rebuild-recipes/ with native downloads and updates disabled.
2. Supply a MinGW clang toolchain in clang_root, as in the workflow, and the Rust
   x86_64-pc-windows-gnullvm and/or aarch64-pc-windows-gnullvm targets.
3. Configure and build, replacing x86_64/build64 with aarch64/buildaarch64 for ARM64:

       cmake -S rebuild-recipes -B build64 -G Ninja \
         -DTARGET_ARCH=x86_64-w64-mingw32 -DCOMPILER_TOOLCHAIN=clang \
         -DCMAKE_INSTALL_PREFIX="$PWD/clang_root" \
         -DMINGW_INSTALL_PREFIX="$PWD/build64/install/x86_64-w64-mingw32" \
         -DSINGLE_SOURCE_LOCATION="$PWD/src_packages" \
         -DRUSTUP_LOCATION="$PWD/install_rustup" -DCLANG_PACKAGES_LTO=ON
       ninja -C build64 mpv

Do not run the original build.sh's ninja update against the pinned tree. Retain
mpv -Dgpl=false and omit FFmpeg --enable-gpl/--enable-nonfree. FFmpeg's
--enable-version3 is retained. The included configure logs record enabled options.
The toolchain may be built from its recipes or supplied separately. Shared system
libraries and compiler toolchains are outside the native library source set.

Use the resulting API-major-2 libmpv-2.dll through the mechanism documented in
replace-playback-library.txt. No Vupl source, application object files or signing
key are needed to use an ABI-compatible modified library. The application licence
permits modifications to LGPL components and reverse engineering needed to debug
those modifications.

The source collection and checksums have been verified. A bit-for-bit rebuild of
the original third-party DLLs from this mirror has not been performed. Timestamps,
toolchains and original caches may affect binary hashes.

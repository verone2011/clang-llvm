# Greenforce Clang

You can see information regarding the latest greenforce clang build in `clang-18.0.0-16082023-1643-info.txt`, the latest build is at `16082023` tag (see `latest.txt`). To use greenforce clang you can download it directly from the GitHub releases, or you can follow the simple command below:

```bash
# Create greenforce clang folder
mkdir -p "$HOME/toolchains/greenforce-clang";

# Create folder environment (optional)
GREENFORCE_DIR="$HOME/toolchains/greenforce-clang";

# Download and extract to folder
wget -c https://github.com/greenforce-project/clang-llvm/releases/download/16082023/greenforce-clang-18.0.0-16082023-1643.tar.gz -O - | tar -xz -C "${GREENFORCE_DIR}"

```

## Host compatibility

This toolchain is built on Ubuntu 22.04.2 LTS, which uses glibc 2.35. Compatibility with older distributions cannot be guaranteed. Other libc implementations (such as musl) are not supported.

## Building Linux

Make sure you have this toolchain in your `PATH`:

```bash

export PATH="$HOME/toolchains/greenforce-clang/bin:$PATH"

```

For an AArch64 cross-compilation setup, you must set the following variables. Some of them can be environment variables, but some must be passed directly to `make` as a command-line argument. It is recommended to pass **all** of them as `make` arguments to avoid confusing errors:

- `CC=clang` (must be passed directly to `make`)
- `CROSS_COMPILE=aarch64-linux-gnu-`
- If your kernel has a 32-bit vDSO: `CROSS_COMPILE_ARM32=arm-linux-gnueabi-`

Optionally, you can also choose to use as many LLVM tools as possible to reduce reliance on binutils. All of these must be passed directly to `make`:

- `AR=llvm-ar`
- `NM=llvm-nm`
- `OBJCOPY=llvm-objcopy`
- `OBJDUMP=llvm-objdump`
- `STRIP=llvm-strip`

Note, however, that additional kernel patches may be required for these LLVM tools to work. It is also possible to replace the binutils linkers (`lf.bfd` and `ld.gold`) with `lld` and use Clang's integrated assembler for inline assembly in C code, but that will require many more kernel patches and it is currently impossible to use the integrated assembler for *all* assembly code in the kernel.

Android kernels older than 4.14 will require patches for compiling with any Clang toolchain to work; those patches are out of the scope of this project. See [android-kernel-clang](https://github.com/nathanchance/android-kernel-clang) for more information.

Android kernels 4.19 and newer use the upstream variable `CROSS_COMPILE_COMPAT`. When building these kernels, replace `CROSS_COMPILE_ARM32` in your commands and scripts with `CROSS_COMPILE_COMPAT`.

### Differences from other toolchains

Greenforce Clang has been designed to be easy-to-use compared to other toolchains, such as [AOSP Clang](https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/). The differences are as follows:

- `CLANG_TRIPLE` does not need to be set because we don't use AOSP binutils.
- `LD_LIBRARY_PATH` does not need to be set because we set library load paths in the toolchain.
- No separate GCC/binutils toolchains are necessary; all tools are bundled.

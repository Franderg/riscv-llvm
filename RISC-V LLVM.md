# RISC-V LLVM 

Source: [oficial riscv-tools](https://github.com/riscv/riscv-tools) and [sifive riscv-llvm](https://github.com/sifive/riscv-llvm)

### Requirements

You need install programs before use the tools

```bash
$ sudo apt-get update
$ sudo apt-get install binutils build-essential libtool texinfo gzip zip unzip patchutils curl git make cmake ninja-build automake bison flex gperf grep sed gawk python bc zlib1g-dev libexpat1-dev libmpc-dev libglib2.0-dev libfdt-dev libpixman-1-dev
```



This repository adds support for various aspects of the RISC-V instruction set to the Clang C/C++ compiler and LLVM back end.

You will need the RISC-V gcc toolchain. If you don't have RISC-V hardware then you will want to have QEMU to run your programs.

The following bash commands have been tested on Debian 9.

By default clang is generating rv32imac/rv64imac code but you can override this, and in particular if you have floating point hardware you can add "-march=rv32gc" (or rv64gc) to the clang command line to get FPU instructions with a soft float ABI.

Follow the commands:

```bash
$ git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
$ git submodule update --init --recursive
$ export RISCV=/path/to/install/riscv/toolchain
$ ./build.sh
```

By default use folder in home directory 



### Generate IR LLVM 

1. Clang compile

   ```bash
   clang-version -S -emit-llvm file.c
   ```

2.  Link them into a single one **

   ```bash
   llvm-link-version -S -v -o single.ll *.ll
   ```

3.  (Optional) Optimise your code (maybe some alias analysis)

   ```bash
   opt-version -S -O3 -aa -basicaaa -tbaa -licm single.ll -o optimised.ll
   ```

4. Generate assembly (generates a optimised.s file)

   ```bash
   llc-version optimised.ll
   ```
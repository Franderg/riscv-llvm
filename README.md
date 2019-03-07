# RISC-V LLVM 

Source: [oficial riscv-tools](https://github.com/riscv/riscv-tools) and [sifive riscv-llvm](https://github.com/sifive/riscv-llvm)

This repository adds support for various aspects of the RISC-V instruction set to the Clang C/C++ compiler and LLVM back end.

You will need the RISC-V gcc toolchain. If you don't have RISC-V hardware then you will want to have QEMU to run your programs.

The following bash commands have been tested on Debian 9.

By default clang is generating rv32imac/rv64imac code but you can override this, and in particular if you have floating point hardware you can add "-march=rv32gc" (or rv64gc) to the clang command line to get FPU instructions with a soft float ABI.

### Requirements

You need install programs before use the tools

```bash
$ sudo apt-get update
$ sudo apt-get install binutils build-essential libtool texinfo gzip zip unzip patchutils curl git make cmake ninja-build automake bison flex gperf grep sed gawk python bc zlib1g-dev libexpat1-dev libmpc-dev libglib2.0-dev libfdt-dev libpixman-1-dev
```

Now you need create a folder to work

```bash
$ mkdir riscv && cd riscv
$ mkdir _install
$ export PATH=`pwd`/_install/bin:$PATH
```

Prepare the riscv-toolchain

```bash
$ git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
$ cd riscv-gnu-toolchain
$ ./configure --prefix=`pwd`/../_install --enable-multilib
$ make -j`nproc`
$ cd ..
```

In case you need work with qemu

```bash
$ git clone https://github.com/riscv/riscv-qemu
$ cd riscv-qemu
$ ./configure --prefix=`pwd`/../_install --target-list=riscv64-linux-user,riscv32-linux-user
$ make -j`nproc` install
$ cd ..
```

LLVM

```bash
$ git clone https://github.com/sifive/riscv-llvm
$ cd riscv-llvm
$ mkdir _build
$ cd _build
$ cmake -G Ninja -DCMAKE_BUILD_TYPE="Release" \
  -DBUILD_SHARED_LIBS=True -DLLVM_USE_SPLIT_DWARF=True \
  -DCMAKE_INSTALL_PREFIX="../../_install" \
  -DLLVM_OPTIMIZED_TABLEGEN=True -DLLVM_BUILD_TESTS=False \
  -DDEFAULT_SYSROOT="../../_install/riscv64-unknown-elf" \
  -DLLVM_DEFAULT_TARGET_TRIPLE="riscv64-unknown-elf" \
  -DLLVM_TARGETS_TO_BUILD="" -DLLVM_EXPERIMENTAL_TARGETS_TO_BUILD="RISCV" \
  ../llvm
$ cmake --build . --target install
$ cd ..
```



### Generate IR LLVM 

1. Clang compile, the last number corresponds to the optimization level, it can take a value of 0,1,2 or 3.

   For riscv32 use the flag ``--target=riscv32``, for riscv64 is not necessary.

   To generate .ll file add ``-emit-llvm``

   ```bash
   $ clang-version -S -emit-llvm file.c -O1 --target=riscv32
   $ clang-version file.c -o output
   ```

2. Run the program in both forms

   ```bash
   $ lli-version file.ll
   ```

   or

   ```bash
   $ ./output
   ```

3. Generate assembly (generates a optimised.s file)

   ```bash
   $ llc-version file.ll -o file.s
   ```

4. Generate the binary for riscv

   ```bash
   $ riscv64-unknown-elf-gcc file.o -o output -march=rv32imac -mabi=ilp32
   ```

   


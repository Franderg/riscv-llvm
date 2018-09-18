# riscv-llvm
Backend compiler based in RISC-V

Original project in: https://github.com/ucb-bar/esp-llvm/tree/riscv-trunk

Low Level Virtual Machine (LLVM)
======================================================

This directory and its subdirectories contain source code for the Low Level
Virtual Machine, a toolkit for the construction of highly optimized compilers,
optimizers, and runtime environments.

LLVM is open source software. You may freely distribute it under the terms of
the license agreement found in `LICENSE.txt`.

Please see the documentation provided in `docs/` for further
assistance with LLVM, and in particular `docs/GettingStarted.rst` for getting
started with LLVM and `docs/README.txt` for an overview of LLVM's
documentation setup.

If you're writing a package for LLVM, see `docs/Packaging.rst` for our
suggestions.

RISC-V LLVM Support [![Build Status](https://travis-ci.com/Franderg/riscv-llvm.svg?branch=master)](https://travis-ci.com/Franderg/riscv-llvm)
--------------------------------------------------------

This repository contains a new target for LLVM RISC-V. It supports a dynamic source version of the ISA. 

The backend is structured similarly to most other LLVM backends and tries to use 
the tablegen format as much as possible. The description of the instructions
are found in `RISCVInstFormats.td`, and `RISCVInstrInfo*.td`. The registers are 
described in `RISCVRegisterInfo.td` and the calling convention is described in
`RISCVCallingConv.td`.

The instructions are defined using the LLVM IR DAG format, and simple 
instructions that use pre-existing LLVM IR operations should be very easy to
add. The instructions are divided into separate files based on their extension,
e.g. atomic operations are defined in `RISCVInstInfoA.td`. Instructions 
implemented with these patterns are simply matched against the programs LLVM IR
DAG for selection. More complicated instructions can use C++ to perform custom
lowering of the LLVM IR in `RISCVISelLowering.cpp`. Combining of multiple LLVM IR
nodes into single target instructions is also possible using C++ in
the same file. In general `RISCVISelLowering.cpp` sets up the lowering based on
the ISA and the specific subtargets features. 

Installation
------------------------------------------------------------------

The LLVM RISCV backend is built just as the normal LLVM system.

	$ git clone https://github.com/Franderg/riscv-llvm.git
	$ mkdir build
	$ cd build
	$ cmake -DCMAKE_INSTALL_PREFIX=/opt/riscv -DLLVM_TARGETS_TO_BUILD="RISCV" ../
	$ make
	$ make install

Now if `/opt/riscv` is on your path you should be able to use clang and LLVM with
RISC-V support.

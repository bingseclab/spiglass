# SPIglass: Supplementing Modern Software Defenses with Stack-Pointer Sanity

This directory contains the source code to **SPIglass**, the LLVM-based implementation of Stack Pointer Integrity.

## Installation

**SPIglass** is based on [LLVM and Clang v3.7.0](http://releases.llvm.org/download.html#3.7.0).

1. Verify that your system meets all requirements for [LLVM](http://llvm.org/releases/3.7.0/docs/GettingStarted.html#requirements), and [building LLVM with CMake](http://releases.llvm.org/3.7.0/docs/CMake.html). We have tested these instructions on Ubuntu 14.04.1 LTS with GCC 5.4.0 and CMake 3.9.1 obtained through APT.

2. Obtain **SPIglass** by either downloading an archive, or by forking this repository. Once extracted, set the `SPIHOME` shell variable to the location of the top-level directory:

    ```shell
    export SPIHOME=/path/to/SPIglass
    ```
You may find it helpful to add this command to a shell configuration or startup file.

4. Checkout the necessary tagged release of Compiler-RT:

    ```shell
    $ cd $SPIHOME/llvm-3.7.0-spi/tools
    $ svn co http://llvm.org/svn/llvm-project/compiler-rt/tags/RELEASE_370/final compiler-rt
    ```

5. Make a directory into which the **SPIglass** build will go:

    ```shell
    $ mkdir -p $SPIHOME/build
    ```

6. Run CMake:

    ```shell
    $ cd $SPIHOME/build
    $ cmake -G "Unix Makefiles" ..
    ```

7. Build **SPIglass**:

    ```shell
    make -C $SPIHOME/build clang -j
    ```

## Usage
You can now use the **SPIglass** enhanced Clang to compile executables you'd like protected:

    ```shell
    $SPIhome/build/bin/clang -spi-align=<size> -mno-red-zone [options] input...
    ```

- `size` is the frame size alignment in bytes, and must be a power of two. In the paper, we use sizes of 128, 256, 512, 1024, and 2048.
- `options` are the [set of options](https://clang.llvm.org/docs/CommandGuide/clang.html#options) available in mainstream Clang.
- `input...` are your source files.

### Using SPIglass with SPEC CPU2006

1. If you have not already done so, perform the [SPEC CPU2006 Unix installation](https://www.spec.org/cpu2006/docs/install-guide-unix.html), steps 1-6. In particular, remember to source the directory using the `cshrc` or `shrc` script.
2. Add [clang37-spiglass.cfg](./clang37-spiglass.cfg) to your CPU2006 configuration files.

    ```shell
    $ mv $SPIHOME/clang37-spiglass.cfg $SPEC/config
    ```
3. Modify the contents of  [clang37-spiglass.cfg](./SPIglass1.0/linux64-amd64-clang37-spiglass.cfg) configuration file:
    1. Lines 7-9: Set the values to reflect your hardware and operating system. This does not affect execution, but will assist you if you maintain multiple configuration files. For example:
        ```
        #      Compiler name/version:       [SPIglass Clang/Clang++ 3.7]
        #      Operating system version:    [Ubuntu 16.04.3 LTS]
        #      Hardware:                    [Dell Optiplex 9020]
        ```
    2. Lines 32-33: Set the absolute path to your **SPIglass** clang compilers:
        ```
        CC = /path/to/SPIHOME/build/bin/clang
        CXX = /path/to/SPIHOME/build/bin/clang++
        ```
    3. Lines 36-64: Complete the "HWConfig" and "SWConfig" sections. This information will appear in your results, but it does not affect execution of the benchmarks.
    4. Lines 72-73: Set the C and C++ compiler flags, including desired frame alignment size (see _Usage_). For example:
        ```
        COPTIMIZE    = -spi-align=128 -mno-red-zone -g
        CXXOPTIMIZE  = -spi-align=128 -mno-red-zone -g
        ```

4. Run one or more benchmarks (`bzip2` shown below) using the configuration file and **SPIglass**.

    ```shell
    $ runspec --config=clang37-spiglass.cfg --action=rebuild \
    --tune=base --size=ref --iterations=1 --noreportable bzip2
    ```
    
# Paper
**Supplementing Modern Software Defenses with Stack-Pointer Sanity**  
Anh Quach, Matthew Cole and Aravind Prakash.  
Annual Computer Security Applications Conference (ACSAC'17), Orlando, FL, December 2017.  
[Full Paper](https://doi.org/10.1145/3134600.3134641)


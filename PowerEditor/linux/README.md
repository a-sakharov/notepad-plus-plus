# Cross-compiling Notepad++ on Linux

This directory builds the Windows x64 executable using MinGW-w64. It reuses
`../gcc/makefile`, including its source discovery, compiler flags, static
Scintilla/Lexilla builds and portable application data.

On Ubuntu/Debian, install:

```sh
sudo apt install make python3 g++-mingw-w64-x86-64-posix \
    binutils-mingw-w64-x86-64 mingw-w64-x86-64-dev
```

Build from this directory:

```sh
make -j8
```

The portable application is in `bin.gcc.x86_64/`, with
`bin.gcc.x86_64/notepad++.exe` as its executable. Copy the entire directory to
Windows. Wine is optional for testing the executable on Linux.

Other commands:

```sh
make -j8 DEBUG=1      # bin.gcc.x86_64-debug/
make VERBOSE=1       # show application compiler commands
make clean           # remove intermediate files for the selected configuration
make fullclean       # also remove the selected application's output directory
make DEBUG=1 fullclean
```

Invoking `make -f /path/to/PowerEditor/linux/makefile` from another directory
also keeps all output in this directory. As with the upstream GCC build,
the repository path must not contain spaces.

The default compiler is `x86_64-w64-mingw32-g++-posix`: Ubuntu's GCC 13 win32
thread variant cannot compile the standard-library condition variables used by
this project. A different compatible GCC can be selected with
`make CXX=/path/to/compiler CROSS_COMPILE=/path/to/x86_64-w64-mingw32-`.
Run `make clean` before changing compilers. Only the x64 target is covered here.

Linux-specific inputs stay local: `prepare.py` replaces the Windows batch
generator for `NppLibsVersion.h`, and `compat/Windows.h` accommodates the SDK
header's capitalization on case-sensitive filesystems. No Wine, Windows SDK
installation or changes to the original source directories are needed to build.

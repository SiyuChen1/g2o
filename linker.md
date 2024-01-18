# Linker
## Dynamic Linker Searching Order
When a program is executed, the dynamic linker looks for shared libraries in the following order:
- **RPATH (Runtime Library Search Path)**: If the binary has an `RPATH` set (a list of directories embedded in the binary itself),
the dynamic linker will search these directories first. `RPATH` is set at compile/link time.
- **LD_LIBRARY_PATH**: Next, the dynamic linker checks the `LD_LIBRARY_PATH` environment variable, which is a colon-separated list of directories.
If a library is found in one of these directories, it is used before searching the default system paths. This variable is often used for testing or temporarily overriding library paths.
- **RUNPATH**: If the binary has a `RUNPATH` set (similar to `RPATH` but with lower precedence), the dynamic linker searches these directories after `LD_LIBRARY_PATH`.
- **Default System Directories**: Finally, if the library is not found in any of the above, the dynamic linker searches the default system directories (e.g., `/lib`, `/usr/lib`).
## Behavior of Modern Linker `--enable-new-dtags` by Default 
Many modern linkers, like `Ninja`, use the `--enable-new-dtags` option by default. When this option is enabled, the `-Wl,-rpath` flag sets RUNPATH instead of RPATH. 
This behavior is part of the ELF dynamic tags (`DT_RUNPATH` vs. `DT_RPATH`) and is intended to give `LD_LIBRARY_PATH` precedence over `RPATH`, which was not the case with older linkers.
## Solution
```cmake
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,--disable-new-dtags")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--disable-new-dtags")
```
## Tools to Check `RPATH` and `RUNPATH`
### Using `readelf`
```sh
readelf -d /path/to/binary | grep 'RPATH\|RUNPATH'
```
### Using `chrpath`
```sh
chrpath /path/to/binary
```
### Note
If you don't have `chrpath` installed, you can usually install it via your distribution's package manager. For example, on Ubuntu, you can install it using `sudo apt-get install chrpath`.

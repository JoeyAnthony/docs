---
tags:
  - Cmake
---


[Source](https://cmake.org/cmake/help/latest/guide/importing-exporting/index.html#id1)


# Exporting [Source](https://cmake.org/cmake/help/latest/guide/importing-exporting/index.html#id6)



## Package Configuration file (Config.cmake) [Source](https://cmake.org/cmake/help/latest/guide/importing-exporting/index.html#id8)
### set_and_check()
Don't use `set()` in this file for file locations and directories, only `set_and_check()`. It runs on the users PC and needs to check if the file/directory is available.

### Linking with dependencies
Dependencies need to be linked in side this file to make sure they're available and so CMake knows it comes from the library.
So if the project links to a library with `PUBLIC` it should have the `find_dependency` command for it in the package configuration file. 

```cmake
include(CMakeFindDependencyMacro)
find_dependency(Stats 2.6.4)
```

# Importing pre-built libraries [Source](https://gitlab.kitware.com/cmake/community/-/wikis/doc/tutorials/Exporting-and-Importing-Targets)

in the rule to link myexe. On Windows a .dll and its .lib import
library may be imported together:

``` cmake
add_library(bar SHARED IMPORTED)
set_property(TARGET bar PROPERTY IMPORTED_LOCATION c:/path/to/bar.dll)
set_property(TARGET bar PROPERTY IMPORTED_IMPLIB c:/path/to/bar.lib)
add_executable(myexe src1.c src2.c)
target_link_libraries(myexe bar)
```

in the rule to link myexe. A library with multiple configurations may
be imported with a single target:
``` cmake
add_library(foo STATIC IMPORTED)
set_property(TARGET foo PROPERTY IMPORTED_LOCATION_RELEASE c:/path/to/foo.lib)
set_property(TARGET foo PROPERTY IMPORTED_LOCATION_DEBUG   c:/path/to/foo_d.lib)
add_executable(myexe src1.c src2.c)
target_link_libraries(myexe foo)
```

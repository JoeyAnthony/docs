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

## Export for macOS and iOS
### Setup the toolchain
Source for setting up the toolchain [CMake source](https://cmake.org/cmake/help/latest/manual/cmake-toolchains.7.html#cross-compiling-for-ios-tvos-or-watchos)

### Export the framework
For Apple export, you need to export a framework as described [here](https://cmake.org/cmake/help/latest/prop_tgt/FRAMEWORK.html)

Eample from the source:
```cmake
add_library(dynamicFramework SHARED
            dynamicFramework.c
            dynamicFramework.h
)
set_target_properties(dynamicFramework PROPERTIES
  FRAMEWORK TRUE
  FRAMEWORK_VERSION C
  MACOSX_FRAMEWORK_IDENTIFIER com.cmake.dynamicFramework
  MACOSX_FRAMEWORK_INFO_PLIST Info.plist
  # "current version" in semantic format in Mach-O binary file
  VERSION 16.4.0
  # "compatibility version" in semantic format in Mach-O binary file
  SOVERSION 1.0.0
  PUBLIC_HEADER dynamicFramework.h
  XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "iPhone Developer"
)
```

To export the framework itself, the `FRAMEWORK DESTINATION` needs to be added to the export target.

```cmake
install(TARGETS dynamicFramework
        EXPORT dynamicFrameworkTargets
        FRAMEWORK DESTINATION "some_destination_for_frameworks"
)
```

## Cocoapods podspec documentation
[Cocoapods documentation](https://guides.cocoapods.org/syntax/podspec.html#group_build_settings)
[Podspec examples](https://github.com/tom-xy/PodspecExample)

## Cocoapods linking with dynamic frameworks
```podspec
  # telling CocoaPods not to remove framework
  s.preserve_paths = [
    'ReceiverLibrary.framework',
    'TransmitterLibrary.framework',

    'opencv2.framework',
    # comment reference to Library folder to use lightdrop-core submodule
    'Library',
  ]

  # telling linker to include opencv2 framework and configure the headers search paths
  s.xcconfig = {
    'HEADER_SEARCH_PATHS' => [
        '${PODS_TARGET_SRCROOT}/ReceiverLibrary.framework/Headers',
        '${PODS_TARGET_SRCROOT}/TransmitterLibrary.framework/Headers',
    ],
    'FRAMEWORK_SEARCH_PATHS' => [
        '/Library/Frameworks',
    ],
    'OTHER_LDFLAGS' => '-framework ReceiverLibrary -framework TransmitterLibrary -framework opencv2',
    'CLANG_CXX_LANGUAGE_STANDARD' => 'c++20',
  }

  # including OpenCV framework
  s.vendored_frameworks = [
    'ReceiverLibrary.framework',
    'TransmitterLibrary.framework',

    'opencv2.framework',
    'opencv2.xcframework',
  ]
```

### MACOSX Package location
[cmake source](https://cmake.org/cmake/help/latest/prop_sf/MACOSX_PACKAGE_LOCATION.html)



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

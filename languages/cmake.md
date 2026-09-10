# CMake

## Version floor

`cmake_minimum_required(VERSION 3.16)`: this is the floor across every
current project; do not drop below it without a specific reason tied to a
target platform's available CMake version.

## Modern, target-based style

Prefer target-based commands over the older directory-scoped ones:

```cmake
add_library(mypkg_core STATIC src/core.cpp)
target_include_directories(mypkg_core PUBLIC include)
target_link_libraries(mypkg_core PRIVATE some_dependency)
target_compile_features(mypkg_core PUBLIC cxx_std_20)
```

Avoid `include_directories()`, `link_libraries()`, and other directory-wide
commands: they leak scope to every target in the directory, including
ones that do not need the dependency.

## Optional dependencies fail gracefully

When a dependency is optional (a GUI toolkit, a simulator integration),
`find_package` should degrade to a warning and a disabled feature flag
rather than a hard configure failure:

```cmake
find_package(Qt6 QUIET)
option(BUILD_GUI "Build the GUI" ${Qt6_FOUND})
if(BUILD_GUI AND NOT Qt6_FOUND)
    message(WARNING "Qt6 not found: disabling BUILD_GUI")
    set(BUILD_GUI OFF)
endif()
```

This keeps a checkout buildable on a machine that only has a subset of the
optional toolchains installed.

## ROS 2 / ament_cmake projects

For `ament_cmake` packages: standard `ament_python_install_package`/
`install()` layout for launch files, config, and resource directories;
custom commands for per-model asset generation (e.g. XACRO → URDF) live in
the owning package's `CMakeLists.txt`, not in a shared top-level script.

## Formatting

CMake files have no enforced formatter across projects.
If a project adopts one (e.g. `gersemi`, `cmake-format`), document the
choice in that project's own guideline file rather than assuming a shared
default.

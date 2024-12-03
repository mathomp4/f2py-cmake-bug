## f2py-cmake-bug

This repo is trying to demonstrate a possible issue in [`f2py-cmake`](https://github.com/scikit-build/f2py-cmake).

The issue seems to occur when multiple calls to `include(UseF2Py)` occur.

To test this if you run with `-DFIXED_FILE:BOOL=OFF`, the build will fail at the
generation step with:

```
❯ cmake -B build --install-prefix=$(pwd)/install --fresh -DFIXED_FILE:BOOL=OFF && cmake --build build && cmake --install build
-- The Fortran compiler identification is GNU 14.2.0
-- The C compiler identification is AppleClang 16.0.0.16000026
...
-- Building subrepo f2py module
-- Done building subrepo f2py module
-- Building test f2py module
-- Done building test f2py module
-- Configuring done (4.7s)
CMake Error at cmake/UseF2Py.cmake:43 (target_link_libraries):
  Target "test_object" links to:

    ::NumPy

  but the target was not found.  Possible reasons include:

    * There is a typo in the target name.
    * A find_package call is missing for an IMPORTED target.
    * An ALIAS target is missing.

Call Stack (most recent call first):
  src/CMakeLists.txt:18 (f2py_object_library)


-- Generating done (0.0s)
CMake Generate step failed.  Build files cannot be regenerated correctly.
```

If you run with `-DFIXED_FILE:BOOL=ON`, the build will succeed.

```
❯ cmake -B build --install-prefix=$(pwd)/install --fresh -DFIXED_FILE:BOOL=ON && cmake --build build && cmake --install build
❯ cmake -B build --install-prefix=$(pwd)/install --fresh -DFIXED_FILE:BOOL=ON && cmake --build build && cmake --install build
-- The Fortran compiler identification is GNU 14.2.0
-- The C compiler identification is AppleClang 16.0.0.16000026
...
-- Building subrepo f2py module
-- Done building subrepo f2py module
-- Building test f2py module
-- Done building test f2py module
-- Configuring done (5.0s)
-- Generating done (0.0s)
-- Build files have been written to: /Users/mathomp4/Bug-f2py-cmake/build
[ 10%] Building C object src/CMakeFiles/test_object.dir/Users/mathomp4/installed/Core/GEOSpyD/24.9.2-0/2024-11-27/envs/py3.12/lib/python3.12/site-packages/numpy/f2py/src/fortranobject.c.o
...
[100%] Linking Fortran shared module subrepo_.cpython-312-darwin.so
[100%] Built target subrepo_
-- Install configuration: ""
-- Installing: /Users/mathomp4/Bug-f2py-cmake/install/lib/Python/subrepo_.cpython-312-darwin.so
-- Installing: /Users/mathomp4/Bug-f2py-cmake/install/lib/Python/test_.cpython-312-darwin.so
```

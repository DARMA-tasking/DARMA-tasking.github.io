# Linking Your App Against DARMA

Many DARMA backends provide a CMake package that can be used to tie an app’s build system into DARMA.  To import a DARMA backend package into your CMake build system, include the following code in your CMakeLists.txt file:

```
if (DARMA_BACKEND_PKG)
  find_package (${DARMA_BACKEND_PKG})
  if (NOT ${DARMA_BACKEND_PKG}_FOUND)
    message (FATAL_ERROR
      "Error: DARMA backend package could not be found; use "
      "${DARMA_BACKEND_PKG}_DIR= to specify path to "
      "${DARMA_BACKEND_PKG}Config.cmake file")
  endif ()
  set (CMAKE_CXX_COMPILER ${DARMA_BACKEND_CXX_COMPILER})
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${DARMA_BACKEND_CXX_FLAGS}")
  include_directories( ${DARMA_BACKEND_INCLUDE_DIRS} )
  link_directories( ${DARMA_BACKEND_LIBRARY_DIRS} )
else()  # not using cmake package
  message(FATAL_ERROR
    "Specify the DARMA backend package using -DDARMA_BACKEND_PKG=")
endif()
```

Then, for each target you want linked against DARMA, all you need to say is:

```
target_link_libraries(your_app ${DARMA_BACKEND_LIBRARIES})
```

When you configure your build, add the following two options to your cmake line:

```
-DDARMA_BACKEND_PKG=${backend_name}
-D${backend_name}_DIR=/path/to/darma/install/cmake
```

where `backend_name` is the name of the DARMA backend you want to use.  Note that the `cmake` subdirectory on the end of the path is critical.

For the threads backend, for example, this would look like:

```
-DDARMA_BACKEND_PKG=DarmaThreadsBackend
-DDarmaThreadsBackend_DIR=/path/to/darma/install/cmake
```

For the Charm++ backend, this would be:

```
-DDARMA_BACKEND_PKG=DarmaCharmBackend
-DDarmaCharmBackend_DIR=/path/to/darma/install/cmake
```

By using this approach, your app will be built with the same compilers, flags, etc. that were used in the DARMA build.  Additional convenience variables may also be set (e.g., `DARMA_BACKEND_RUN_SCRIPT`).  As mentioned previously, you will need to manually add `DARMA_BACKEND_LIBRARIES` to the `target_link_libraries()` command for each executable you want to link against DARMA.

Because the Charm++ backend changes the way your build system compiles and links executables, it will not work with CMake 3.3.2 or earlier.  We have had success with CMake 3.4.3 or greater.  If you are linking against the threads backend, you may only need CMake 2.8 (we have tested 2.8.12 successfully).


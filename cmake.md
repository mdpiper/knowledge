# CMake

The [CMake home page](http://www.cmake.org/).

## Documentation

* CMake [documentation](http://www.cmake.org/cmake/help/v3.0/index.html), including [commands](http://www.cmake.org/cmake/help/v3.0/manual/cmake-commands.7.html) and [variables](http://www.cmake.org/cmake/help/v3.0/manual/cmake-variables.7.html) [cmake.org]
* CTest [documentation](http://www.cmake.org/Wiki/CMake/Testing_With_CTest) [cmake.org]

I have examples of using CMake in:

* https://github.com/csdms/bmi-fortran
* https://github.com/csdms/bmi-example-fortran
* https://github.com/csdms-contrib/fastmech

## Use pattern

### Linux and macOS

From the source directory:

    mkdir _build && cd _build
    cmake -DCMAKE_INSTALL_PREFIX=</path/to/install> ..
    make
    make install

I often build in a conda environment,
in which case the install prefix is `$CONDA_PREFIX`.

To run CTest, add:

    make test

or

    ctest

To run only the second of a set of tests and show all output from the test:

    ctest -I2,2 --verbose

### Windows

To access the VS linker,
`cmake` must be run
in a [Developer Command Prompt](https://docs.microsoft.com/en-us/dotnet/framework/tools/developer-command-prompt-for-vs)
(an Anaconda Prompt works, but not a Git Bash shell).
Before calling `cmake`,
set up links to VS with
```
cd C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build
vcvars64.bat	
```

A typical configure-build-install sequence is:
```
mkdir build && cd build
cmake .. ^
  -G "NMake Makefiles" ^
  -DCMAKE_INSTALL_PREFIX=%CONDA_PREFIX% ^
  -DCMAKE_BUILD_TYPE=Release
cmake --build . --target install --config Release
```
The install path must be absolute, not relative.

See also a conda-forge feedstock [bld.bat](https://github.com/conda-forge/ecsimplesnow-feedstock/blob/master/recipe/bld.bat), for example.

## Setting rpaths for install

This happened to me with the
[bmi-fortran](https://github.com/csdms/bmi-fortran) project.

My typical pattern (shown above) is to install into **../_install**.
In **bmi-fortran**,
I built two shared objects,
with one dependent on the other.
Things were fine when I built,
but when I installed,
the dependendent library couldn't find the independent library.

The solution is to set the [rpath](https://en.wikipedia.org/wiki/Rpath)
for this nonstandard library location.
I added

    set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")

to my top-level **CMakeLists.txt** file.

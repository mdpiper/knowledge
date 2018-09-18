# CMake

The [CMake home page](http://www.cmake.org/).

## Documentation

* CMake [documentation](http://www.cmake.org/cmake/help/v3.0/index.html), including [commands](http://www.cmake.org/cmake/help/v3.0/manual/cmake-commands.7.html) and [variables](http://www.cmake.org/cmake/help/v3.0/manual/cmake-variables.7.html) [cmake.org]
* CTest [documentation](http://www.cmake.org/Wiki/CMake/Testing_With_CTest) [cmake.org]

I have examples of using CMake in:

* https://github.com/csdms-contrib/storm
* https://github.com/csdms/bmi-fortran
* https://github.com/mdpiper/fastmech

## Use pattern

From the source directory:

	$ mkdir _build && cd _build
	$ cmake .. -DCMAKE_INSTALL_PREFIX=../_install
	$ make
	# make install

To run CTest, add:

	$ make test

or

    $ ctest

To run only the second of a set of tests and show all output from the test:

    $ ctest -I 2 --verbose

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

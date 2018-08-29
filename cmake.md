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

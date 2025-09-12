# gcc and gfortran

Notes on using `gcc` and `gfortran`.
There are related notes in [linux.md](./linux.md).

## Compiling against a shared library in a nonstandard location

This happened with my
[fortran-python](https://github.com/mdpiper/fortran-python) project.

I installed the [bmi-fortran](https://github.com/csdms/bmi-fortran) project
to a local directory outside the standard library path:

    LOCAL_LIB=../_install/lib

This directory holds two shared objects,
**libbmif.so** and **libbmiheatf.so**.
My example, **heat_ex.f90**,
needs to be dynamically linked to both libraries.
Here's the `gfortran` call that does it:

    gfortran heat_ex.f90 \
	    -L${LOCAL_LIB} \
        -I${LOCAL_LIB} \
        -Wl,-rpath=${LOCAL_LIB} \
        -lbmif -lbmiheatf \
        -o heat_ex

Point to the local library directory with the `-L` flag.
Point to Fortran `.mod` files with the `-I` flag.
Set the [rpath](https://en.wikipedia.org/wiki/Rpath)
to this nonstandard library location with `-Wl,rpath`.
With the `-l` flag,
explicitly link against the *bmif* and *bmiheatf* shared libraries
in this location.
Name the resulting executable "heat_ex".

## Setting the C dialect

I've had to look this up a few times.

* https://gcc.gnu.org/onlinedocs/gcc/C-Dialect-Options.html

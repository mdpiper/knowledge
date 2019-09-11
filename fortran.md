# Fortran

These notes are mainly for Fortran 90/95,
and for Fortran 2003 and 2008 where specified.
Fortran 77 is ingrained.


## References

* [Compact Fortran 95 Language Summary](https://www.csee.umbc.edu/~squire/fortranclass/summary.shtml) [umbc.edu].
* [The GNU Fortran Compiler](https://gcc.gnu.org/onlinedocs/gfortran/) [gnu.org]. Includes a [list](https://gcc.gnu.org/onlinedocs/gfortran/Intrinsic-Procedures.html) of all the intrinsic functions.
* [Fortran Best Practices](http://www.fortran90.org/src/best-practices.html) [fortran90.org]. This is good. Despite the domain name, it's not an official web site.
* http://www.cs.rpi.edu/~szymansk/OOF90/F90_Objects.html
* https://github.com/Unidata/netcdf-fortran. See how the Unidata engineers build netCDF-Fortran. Or maybe not -- they use includes more than modules.


## Parlance

* Data hiding is done through *private* (and *public*) statements/attributes.
* Encapsulate with a *type*.
* Function overloading is accomplished through a *generic interface*.
* Import from modules with *use*. Explicitly import with *use*, *only*.
 * On import, rename an item with `=>`.
* The pointer assignment operator is also `=>`. For example, `p => target`.
 * The pointer variable must have the `pointer` attribute.
 * The target variable must have the `target` attribute.
* The equivalent of a C header file is a module.


## Variables

* The maximum length of a variable name is 31 characters.
* Use lower case for variable names, connected with underscores.


## Interfaces

An *interface* in F90 is defined (for example) in a main program
to provide an explicit interface to a subprogram,
thereby allowing keyword and optional arguments.
If the subprogram is located in a module,
then an interface is not needed.
It's preferrable to use modules, when possible.
An interface would be used, for example,
to wrap a F77 subroutine,
rather than rewriting the routine to use an F90 module.

So, a F90 interface isn't like what I think of
an an interface, like in Java, for example.


## Procedure pointers and type-bound procedures

Procedure pointers and type-bound procedures
are defined in Fortran 2003.
See ftp://ftp.nag.co.uk/sc22wg5/n1551-n1600/n1579.pdf,
and to a lesser extent,
http://fortranwiki.org/fortran/show/Object-oriented+programming.


## Change rank of array while preserving reference

This is used, for example, in BMI.

F03 is needed for rank 1 to 2;
see http://stackoverflow.com/a/5406261.
F08 is needed for rank 2 to 1,
along with the `contiguous` attribute;
see http://stackoverflow.com/a/5406584.

I have an example of this in my wunderkammer repo.
Needs gfortran 4.6.

Alternately, use the
[iso_c_binding](https://gcc.gnu.org/onlinedocs/gfortran/ISO_005fC_005fBINDING.html)
module introduced in F03.


## Classes in F03

Here are a set of links that helped me understand how to write classes
in F03.

* http://fortranwiki.org/fortran/show/Factory+Pattern
* http://fortranwiki.org/fortran/show/Decorator+Pattern
* http://stackoverflow.com/a/25690402/1563298

### Constructors: default and user-defined

F03 creates a default constructor.
For example, for a user-defined type `Simulation`,
I can create an instance with

    s = Simulation()

You can override the default constructor by defining
an interface for the type.
For example, for the `Simulation` type:
```
interface Simulation
   module function constructor_Simulation(ctl_data) result(this)
     type(Simulation) :: this
     type(Control), intent(in) :: ctl_data
   end function
end interface Simulation
```

See https://www.ibm.com/developerworks/community/blogs/b10932b4-0edd-4e61-89f2-6e478ccba9aa/entry/object_oriented_fortran_user_defined_constructors2?lang=en for more.

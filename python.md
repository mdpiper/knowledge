# Python

See also notes on [Matplotlib](./python-matplotlib.md),
[packaging](./python-packaging.md),
and [conda](./conda.md).


Style
------------------------------------------------------------------------------

The Python style guide is called the PEP-8, authored by Guido:

[http://www.python.org/dev/peps/pep-0008/](http://www.python.org/dev/peps/pep-0008/)

Also:
```python
>>> import this
```

Check for style errors with `flake8`:

    $ flake8 foo.py


Program types
------------------------------------------------------------------------------

Python has only functions, no procedures/subroutines. If no return is 
specified, Python returns `None`, the null value.

**Definition syntax:**
```python
def foo():
  print('bar!')
```

**Calling syntax:**
```python
>>> foo()
bar!
```

or
```python
>>> a = foo()
bar!
>>> print a
None
```


Case sensitivity
------------------------------------------------------------------------------

Python is case-sensitive. 
```python
>>> a = 5
>>> A = 6
>>> A != a
True
```


Path
------------------------------------------------------------------------------

`$PYTHONPATH` sets path.
```python
>>> import sys
>>> sys.path  # prints path
>>> sys.path.append('/path/to/stuff')  # local to session
>>> sys.version
```

Better to use packages, though.


Importing modules
------------------------------------------------------------------------------

The function `hello_world`

```python
def hello_world():
  """Python hello world program."""
  return "Hello World!"
```

is in the file **hello_world.py**.

Import the module with:
```python
>>> import hello_world as h
```

then call it with:
```python
>>> h.hello_world()
'Hello World!'
```

or
```python
>>> i = h.hello_world()
>>> i()
'Hello World!'
```

alternately,
```python
>>> from hello_world import hello_world
>>> hello_world()
'Hello World!'
```

It's strongly encouraged to never import using `*`:
```python
>>> from <module> import *
```

Examples from my library (**minmax.py**):
```python
>>> from minmax import *
>>> a = range(5)
>>> minmax(a)
[0, 4]

>>> from minmax import minmax
>>> a = range(5)
>>> b = minmax(a)
>>> b
[0, 4]

>>> import minmax as mm
>>> mm.minmax(a)
[0, 4]
```


Module test suite
------------------------------------------------------------------------------

Appending a main program at the bottom of an IDL routine is equivalent to 
the Python construct

```python
if __name__ == '__main__':
  print hello_world()
```

This is called the *module test suite*.
It's a unit test.
I use it as an example program in IDL.
Call this main program from the shell prompt with:

	$ python hello_world.py
	Hello World!

or from within IPython with:

	In [1]: run hello_world.py
	Hello World!


Arguments
------------------------------------------------------------------------------

Arguments in Python behave, by default, like positional parameters in IDL,
but can also be called like IDL keyword parameters.

Keyword parameters in Python supply default values to a function. They
can be overriden in the call to the function. Their order doesn't
matter.

For an example, see **examples/ClassWithKeyword.py**.


The dir() function
------------------------------------------------------------------------------

The `dir` function lists everything about something.
```python
>>> import hello_world as h 
>>> dir(h)
['__builtins__', '__doc__', '__file__', '__name__', 'hello_world']
```


The callable() function
------------------------------------------------------------------------------

Is an object callable?
```python
>>> import hello_world as h 
>>> callable(h)
False
>>> callable(h.hello_world)
True
```


Docstrings
------------------------------------------------------------------------------

Get a docstring:
```python
>>> print pow.__doc__
```

Alternately, use the `help` function:
```python
>>> help(pow)
```


The os module
------------------------------------------------------------------------------

The `os` module provides the equivalent of many (but not all) bash commands:
```python
>>> os.path.exists('/Users/Mark/README')
True

>>> os.path.sep
'/'

>>> os.getcwd()
'/Users/Mark/code/python'

>>> os.listdir(os.getcwd))
```


Lists
------------------------------------------------------------------------------

Note that `x` is a list, not an array (which needs Numpy):
```python
>>> x = range(5)
>>> x
>>> [0, 1, 2, 3, 4]

>>> dir(x);  # remove ";" to see output, like MATLAB

>>> x.__class__
>>> list

>>> x.append(55)
>>> x
>>> [0, 1, 2, 3, 4, 55]
```

Note the difference between `range` (list) and `arange` (array):
```python
In [58]: x = range(5)
In [59]: y = np.arange(0,5)
In [60]: x
Out[60]: [0, 1, 2, 3, 4]
In [61]: y
Out[61]: array([0, 1, 2, 3, 4])
In [62]: x*2
Out[62]: [0, 1, 2, 3, 4, 0, 1, 2, 3, 4]
In [63]: y*2
Out[63]: array([0, 2, 4, 6, 8])
```

And `xrange` is optimized to be faster than `range`:
```python
In [10]: %timeit list1 = range(1000000)
100 loops, best of 3: 15 ms per loop

In [11]: %timeit list2 = xrange(1000000)
10000000 loops, best of 3: 151 ns per loop
```

Get the length of a list:
```python
>>> len(x)
5
```

Remove an element from a list:
```python
>>> x.pop(0)
>>> x
[1, 2, 3, 4]
```

Append versus extend:
```python
In [24]: x1 = range(5)
In [25]: x2 = range(5)
In [26]: y = range(5,10)
In [27]: x1.append(y)
In [28]: x1
Out[28]: [0, 1, 2, 3, 4, [5, 6, 7, 8, 9]]
In [29]: x2.extend(y)
In [30]: x2
Out[30]: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Lists are copied by reference:
```python
>>> b = [1,2,3]
>>> c = b
>>> c[1] = 56
>>> b
[1, 56, 3]
```

Yikes! Subscript to copy the values of one list to another:
```python
>>> c = b[:]
```

### List comprehesion:

It's like an implied loop in Fortran.
```python
>>> x = range(5)
>>> y = [i**2 for i in x]
>>> x, y
([0, 1, 2, 3, 4], [0, 1, 4, 9, 16])
```


### List filtering

List filtering is equivalent to running WHERE output through conditioned
array.


Tuples
------------------------------------------------------------------------------

Lists use `[]`, tuples use `()`.
```python
>>> a = [1,2,3]
>>> b = (1,2,3)
>>> type(a)
list
>>> type(b)
tuple
```

Tuples are immutable.
```python
>>> a = (1,2,3)
>>> a
(1, 2, 3)
>>> a[1] = 56
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```


Lambda functions
------------------------------------------------------------------------------

Lambda functions:
```python
>>> g = lambda x: x**2
```

is equivalent to
```python
def g(x):
  return x**2
```

Use it:
```python
>>> g(5)
25
```


Typing
------------------------------------------------------------------------------

Python is dynamically and strongly (needs explicit type conversion) typed.
IDL is dynamically and weakly typed.


String formatting
------------------------------------------------------------------------------

String formatting in Python is the same as `sprintf` in C. The `+`
operator does concatenation, while `str()` does type coercion.

And it can be crazy. Check this out:
```python
>>> first_name = "Mark"
>>> last_name = "Piper"
>>> title = "Grand Poobah"
>>> s = "Hi, my name is {0} {1}, {2}.".format(first_name, last_name, title)
>>> s
'Hi, my name is Mark Piper, Grand Poobah.'
```


Garbage collection
------------------------------------------------------------------------------

Python uses reference counting on objects, like IDL 8.0.


Array operations
------------------------------------------------------------------------------

Need numpy or scipy to do array operations, eh?
```python
>>> i = range(10)
>>> j = i**2 # raises exception
```

Arrays are not intrinsic in Python.


Slicing
------------------------------------------------------------------------------

Subscripting ("slicing") arrays is whack. Done on interval [start,end).
```python
In [34]: x = range(10)
In [35]: x[0:4]
Out[35]: [0, 1, 2, 3]
```

Negative indexing works like IDL, with -1 the last element in an
array.


Numpy doesn't copy
------------------------------------------------------------------------------

Like lists, arrays aren't copied.
(Assignment creates a pointer to the original array.)
```python
>>> x = np.arange(0,5)
>>> y = x
>>> y[-1] = 42.0
>>> print x
[ 0  1  2  3 42]
```


List to an array
------------------------------------------------------------------------------

Convert a list to an array:
```python
>>> x = range(5)
>>> y = np.asarray(x)
>>> x.__class__
list
>>> y.__class__
numpy.ndarray
```


Integers
------------------------------------------------------------------------------

Python supports integer division.


The subprocess module
------------------------------------------------------------------------------

Replaces `os.system` for calling shell commands.

	In [33]: b = ["foo", "bar", "baz"]
	In [34]: for i in b:
		subprocess.call(["echo", i])
		....:
	foo
	bar
	baz

Alternately, using a single string:

	In [36]: for i in b:
		subprocess.call("echo " + i, shell=True)
		....:
	foo
	bar
	baz

See the [docs](https://docs.python.org/2/library/subprocess.html#replacing-os-system).


Magic functions
------------------------------------------------------------------------------

IPython has "magic" functions prefaced with the "%" character; e.g.,

	%whos
	%cd
	%pwd

Can also use shell commands; e.g.,

	ls
	mkdir

though some may need to be escaped:

	!date


History
------------------------------------------------------------------------------

Use `Ctrl-P` to search through history to match a command. 

`Ctrl-N` does a reverse search.

Works in Python and IPython.


Movies!
------------------------------------------------------------------------------

Make a movie from a bunch of PNG files with:

	>>> os.system('avconv -r 2.5 -i gph.%03d.png -vcodec libx264 gph.mp4')

However, is there a Pythonic solution?

Yes, though not with my version of matplotlib (1.1.1rc). 
See: http://stackoverflow.com/questions/4092927/generating-movie-from-python-without-saving-individual-frames-to-files


Pretty print
------------------------------------------------------------------------------

For a nicely formatted print, use the `pprint` module.



OOP
------------------------------------------------------------------------------

Python uses class and instance variables.


Mixins
------------------------------------------------------------------------------

Avoid problems with multiple inheritance by using the *mixin* design pattern.
Mixins are classes that define and implement a single, well-defined, feature.
See discussion and examples in the links below.

*References:*

* https://www.residentmar.io/2019/07/07/python-mixins.html
* https://stackoverflow.com/a/547714


Web server
------------------------------------------------------------------------------

Python includes a simple HTTP server that allows any directory
to be the document root.
Start it with:

    $ python -m http.server 8080

The default port is 8000.
In a web browser, open
http://localhost:8080 or
http://[::]:8080.
If no **index.html** file exists,
all files in the directory will be listed.

Here's the canonical test **index.html**:

    <html><body><h1>It works!</h1></body></html>


The walrus operator
------------------------------------------------------------------------------

The walrus operator `:=` was introduced in Python 3.8 (see [PEP 572](https://peps.python.org/pep-0572/)).
It's used for assignment within an expression, like conditions and loops.
For example:
```python
# Handle a matched regex
if (match := pattern.search(data)) is not None:
    # Do something with match

# A loop that can't be trivially rewritten using 2-arg iter()
while chunk := file.read(8192):
   process(chunk)
```

*Reference:*

* https://stackoverflow.com/a/26000366

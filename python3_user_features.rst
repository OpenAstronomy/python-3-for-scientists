Useful Python 3 features for users
==================================

Unicode strings
---------------

In Python 2, only the basic ASCII character set was available in standard
strings; to use the much more extensive Unicode set of characters, you had to
prefix each string with a u::

    >>> s1 = "an ascii string"
    >>> s2 = u"The total is €10"

Unicode strings are the default in Python 3. This makes it more straightforward
to e.g., include foreign languages, and print greek symbols (or emoji) in
strings::

    >>> s3 = "Πύθων"
    >>> s4 = "unicode strings are great! 😍"

Division
--------

In Python 2, integer division is the default, so 1/2 evaluates to 0. This means
frequently having to explicitly convert integers to floats when working with
integer variables::

  >>> int_one = 1
  >>> int_two = 2
  >>> int_one / int_two
  0
  >>> float(int_one) / int_two
  0.5

or being careful to do things like ``/ 2.`` or ``* 0.5``. In Python 3, the
default division will yield a float, and integer division is accessed using the //
operator::

  >>> int_one / int_two
  0.5
  >>> int_one // int_two
  0

This makes it safer to use by default, since there there is no longer any
implicit conversion to integers.

Unicode variable names
----------------------

As with strings, Python 3 allows most unicode symbols to be used in variable
names. In contrast, Python 2 could only use the basic ASCII character set for
variable names. This means you can use foreign language words and letter-like
symbols as variable names, e.g.::

  >>> π = 3.14159
  >>> jalapeño = "a hot pepper"

(Sadly, no emoji here!)

Use caution if you're planning to share your code, though, as it's fairly easy
to produce illegible code this way.

Recursive glob
--------------

A small but very useful feature in Python 3 is the addition of a recursive
option in the built-in :func:`~glob.glob` function. In Python 2 and 3, this
function can be used to find all files and directories matching a certain
pattern::

    >>> import os
    >>> import glob
    >>> glob.glob(os.path.join('data', '*.fits'))
    ['data/image.fits']

Now let's say that the ``data`` directory now contains FITS files both
directly in ``data`` and in sub-directories of ``data``. In Python 3, you can
now do::

    >>> import os
    >>> import glob
    >>> glob.glob(os.path.join('data', '**', '*.fits'), recursive=True)
    ['data/image1.fits', 'data/subset1/a.fits', 'data/subset1/b.fits',
     'data/subset1/c.fits', 'data/subset2/d.fits', 'data/subset2/e.fits']

The ``**`` is used to indicate the point in the path at which to look for
recursive directories, and the ``recursive=True`` option is needed to
correctly interpret the ``**``.

.. note:: We use ``os.path.join`` instead of writing out the path
          by hand (e.g. ``data/*.fits``) to make sure that this works on
          Windows as well as Linux and MacOS X.

Matrix multiplication operator
------------------------------

Since Python 3.5, and Numpy 1.10, it is now possible to use the ``@`` operator
to do matrix multiplication (vector product)::

    >>> x = np.array([[1, 2], [3, 4]])
    >>> y = np.array([[3, 2], [2, -1]])
    >>> x @ y
    array([[ 7,  0],
           [17,  2]])

Note that this is different from ``x * y``, which returns an element-wise
multiplication of the arrays:

    >>> x * y
    array([[ 3,  4],
           [ 6, -4]])

Advanced print function
-----------------------

One of the widely known changes between Python 2 and Python 3 is the change
from a ``print`` statement to a ``print`` function. This change is not just
esthetic, it now allows you to better customize aspects such as what separator
to use between variables, and whether to go to the next line between successive
print statements.

By default, ``print()`` behaves like the Python 2 print statement in that it
separates variables by spaces and goes to the next line at the end of a print
call:

    >>> a, b = 1, 2
    >>> print(a, b)
    1 2

The ``sep`` argument can be used to customize the separator:

    >>> print(a, b, sep=', ')
    1, 2

And similarly, the ``end`` argument can be used to customize the end of the line -
this defaults to ``\n``, which is a carriage return (or *newline*):

    >>> print("hello"); print("world")
    hello
    world
    >>> print("hello", end=' '); print("world")
    hello world

In the above example, we had to put the print statements on the same line,
because in interactive Python, you will be returned to the Python prompt after
the line is executed. However, in scripts, you can do::

    print("hello ", end=' ')
    print("world")

Finally, a last useful feature is that it is possible to send the output of the
print calls to file-like objects instead of the main terminal output (the
*standard output*):

    >>> f = open('data.txt', 'w')
    >>> print(a, b, file=f)
    >>> f.close()

or better, if you are familiar with the context manager notation:

    >>> with open('data.txt', 'w') as f:
    ...     print(a, b, file=f)

Advanced unpacking
------------------

In Python 2, you can use implicit unpacking of variables to go from a list,
tuple, or more generally any *iterable* to separate variables:

    >>> a, b, c = range(3)
    >>> a
    0
    >>> b
    1
    >>> c
    2

The number of items in the iterable on the right has to match exactly the number
of variables on the left. However, there are cases where one might only be
interested in the first few items of the iterable. For example, if you have a
list of 5 items::

    >>> values = range(10)

and are only interested in the first two, in Python 2 you would need to do
either:

    >>> a, b, _, _, _ = values

or

    >>> a = values[0]
    >>> b = values[1]

Python 3 now allows users to use the ``*variable`` syntax (similar to ``*args``
in function arguments) to avoid having to write out as many variables than items
in the iterable::

    >>> a, b, *rest = values
    >>> a
    0
    >>> b
    1
    >>> c
    [2, 3, 4]

The ``*`` syntax can also be used for e.g. the first variable and variables in the middle::

    >>> a, *rest, b = range(5)
    >>> a, b
    (0, 4)
    >>> *rest, a, b = range(5)
    >>> a, b
    (3, 4)

This can be used for example to access the first two lines and the last line
in a file:

    >>> f = open('data.txt')
    >>> first, second, *rest, last = f.readlines()
    >>> f.close()

Function annotations
--------------------

Since Python 3.5, it is possible to use the following syntax to annotate
functions, to provide information on inputs/outputs. For example, it is possible
to specify *type* annotations:

    >>> def remove_spaces(x: str) -> str:
    ...     return x.replace(' ', '')

This syntax means that the input as well as the output should be a string. Now
it turns out that Python doesn't do anything with these type annotations (there
are still reasons why developers might want to do this, but this is not
necessarily critical for the typical user).

However, some packages have now implemented their own annotations. For example,
the `Astropy <http://www.astropy.org>`_ package uses these to allow users to
specify what units different variables should be in:

    >>> import astropy.units as u
    >>> @u.quantity_input
    ... def kinetic_energy(mass: u.kg, velocity: u.m / u.s):
    ...    return 0.5 * mass * velocity ** 2

This does then raise an error if the variables do not have units attached:

    >>> kinetic_energy(1, 3)
    ...
    TypeError: Argument 'mass' to function 'kinetic_energy' has no 'unit'
    attribute. You may want to pass in an Astropy Quantity instead.

or if the units are not compatible/convertible:

    >>> kinetic_energy(1 * u.s, 3 * u.km / u.s)
    ...
    UnitsError: Argument 'mass' to function 'kinetic_energy' must be in
    units convertible to 'kg'.

Other packages will hopefully also provide useful annotations such as these!

Sensible comparison
-------------------

In Python 2, it was possible to compare things that shouldn't really be
comparable:

    >>> '1' > 2
    True

Whether a string was greater than an integer or a float was not necessarily
predictable or intuitive. In Python 3, this type of comparison is no longer
allowed:

    >>> '1' > 2
    ...
    TypeError: unorderable types: str() > int()

This should avoid quite a few bugs!

String interpolation
--------------------

The upcoming Python 3.6 release will include a new type of strings: f-strings.
The idea is that when doing string formatting, we can often end up in cases
that are too verbose such as:

    >>> value = 4 * 20
    >>> 'The value is {value}.'.format(value=value)
    'The value is 80.'

or we can end up in situations where the code is unecessarily complex, since
``value`` is detached from where it appears in the string.

>>> 'The value is {}.'.format(value)
'The value is 80.'

The new `f-strings <https://www.python.org/dev/peps/pep-0498/>`_ allow you to
use variable names directly inside the curly brackets:

    >>> f'The value is {value}.'
    'The value is 80.'

You can actually use full Python expressions inside the curly brackets! For
instance:

    >>> a, b = 10, 20
    >>> f'The sum of the values is {a + b}.'
    'The sum of the values is 30.'

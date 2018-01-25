Useful Python 3 features
========================


Division
--------

In Python 2, integer division is the default, so 1/2 evaluates to 0. This means
frequently having to explicitly convert integers to floats when working with
integer variables

.. doctest::
   :pyversion: < 3

    >>> int_one = 1
    >>> int_two = 2
    >>> int_one / int_two
    0
    >>> float(int_one) / int_two
    0.5

or being careful to do things like ``/ 2.`` or ``* 0.5``. In Python 3, the
default division will yield a float, and integer division is accessed using the //
operator

.. doctest:: division3
   :pyversion: >= 3.0
   :hide:

    >>> int_one = 1
    >>> int_two = 2

.. doctest:: division3
   :pyversion: >= 3.0

    >>> int_one / int_two
    0.5
    >>> int_one // int_two
    0

This makes it safer to use by default, since there there is no longer any
implicit conversion to integers.

Recursive glob
--------------

A small but very useful feature in Python 3 is the addition of a recursive
option in the built-in :func:`~glob.glob` function. In Python 2 and 3, this
function can be used to find all files and directories matching a certain
pattern

.. doctest:: glob
   :pyversion: >= 3.5
   :hide:

    >>> import os
    >>> os.makedirs(os.path.join('data', 'subset1'), exist_ok=True)
    >>> os.makedirs(os.path.join('data', 'subset2'), exist_ok=True)
    >>> _ = open(os.path.join('data', 'image.fits'), 'w').write('i')
    >>> _ = open(os.path.join('data', 'subset1', 'a.fits'), 'w').write('a')
    >>> _ = open(os.path.join('data', 'subset1', 'b.fits'), 'w').write('b')
    >>> _ = open(os.path.join('data', 'subset1', 'c.fits'), 'w').write('c')
    >>> _ = open(os.path.join('data', 'subset2', 'd.fits'), 'w').write('d')
    >>> _ = open(os.path.join('data', 'subset2', 'e.fits'), 'w').write('e')

.. doctest:: glob
   :pyversion: >= 3.5

    >>> import os
    >>> import glob
    >>> glob.glob(os.path.join('data', '*.fits'))
    ['data/image.fits']

Now let's say that the ``data`` directory now contains FITS files both
directly in ``data`` and in sub-directories of ``data``. In Python 3, you can
now do

.. doctest:: glob
   :pyversion: >= 3.5
   :options: +NORMALIZE_WHITESPACE

    >>> import os
    >>> import glob
    >>> glob.glob(os.path.join('data', '**', '*.fits'), recursive=True)
    ['data/image.fits', 'data/subset1/a.fits', 'data/subset1/b.fits',
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
to do matrix multiplication (vector product)

.. doctest:: matrix
   :pyversion: >= 3.5

    >>> import numpy as np
    >>> x = np.array([[1, 2], [3, 4]])
    >>> y = np.array([[3, 2], [2, -1]])
    >>> x @ y
    array([[ 7,  0],
           [17,  2]])

Note that this is different from ``x * y``, which returns an element-wise
multiplication of the arrays:

.. doctest:: matrix
   :pyversion: >= 3.5

    >>> x * y
    array([[ 3,  4],
           [ 6, -4]])

Clearing lists
--------------

In Python 2 and 3, dictionaries can easily be emptied using the ``.clear`` method:

.. doctest:: clear
   :pyversion: >= 3.3

    >>> d = {'flux': 1}
    >>> d.clear()
    >>> d
    {}

But Python 2.7 did not allow lists to be cleared in the same way:

.. doctest:: clear
   :pyversion: < 3
   :options: +ELLIPSIS

    >>> li = ['spam', 'egg', 'spam']
    >>> li.clear()
    Traceback (most recent call last):
    ...
    AttributeError: 'list' object has no attribute 'clear'

instead requiring non-intuitive code such as:

.. doctest:: clear
   :pyversion: < 3

    >>> del li[:]
    >>> li
    []

Since Python 3.3, lists can be emptied by using the ``clear`` method:

.. doctest:: clear
   :pyversion: >= 3.3

    >>> li = ['spam', 'egg', 'spam']
    >>> li.clear()
    >>> li
    []

Advanced print function
-----------------------

One of the widely known changes between Python 2 and Python 3 is the change
from a ``print`` statement to a :func:`print` function. This change is not just
esthetic, it now allows you to better customize aspects such as what separator
to use between variables, and whether to go to the next line between successive
print statements.

By default, :func:`print` behaves like the Python 2 print statement in that it
separates variables by spaces and goes to the next line at the end of a print
call:

.. doctest:: print
   :pyversion: >= 3.0

    >>> a, b = 1, 2
    >>> print(a, b)
    1 2

The ``sep`` argument can be used to customize the separator:

.. doctest:: print
   :pyversion: >= 3.0

    >>> print(a, b, sep=', ')
    1, 2

And similarly, the ``end`` argument can be used to customize the end of the line -
this defaults to ``\n``, which is a carriage return (or *newline*):

.. doctest:: print
   :pyversion: >= 3.0

    >>> print("hello"); print("world")
    hello
    world
    >>> print("hello", end=' '); print("world")
    hello world

In the above example, we had to put the print statements on the same line,
because in interactive Python, you will be returned to the Python prompt after
the line is executed. However, in scripts, you can do

.. doctest:: print
   :pyversion: >= 3.0

    print("hello ", end=' ')
    print("world")

Finally, a last useful feature is that it is possible to send the output of the
print calls to file-like objects instead of the main terminal output (the
*standard output*):

.. doctest:: print
   :pyversion: >= 3.0

    >>> f = open('data.txt', 'w')
    >>> print(a, b, file=f)
    >>> f.close()

or better, if you are familiar with the context manager notation:

.. doctest:: print
   :pyversion: >= 3.0

    >>> with open('data.txt', 'w') as f:
    ...     print(a, b, file=f)

Advanced unpacking
------------------

In Python 2, you can use implicit unpacking of variables to go from a list,
tuple, or more generally any *iterable* to separate variables:

.. doctest:: unpacking
   :pyversion: >= 3.0

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
list of 5 items

.. doctest:: unpacking
   :pyversion: >= 3.0

    >>> values = range(5)

and are only interested in the first two, in Python 2 you would need to do
either:

.. doctest:: unpacking
   :pyversion: >= 3.0

    >>> a, b, _, _, _ = values

or

.. doctest:: unpacking
   :pyversion: >= 3.0

    >>> a = values[0]
    >>> b = values[1]

Python 3 now allows users to use the ``*variable`` syntax (similar to ``*args``
in function arguments) to avoid having to write out as many variables than items
in the iterable

.. doctest:: unpacking
   :pyversion: >= 3.0

    >>> a, b, *rest = values
    >>> a
    0
    >>> b
    1
    >>> rest
    [2, 3, 4]

The ``*`` syntax can also be used for e.g. the first variable and variables in the middle

.. doctest:: unpacking
   :pyversion: >= 3.0

    >>> a, *rest, b = range(5)
    >>> a, b
    (0, 4)
    >>> *rest, a, b = range(5)
    >>> a, b
    (3, 4)

This can be used for example to access the first two lines and the last line
in a file:

.. doctest:: unpacking
   :pyversion: >= 3.0
   :hide:

   >>> _ = open('data.txt', 'w').write('\n'.join('a' for i in range(10)))

.. doctest:: unpacking
   :pyversion: >= 3.0

    >>> f = open('data.txt')
    >>> first, second, *rest, last = f.readlines()
    >>> f.close()

Function annotations
--------------------

Since Python 3.5, it is possible to use the following syntax to annotate
functions, to provide information on inputs/outputs. For example, it is possible
to specify *type* annotations:

.. doctest:: annotations
   :pyversion: >= 3.5

    >>> def remove_spaces(x: str) -> str:
    ...     return x.replace(' ', '')

This syntax means that the input as well as the output should be a string. Now
it turns out that Python doesn't do anything with these type annotations (there
are still reasons why developers might want to do this, but this is not
necessarily critical for the typical user).

However, some packages have now implemented their own annotations. For example,
the `Astropy <http://www.astropy.org>`_ package uses these to allow users to
specify what units different variables should be in:

.. doctest:: annotations
   :pyversion: >= 3.5

    >>> import astropy.units as u
    >>> @u.quantity_input
    ... def kinetic_energy(mass: u.kg, velocity: u.m / u.s):
    ...    return 0.5 * mass * velocity ** 2

This does then raise an error if the variables do not have units attached:

.. doctest:: annotations
   :pyversion: >= 3.5
   :options: +ELLIPSIS +IGNORE_EXCEPTION_DETAIL

    >>> kinetic_energy(1, 3)
    Traceback (most recent call last):
    ...
    TypeError: Argument 'mass' to function 'kinetic_energy' has no 'unit'
    attribute. You may want to pass in an Astropy Quantity instead.

or if the units are not compatible/convertible:

.. doctest:: annotations
   :pyversion: >= 3.5
   :options: +ELLIPSIS +IGNORE_EXCEPTION_DETAIL

    >>> kinetic_energy(1 * u.s, 3 * u.km / u.s)
    Traceback (most recent call last):
    ...
    UnitsError: Argument 'mass' to function 'kinetic_energy' must be in
    units convertible to 'kg'.

Other packages will hopefully also provide useful annotations such as these!

Sensible comparison
-------------------

In Python 2, it was possible to compare things that shouldn't really be
comparable:

.. doctest:: comparison
   :pyversion: < 3

    >>> '1' > 2
    True

Whether a string was greater than an integer or a float was not necessarily
predictable or intuitive. In Python 3, this type of comparison is no longer
allowed:

.. doctest:: comparison
   :pyversion: >= 3
   :options: +ELLIPSIS

    >>> '1' > 2
    Traceback (most recent call last):
    ...
    TypeError: '>' not supported between instances of 'str' and 'int'

This should avoid quite a few bugs!

String interpolation
--------------------

Python 3.6 includes a new type of strings: f-strings. The idea is that when
doing string formatting, we can often end up in cases that are too verbose such
as:

.. doctest:: fstring
   :pyversion: >= 2.7

    >>> value = 4 * 20
    >>> 'The value is {value}.'.format(value=value)
    'The value is 80.'

or we can end up in situations where the code is unnecessarily complex, since
``value`` is detached from where it appears in the string.

.. doctest:: fstring
   :pyversion: >= 2.7

    >>> 'The value is {}.'.format(value)
    'The value is 80.'

The new `f-strings <https://www.python.org/dev/peps/pep-0498/>`_ allow you to
use variable names directly inside the curly brackets:

.. doctest:: fstring
   :pyversion: >= 3.6

    >>> f'The value is {value}.'
    'The value is 80.'

You can actually use full Python expressions inside the curly brackets! For
instance:

.. doctest:: fstring
   :pyversion: >= 3.6

    >>> a, b = 10, 20
    >>> f'The sum of the values is {a + b}.'
    'The sum of the values is 30.'

Underscores in numbers
----------------------

Have you ever had issues figuring out whether 100000000 is a hundred million or
a billion? In Python 3.6, you can now add underscores anywhere in an integer,
which allows you to do e.g.:

.. doctest:: underscores
   :pyversion: >= 3.6

    >>> a = 1_000_000_000

This also works with hexadecimal and binary literals, e.g.

.. doctest:: underscores
   :pyversion: >= 3.6

    >>> b = 0b_0011_1111_0100_1110

Unicode strings
---------------

In Python 2, only the basic ASCII character set was available in standard
strings; to use the much more extensive Unicode set of characters, you had to
prefix each string with a u:

.. doctest:: unicode
   :pyversion: < 3

    >>> s1 = "an ascii string"
    >>> s2 = u"The total is €10"

Unicode strings are the default in Python 3. This makes it more straightforward
to e.g., include foreign languages, and print greek symbols (or emoji) in
strings:

.. doctest:: unicode
   :pyversion: >= 3.0

    >>> s3 = "Πύθων"
    >>> s4 = "unicode strings are great! 😍"

Unicode variable names
----------------------

Python 3 allows many unicode symbols to be used in variable names. Unlike Julia
or Swift, which allow any unicode symbol to represent a variable (including
emoji) Python 3 restricts variable names to unicode characters that represent
characters in written languages. In contrast, Python 2 could only use the basic
ASCII character set for variable names.

This means you can use foreign language words and letter-like symbols as
variable names, e.g.:

.. doctest:: unicodevar
   :pyversion: >= 3.0

    >>> π = 3.14159
    >>> jalapeño = "a hot pepper"
    >>> ラーメン = "delicious"

But cannot use, say, emoji:

.. doctest:: unicodevar
   :pyversion: >= 3.0
   :options: +ELLIPSIS

    >>> ☃ = "brrr!"
    Traceback (most recent call last):
    ...
    SyntaxError: invalid character in identifier

One nice use case is for mathematical notation:

.. doctest:: unicodevar
   :pyversion: >= 3.5

   >>> from numpy import array, cos, sin
   >>> def rotate(vector, angle):
   ...     θ = angle
   ...     mat = [[cos(θ), -sin(θ)],
   ...            [sin(θ), cos(θ)]]
   ...     mat = array(mat)
   ...     return mat @ vector

Using unicode variable names like this can make it easier to read complicated
mathematical expressions and compare with the printed definition. Be careful not
to expose unicode variable names in your project's API, as it might be difficult
for others to type these characters. Also, use caution if you're planning to
share your code as it's fairly easy to produce illegible code this way.

More useful exceptions
----------------------

Python 3 makes some error cases easier to catch. For example, to open a file
and catch the error if it's not there:

.. doctest:: exceptions
   :pyversion: >= 3.0

    try:
        f = open('is_it_there.txt')
    except FileNotFoundError:
        # Fallback code...

Doing this in Python 2 is more complicated:

.. doctest:: exceptions
   :pyversion: >= 2.7

    import errno

    try:
        f = open('is_it_there.txt')
    except OSError as e:
        if e.errno == errno.ENOENT:
            # Fallback code...
        else:
            raise  # It was an OSError for something else

Other new exception classes include ``PermissionError``, ``IsADirectoryError``
and ``TimeoutError``. For more information, `see the Python documentation
<https://docs.python.org/3/whatsnew/3.3.html#pep-3151-reworking-the-os-and-io-exception-hierarchy>`__.

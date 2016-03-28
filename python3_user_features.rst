Useful Python 3 features for scientists
=======================================

Unicode strings
---------------

Division
--------

Unicode variable names
----------------------

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

.. note:: Note that we use ``os.path.join`` instead of writing out the path
          by hand (e.g. ``data/*.fits``) to make sure that this works on
          Windows as well as Linux and MacOS X.

Function annotations
--------------------

Matrix multiplication operator
------------------------------

Advanced print function
-----------------------

Concurrent futures
------------------

Advanced unpacking
------------------

In Python 2, you can use implicit unpacking of variables to go from a list, tuple, or more generally any *iterable* to separate variables:

    >>> a, b, c = range(3)
    >>> a
    0
    >>> b
    1
    >>> c
    2

The number of items in the iterable on the right has to match exactly the
number of variables on the left. However, there are cases where one might
only be intersted in the first few items of the iterable. For example, if you
have a list of 5 items::


    >>> values = range(10)

and are only interested in the first two, in Python 2 you would need to do
either:

    >>> a, b, _, _, _ = values

or

    >>> a = values[0]
    >>> b = values[1]

Python 3 now allows users to use the ``*variable`` syntax (similar to ``*args`` in function argumnts) to avoid having to write out as many variables than items in the iterable::

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



Sensible comparison
-------------------
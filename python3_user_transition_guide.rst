Python 3 transition guide
=========================

Switching to Python 3 can seem like a daunting task, but this guide will
provide some tips and resources to help make it more straightforward.

New code you write should be Python 3 compatible
------------------------------------------------

Starting now, *any new code you write should be Python 3 compatible!* This is
the first  step you can take towards switching. Thankfully, the most disruptive
changes to the language have been back-ported to Python 2 so you can ensure
that, when writing Python 2-style code, it will be executable in Python 3. To
use these back-ported features, you must import certain utilities from the
built-in `__future__` package. It is generally recommended that, if you are
still writing Python 2 compatible code, you should import the following four
modules in any code you write::

    from __future__ import (absolute_import, division,
                            print_function, unicode_literals)

Copy any paste this into the import block of any new script or piece of code you
write in Python 2! Most notably, (1) this will enable you to use the Python
3-style `print` function:

    >>> print("It's only a model")

The Python 2 print statement will now fail:

    >>> print "It's only a model"
      File "<stdin>", line 1
        print "It's only a model"
                                ^
    SyntaxError: invalid syntax


And (2), integer division will no longer truncate:

    >>> 1/2
    0

will become:

    >>> 1/2
    0.5

A slightly more annoying issue is that several of the built-in Python packages
have been reorganized or moved. Most of the changes were made to fairly obscure
packages (see [2]_ for a full list), but there are a few notable changes:

- `cPickle` - renamed to `pickle`
- `cProfile` - renamed to `profile`
- `urllib`, `urllib2`, `urlparse` - have been combined into subpackages of
  `urllib`

One other major change is that many built-in methods on container classes (e.g.,
the dictionary) now return iterators instead of `list`s. For example, in
Python 2:

    >>> airspeed = {'ladened': 2, 'unladened': 11}
    >>> airspeed.keys()
    ['ladened', 'unladened']
    >>> airspeed.keys()[0]
    'ladened'

In Python 3, the `.keys()` method instead returns an iterator object:

    >>> airspeed = {'ladened': 2, 'unladened': 11}
    >>> airspeed.keys()
    dict_keys(['ladened', 'unladened'])
    >>> airspeed.keys()[0]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: 'dict_keys' object does not support indexing

These iterator objects support iteration (e.g., you can loop over the
`dict_keys` as you would a list), but as shown above, do not support indexing.
To get a list, just wrap any of these methods in a `list()` call, e.g.:

    >>> the_keys = list(airspeed.keys())
    >>> the_keys[0]
    'ladened'

Give Python 3 a try
-------------------

To switch to Python 3, you don't have to permanently leave Python 2 behind and
you don't have to switch in one sitting. Many of us are trying to balance a
complicated workflow, deadlines, and software update requirements, so luckily it
is possible to try out Python 3 while continuing to work in Python 2 in a
separate Python environment. This is most easily done using a virtual
environment manager. With virtual environments, you can switch over to Python 3,
experiment, see what code runs, what code breaks, but then easily switch back to
Python 2 if need be.

There are several possible choices for managing virtual environments in Python
but to manage multiple *versions* of Python we have found the Anaconda package
manager to be the best all-in-one option. If you are not using Anaconda, we
highly recommend installing it and using it for package and environment
management!

If you are using Anaconda for Python 2 or have just installed Anaconda, it's
easy to create a new environment that uses Python 3. If you have never used
Anaconda (conda) environments, you should have only one environment. If you
type::

    conda env list

in your terminal, you should see a single line like::

    root                  *   /Users/adrian/anaconda

This just tells you that you only have a single ('root') environment. I like to
have two main installations of Python for testing: one that uses the latest
Python 2 version and one that uses the latest Python 3 version. I name the
environments ``two`` and ``three``. For your main Python 2 environment, you can
clone your root environment over (and therefore copy over any packages you've
installed) by doing::

    conda create --name two --clone root

If instead you'd like to create a fresh installation of Python 2 in the new
environment, you can do::

    conda create --name two python=2

(the ``python=2`` tells conda to install the latest version of Python 2 in the
environment named ``two``). We can do the same thing to create a new environment
for Python 3::

    conda create --name three python=3

Again, the ``python=3`` tells conda to install the latest version of Python 3 in
this new environment (named ``three``). To enable an environment, you use::

    source activate <name of environment>

So, for each of these you can use::

    source activate two

and::

    source activate three

to switch back and forth between Python 2 and 3! When I first switched, I found
that I was typing these commands a lot and created aliases in my shell profile
to make it faster::

    alias pytwo="source activate two"
    alias pythree="source activate three"


Identify Python 3 incompatibilities and automatically update them
-----------------------------------------------------------------

If you have a lot of code with Python 2 `print` statements and other Python
3-incompatible lines, it can be a huge pain to go file-by-file and modify the
code to be compliant. Luckily, there are tools to help automate this process.

TODO

Advanced: Maintaining code that is compatible with Python 2 and 3
-----------------------------------------------------------------

TODO: six
TODO: wrap iterators with list()

References
``````````

[1] http://conda.pydata.org/docs/using/envs.html
[2] http://python3porting.com/stdlib.html

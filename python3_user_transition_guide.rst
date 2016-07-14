Python 3 transition guide
=========================

Switching to Python 3 can seem like a daunting task, but this guide will
provide some tips and resources to help make it more straightforward.

Changes you can make today to make the transition easier
--------------------------------------------------------

Any new code you write can easily be Python 3 compatible! This is the first step
you can take towards switching. Thankfully, the most disruptive changes to the
language have been back-ported to Python 2 so you can ensure that, when writing
new code that you intend to execute with Python 2, it will be executable in
Python 3 once you switch. To use these back-ported changes, you must import
certain utilities from the built-in ```__future__``
<https://docs.python.org/2/library/__future__.html>`_ package. It is generally
recommended that, if you are still writing Python 2 compatible code, you should
import the following four modules in any code you write

.. code-block:: python

    from __future__ import division, print_function, absolute_import

Copy any paste this into the import block of any new script or piece of code you
write in Python 2! Most notably, (1) this will enable you to use the Python
3-style ``print`` function:

.. code-block:: python

    >>> print("It's only a model")

The Python 2 print statement will now fail:

.. code-block:: python

    >>> print "It's only a model"
      File "<stdin>", line 1
        print "It's only a model"
                                ^
    SyntaxError: invalid syntax


And (2), integer division will no longer truncate:

.. code-block:: python

    >>> 1/2
    0

will become:

.. code-block:: python

    >>> 1/2
    0.5

For now, don't worry about `absolute_import` (but do include it in your
import!). If you'd like to know more, `this is a good blog post <https://blog.ta
nkywoo.com/python/2013/10/07/python-relative-and-absolute-import.html>`_ that
explains what this does. If you want to know even more, here is a `StackOverflow
post <http://stackoverflow.com/questions/33743880/what-does-from-future-import-a
bsolute-import-actually-do>`_ about it.

Other major changes in Python 3
-------------------------------

A slightly more annoying issue is that several of the standard library Python
packages have been reorganized or moved. Most of the changes were made to fairly
obscure packages (`full list here <http://python3porting.com/stdlib.html>`_),
but there are a few notable changes:

- ``cPickle`` - renamed to ``pickle``
- ``cProfile`` - renamed to ``profile``
- ``urllib``, ``urllib2``, ``urlparse`` - have been combined into subpackages of
  ``urllib``

To move to Python 3, you can simply replace the imports. To maintain
compatibility between Python 2 and 3, an easy solution is to use the
`six <http://pythonhosted.org/six/>`_ package (see discussion below in
:ref:`maintain-compatibility`).

One other major change is that many built-in methods on container classes (e.g.,
the dictionary) now return iterators instead of ``list`` s. For example, in
Python 2

.. code-block:: python

    >>> airspeed = {'ladened': 2, 'unladened': 11}
    >>> airspeed.keys()
    ['ladened', 'unladened']
    >>> airspeed.keys()[0]
    'ladened'

In Python 3, the ``.keys()`` method instead returns an iterator object

.. code-block:: python

    >>> airspeed = {'ladened': 2, 'unladened': 11}
    >>> airspeed.keys()
    dict_keys(['ladened', 'unladened'])
    >>> airspeed.keys()[0]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: 'dict_keys' object does not support indexing

These iterator objects support iteration (e.g., you can loop over the
``dict_keys`` as you would a list), but as shown above, do not support indexing.
To get a list, just wrap any of these methods in a call to ``list()``, e.g.

.. code-block:: python

    >>> the_keys = list(airspeed.keys())
    >>> the_keys[0]
    'ladened'

Upgrading existing Python 2 code
--------------------------------

If you have a lot of code with Python 2 ``print`` statements and other Python
3-incompatible lines, it can be a huge pain to go file-by-file and modify the
code to be compliant. Python provides a tool -- ``2to3`` -- to automatically
identify code that will error in Python 3 and, optionally, to update it in
place. The `Python documentation has a page
<https://docs.python.org/2/library/2to3.html>`_ on how to use this tool. For a
single file, calling ``2to3`` on the file without any flags will output a diff
showing any invalid code. For example, if we have a script called "test.py"
containing a single line ``print "yo"``

.. code-block:: diff

    % 2to3 test.py
    --- test.py (original)
    +++ test.py (refactored)
    @@ -1 +1 @@
    -print "yo"
    +print("yo")
    RefactoringTool: Files that need to be modified:
    RefactoringTool: test.py

Note that it finds the Python 2-style ``print`` statement and even tells you what
to change it to, but this did not modify the file to make it compatible. To
actually fix the incompatible code, call with the ``-w`` flag

.. code-block:: diff

    % 2to3 -w test.py
    --- test.py (original)
    +++ test.py (refactored)
    @@ -1 +1 @@
    -print "yo"
    +print("yo")
    RefactoringTool: Files that were modified:
    RefactoringTool: test.py

This can also be run on entire packages or directory trees to update code in
bulk.

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
management! If you have a separate Python installation and prefer it, you can
still install Anaconda to play with switching to Python 3 without messing up
your other environment.

If you are using Anaconda for Python 2 or have just installed Anaconda, it's
easy to create a new environment that uses Python 3. If you have never used
`Anaconda (conda) environments <http://conda.pydata.org/docs/using/envs.html>`_,
you should have only one environment. If you type

.. code-block:: bash

    % conda env list

in your terminal, you should see a single line like

.. code-block:: bash

    root                  *   /Users/adrian/anaconda

This just tells you that you only have a single ('root') environment. It can be
useful to have two main installations of Python for testing: one that uses the
latest Python 2 version and one that uses the latest Python 3 version. Here
we'll create these two environments and name them ``two`` and ``three``. For
your main Python 2 environment, you can clone your root environment over (and
therefore copy over any packages you've installed) by doing

.. code-block:: bash

    % conda create --name two --clone root

If instead you'd like to create a fresh installation of Python 2 in the new
environment, you can do

.. code-block:: bash

    % conda create --name two python=2

(the ``python=2`` tells conda to install the latest version of Python 2 in the
environment named ``two``). We can do the same thing to create a new environment
for Python 3

.. code-block:: bash

    % conda create --name three python=3

Again, the ``python=3`` tells conda to install the latest version of Python 3 in
this new environment (named ``three``). To enable an environment, you use

.. code-block:: bash

    % source activate <name of environment>

So, for each of these you can use

.. code-block:: bash

    % source activate two

and

.. code-block:: bash

    % source activate three

to switch back and forth between Python 2 and 3! After installing Python 3, you
may find yourself typing these commands a lot to switch back and forth -- you
may want to create aliases in your shell profile to make it faster

.. code-block:: bash

    % alias pytwo="source activate two"
    % alias pythree="source activate three"

Once these two environments are set up, you may want to stop using the root
environment so you can quickly tell whether you are using Python 2 or 3. But,
by default any new shell you open will use the ``root`` environment. An easy way
to change this is to activate whichever environment you want to use as default
in your profile or rc file as well. For example, if you want to move to Python 3
you can add

.. code-block:: bash

    pythree

below the definition of your alias, which will call ``source activate three``
whenever the profile or rc file is run.

.. _maintain-compatibility:

Advanced: Writing code that is compatible with Python 2 and 3
-------------------------------------------------------------

As mentioned above, a number of standard library packages have been reorganized
or renamed, meaning that import statements may fail when executing code in
either Python 2 or 3. The pip-installable `six <http://pythonhosted.org/six/>`_
package is here to help! ``six`` has a subpackage that normalizes the import
paths for these cases so you don't have to write extra code to check whether the
code is executed in 2 or 3. For example:

- ``cPickle`` / ``pickle``

.. code-block:: python

    >>> from six.moves import cPickle as pickle

- ``urllib``, ``urllib2``

.. code-block:: python

    >>> from six.moves import urllib

This will work in Python 2 or 3. `This website
<https://wiki.python.org/moin/PortingToPy3k/BilingualQuickRef>`_ contains a
number of other useful tips for maintaining code that runs in both 2 and 3.

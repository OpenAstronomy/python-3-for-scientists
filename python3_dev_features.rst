Useful Python 3 features for developers
=======================================

Keyword-only Arguments
----------------------

.. note::

    Keyword-only arguments are valid in Python 3. Code that uses them will not
    run under Python 2.

Interfaces, especially useful ones, are prone to being changed over time. This
can present a problem, since when one changes an interface in an incompatible
way, all users of that interface must also be updated. Since the author of an
interface and the person using that interface are not always the same person,
this issue can lead to a considerable amount of churn, pain, breakage, and
gnashing of teeth.

To explain this, we need to review the different kinds of parameters that a
python function can be defined to take. The simplest is a positional
parameter. The following function has two positional poarameters::

    def positional_function(foo, bar):
        pass

Positional parameters are the simplest, and default kind of
parameter. In addition, both Python 2 and 3 also allow keyword arguments. These
kinds of arguments make it possible to define a default value for a parameter::

    def keyword_function(foo=3, bar=4)

One can specify the values of positional arguments when calling functions in two
ways. The following are all valid::

    positional_function(3, 4)
    positional_function(3, bar=4)
    positional_function(foo=3, bar=4)

However, the following is not valid::

    positional_function(4, foo=3)

This will raise a ``TypeError``, because Python interprets this line to mean
that `foo` is getting specified twice. For positional arguments, the *order* of
the arguments matters.

Just like positional arguments, keyword arguments can be specified using their
order, or by specifying their names. All of these are valid::

    keyword_function(3, 4)
    keyword_function(3, bar=4)
    keyword_function(foo=3, bar=4)

Now that we know the difference between positional arguments and keyword
arguments, we can introduce keyword-only arguments. These are *only* specifiable
via the name of the argument, and *cannot* be specified as a positional
argument.

For example, the following function takes a positional argument and two
keyword-only arguments::

    def keyword_only_function(parameter, *, option1=False, option2=''):
        pass

In this example, ``option1``, and ``option2`` are only specifiable via the
keyword argument syntax. The following is valid::

    keyword_only_function(3, option1=True, option2='Hello World!')

But this example will raise an error::

    keyword_only_function(3, True, 'Hello World!')

The option to specify that a parameter is keyword-only makes it possible ensure
that users of a function cannot accidentally use an option that is controlled by
a keyword-only argument. It also becomes possible to reorder keyword-only
arguments in a function signature, since keyword-only arguments are only
accessible via the name of the argument, not its position in the function
signature.

Chained Exceptions
------------------

In python, the most natural way to communicate about an error is to raise an
exception. If the error state is recoverable, the exception might be caught
elsewhere and then dealt with. If it is not recoverable, the original exception
might be re-raised to be dealt with at a higher level, or a completely new
exception could be raised.

In Python 2, the error message that gets printed out when an exception is raised
to the user level only contains information about the last exception that was
raised. Information from intermediate exceptions generated at lower levels in
the code are lost unless care is taken to re-raise the exception with the
appropriate information and context. This can be painful, especially when
debugging a library, as the error message containing information about the *real*
error will get discarded in favor of a more generic error message.

Take the following short example::

    my_dict = {'a': 1, 'b': 2}

    try:
        value = my_dict['c']
    except KeyError:
        raise RuntimeError("dict access failed")

In Python 3, executing this snippet will print the following::

    Traceback (most recent call last):
      File "test.py", line 4, in <module>
        value = my_dict['c']
    KeyError: 'c'

    During handling of the above exception, another exception occurred:

    Traceback (most recent call last):
      File "test.py", line 6, in <module>
        raise RuntimeError("dict access failed")
    RuntimeError: dict access failed

Under Python 2, you will see a much less useful error message::

    Traceback (most recent call last):
      File "test.py", line 6, in <module>
        raise RuntimeError("dict access failed")
    RuntimeError: dict access failed

Even in this contrived example you can see how the extra information from the
original exception can ease debugging. Note how under Python 3, the original
exception is printed out, *along with the original traceback*. This makes it
possible to immediately see where the original exception was raised, and where
error handling code is re-raising another exception. In real code, where errors
might propagate between files and in the worst case, across complex codebases,
this extra information can be enough to head off an afternoon of fruitless head
scratching and troubleshooting.

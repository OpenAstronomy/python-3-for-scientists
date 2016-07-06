Useful Python 3 features for developers
=======================================

Chained Exceptions
------------------

In python, the most natural way to communicate about an error is to raise an
exception. If the error state is recoverable, the exception might be caught
elsewhere and then dealt with, it might be re-raised to be dealth with at a
higher level, or a completely new exception could be raised.

In Python 2, the error message that gets printed out when an exception is
presented to a user only contains information about the last exception that was
raised. Information from intermediate exceptions generated at lower levels in
the code are generally lost. This can be painful, especially when debuggin a
library, as the error message containing information about the *real* error
will get discarded in favor of a more generic error message.

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
original exception can ease debugging. In real code, where errors might
propagate between files and in the worst case, across complex codebases, this
extra information can be enough to head off an afternoon of fruitless head
scratching and troubleshooting.


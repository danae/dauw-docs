==========
Data types
==========

The following chapter describes the data types that are built in into the interpreter.


Unit type — ``Nothing``
=======================

A value of type ``Nothing`` represents the `unit type <https://en.wikipedia.org/wiki/Unit_type>`_, and generally the absence of a value, compared to ``null`` or ``nil`` in other programming languages. The only way to instantiate a value of this type is by using the keyword ``nothing``.


Boolean type — ``Bool``
=======================

A value of type ``Bool`` represents the `Boolean type <https://en.wikipedia.org/wiki/Boolean_data_type>`_, a truth value of logic and Boolean algebra. The ``Bool`` type supports two values, denoted by the keywords ``false`` and ``true``.

Any value has a truth value for use in the condition of a control flow expression or as an operand of a logical operation. By default, a value is considered ``true``, except for the following cases that are considered ``false``:

* The literals ``nothing`` and ``false``.
* The ``Int`` value ``0`` and the ``Real`` values ``0.0`` and ``-0.0``.
* Any value that implements the length operation which yields a result of zero, i.e. ``#a == 0``. This includes empty ``String`` and ``Collection`` values.


Numeric types — ``Int, Real``
=============================

There are two numeric types: integer values and double-precisioun floating point values. They are represented by the ``Int`` and ``Real`` types respectively, which both descend from the ``Number`` type.


Strings
=======

A string value is an array of Unicode code points, which are stored in UTF-8 encoding. Strings are represented by the ``String`` type.

Strings support interpolation of variables and arbitrary expressions too. A variable can be interpolated by prepending a dollar sign before it (``$foo``), while an expression can be interpolated by surrounding it with parentheses and prepending a dollar sign before it. The following examples demonstrate how string interpolation can be used:

::

  let foo = 42
  print("The value of foo is: $foo")  -- Outputs "The value of foo is: 42"
  print("Math $(3 + 4 * 5) is fun!")  -- Outputs "Math 23 is fun!"

=========================
Expressions and operators
=========================

.. |_| unicode:: 0xA0
   :trim:

This chapter explains the meaning of the elements of simple expressions in Dauw, along with their respective syntax grammars.


Atoms
=====

Atoms are the basic building blocks of expressions, which consist of literals, identifiers or grouped expressions. A grouped atom yields whatever the enclosed expression yields and is used to enclose expressions with lower precedence in a high-precedence context::

    atom                  → literal | name | sequence | record | lambda | grouped
    grouped               → '(' expression ')'

Literals
--------

A literal atom specifies an immutable value of one of the ``Nothing``, ``Bool``, ``Int``, ``Real``, ``Rune`` or ``String`` types. The former two types are defined with the keywords ``nothing``, ``false`` and ``true``, while the latter can take several forms. A literal atom yields an object corresponding to the matching literal type with the specified immutable value::

    literal               → 'nothing' | 'false' | 'true' | INT | REAL | RUNE | STRING

Names
-----

An identifier occuring as an atom is a name. When the name is bound to an object in the current scope, the evaluation of the atom yields that object, otherwise a ``NameError`` is reported::

    name                  → IDENTIFIER

Sequences
---------

A sequence atom yields a ``Sequence`` object when it is evaluated. The supplied sequence items are each evaluated and added into the sequence object::

    sequence              → '[' (sequence_item (',' sequence_item)*)? ']'
    sequence_item         → expression

Records
-------

A record atom yields a ``Record`` object when it is evaluated. The supplied record items are each evaluated and put into the record object at the key specified by the identifier::

    record                → '{' (record_item (',' record_item)*)? '}'
    record_item           → IDENTIFIER ':' expression

Lambdas
-------

A lambda atom yields an anonymous ``Function`` object, taking the specified parameters and evaluating the specified body when the function is invoked. The function object acts like a fully-fledged function, with the diffference that it is not bound to a name::

    lambda                → '\' '(' parameters? ')' (':' type)? '=' assignment


Primaries
=========

Primary expressions represent the most tightly bound operations in the language, which consists of calls and accesses. Primaries yield the following values when they are evaluated::

    primary               → atom ('(' arguments? ')' | '.' IDENTIFIER)*

A call primary calls a ``Callable`` object specified by the evaluated primary expression, by invoking the ```()``` function with the specified arguments.

A access primary accesses a field specified by the identifier in an object specified by the evaluated primary expression.


Operators
=========

Operation expressions can be made by using primary expressions as operands with one or more operators::

    operation             → logic_or

The following operators are defined, which are listed below from highest to lowest precedence along with their associativity:

======  ==============================================  ==================================================  ==========
Prec    Operator                                        Description                                         Associates
======  ==============================================  ==================================================  ==========
1       ``()`` ``.``                                    Call, Access (covered by primaries)                 Left
2       ``-`` ``#`` ``$``                               Negation, Length, String                            Right
3       ``*`` ``/`` ``//`` ``%``                        Multiplication, Division, Quotient, Remainder       Left
4       ``+`` ``-``                                     Addition, Subtraction                               Left
5       ``..``                                          Range                                               None
6       ``<=>``                                         Three-way comparison                                None
7       ``<`` ``<=`` ``>`` ``>=`` ``=~`` ``!~``         Comparison, Match                                   None
8       ``==`` ``!=`` ``===`` ``!==`` ``=?`` ``!?``     Equality, Identity, Instance of                     None
9       ``not``                                         Logic not                                           Right
10      ``and``                                         Logic and                                           Left
11      ``or``                                          Logic or                                            Left
======  ==============================================  ==================================================  ==========

Most operators are overloadable by defining a corresponding function with one (for unary operators) or two (for binary operators) arguments; more on overloading is described later.

Unary operators
---------------

The following are the unary operators, which all have the same precedence::

    unary                 → ('-' | '#' | '$') unary | primary

The unary ``-`` operator yields the negation of its operand. If the operand is an ``Int`` or ``Real``, the operand is negated and the result is returned with the same type as the original.

The unary ``#`` operator yields the length of its operand as an ``Int``. This operator is most commonly implemented on ``String`` and ``Sequence`` types. The operator can be overloaded by providing a ```#``` method on the operand type, taking no arguments.

The unary ``$`` operator yields a string representation of its operand as a ``String``. The operator can be overloaded by providing a ```$``` method on the operand type, taking no arguments.

.. note::
    The unary operators can be overloaded by providing a method with the name of the operator on the operand type, taking no arguments.


Arithmetic operators
--------------------

The following binary operators are used for arithmetic operations on numbers, while some also act on operands of other types::

    range                 → term ('..' term)?
    term                  → factor (('+' | '-') factor)*
    factor                → unary (('*' | '/' | '//' | '%') unary)*

The binary ``..`` operator yields a range of values specified by its operands. If the operands are both an ``Int`` or ``Real``, the operator yields a range that uses its operands as  the minimum and exclusive maximum value, respectively.

The binary ``+`` operator yields the sum of its operands. If the operands are both an ``Int`` or ``Real``, the operands are added together; note that combining ``Int`` and ``Real`` operands is not supported without explicit conversions. If the operands are both a ``String``, the operands are concatenated.

The binary ``-`` operator yields the difference of its operands. If the operands are both an ``Int`` or ``Real``, the second operand is subtracted from the first operand; note that combining ``Int`` and ``Real`` operands is not supported without explicit conversions.

The binary ``*`` operator yields the product of its operands. If the operands are both an ``Int`` or ``Real``, the operands are multiplied together; note that combining ``Int`` and ``Real`` operands is not supported without explicit conversions. For a ``String`` and ``Int`` operand, the result is the first operand repeated the amount of times specified by the second operand.

The binary ``/`` operator yields the quotient of its operands. If the operands are both an ``Int`` or ``Real``, the first operand is divided by the second operand; note that combining ``Int`` and ``Real`` operands is not supported without explicit conversions. The result of the division will always be a ``Real`` regardless of the types of the operands.

The binary ``//`` and ``%`` operators yield the floor quotient and remainder of its operands, respectively. If the operands are both an ``Int`` or ``Real``, the ``//`` operator yields the quotient of the operand, while the ``%`` operator yields the remainder, both using floor division. The sign of the result is equal to the sign of the second operand. The two operators are related such that the identity ``a == (a // b) * b + (a % b)`` holds.

.. note::
    The arithmetic operators can be overloaded by providing a method with the name of the operator on the first operand type, taking the second operand as an argument.


Relational operators
====================

The following binary operators are used for relational comparison::

    equality              → comparison (('==' | '!=' | '===' | '!==') comparison)?
    comparison            → threeway (('<' | '<=' | '>' | '>=' | '=~' | '!~') threeway)?
    threeway              → range ('<=>' range)?

The binary ``<=>`` operator yields an ``Int`` with value less than, equal to, or greater than zero if the first operand is less than, equal or greater than the second operand, respectively. The operator is defined for operands that both are a ``Int``, ``Real``, ``Rune``, or ``String``, and can be used to lexicographically sort a sequence of objects. The binary ``<``, ``<=``, ``>``, and ``>=`` operators delegate to the ``<=>`` operator to yield a ``Bool`` result that respresents if the first operand is less than, less than or equal, greater than, or greater than or equal to the second operand, respectively.

The binary ``=~`` operator yields a ``Bool`` result that represent if the first operand matches the second operand. The binary ``!~`` returns the negated ``Bool`` result of ``=~``.

The binary ``==`` operator yields a ``Bool`` result that represents if its operands are equal to each other using **value equality**. The binary ``!=`` operator returns the negated ``Bool`` result of ``==``.

The binary ``===`` operator yields a boolean result that represents if its operands are identical to each other using **reference equality**. The binary ``!==`` operator returns the negated ``Bool`` result of ``===``.

.. note::
    Only the ``<=>``, ``=~`` and ``==`` relational operators can be overloaded by providing a method with the name of the operator on the first operand type, taking the second operand as an argument. The other relational operators delegate calculating the result to one of these three operators.

    Since the ``===`` and ``!==`` relational operators always compare the values or addresses of values of the operands, those cannot be overloaded.


Logic operators
===============

The following binary operators are used for Boolean operations::

    logic_or              → logic_and ('or' logic_and)*
    logic_and             → logic_not ('and' logic_not)*
    logic_not             → 'not' logic_not | equality

* Binary ``not`` yields the negated evaluated ``Bool`` value of its operand.

* Binary ``and`` yields the first operand if that evaluates to ``false``, or the second operand otherwise. Note that this operator short-circuits evaluation of the right operand.

* Binary ``or`` yields the first operand if that evaluates to ``true``, or the second operand otherwise. Note that this operator short-circuits evaluation of the right operand.

=================
Basic expressions
=================

This chapter explains the meaning of the elements of basic expressions in Dauw, along with their respective syntax grammars.

Basic expressions are described by the following syntax grammar::

    operation             → logic_or
    logic_or              → logic_and ('or' logic_and)*
    logic_and             → logic_not ('and' logic_not)*
    logic_not             → 'not' logic_not | equality
    equality              → comparison (('==' | '!=' | '===' | '!==') comparison)?
    comparison            → threeway (('<' | '<=' | '>' | '>=' | '=~' | '!~') threeway)?
    threeway              → range ('<=>' range)?
    range                 → term ('..' term)?
    term                  → factor (('+' | '-') factor)*
    factor                → unary (('*' | '/' | '//' | '%') unary)*
    unary                 → ('-' | '#' | '$') unary | primary

    primary               → atom ('(' arguments? ')' | '.' IDENTIFIER)*

    atom                  → literal | name | sequence | record | lambda | grouped
    literal               → 'nothing' | 'false' | 'true' | INT | REAL | RUNE | STRING
    name                  → IDENTIFIER
    sequence              → '[' (sequence_item (',' sequence_item)*)? ']'
    sequence_item         → expression
    record                → '{' (record_item (',' record_item)*)? '}'
    record_item           → IDENTIFIER ':' expression
    lambda                → '\' '(' parameters? ')' (':' type)? '=' assignment
    grouped               → '(' expression ')'


Atoms
=====

Atoms are the basic building blocks of expressions, which consist of literals, identifiers or grouped expressions. A grouped atom yields whatever the enclosed expression yields and is used to enclose expressions with lower precedence in a high-precedence context:

A literal atom specifies an immutable value of one of the ``Nothing``, ``Bool``, ``Int``, ``Real``, ``Rune`` or ``String`` types. The former two types are defined with the keywords ``nothing``, ``false`` and ``true``, while the latter can take several forms. A literal atom yields an object corresponding to the matching literal type with the specified immutable value.

An identifier occuring as an atom is a name. When the name is bound to an object in the current scope, the evaluation of the atom yields that object, otherwise a ``NameError`` is reported.

A sequence atom yields a ``Sequence`` object when it is evaluated. The supplied sequence items are each evaluated and added into the sequence object.

A record atom yields a ``Record`` object when it is evaluated. The supplied record items are each evaluated and put into the record object at the key specified by the identifier.

A lambda atom yields an anonymous ``Function`` object, taking the specified parameters and evaluating the specified body when the function is invoked. The function object acts like a fully-fledged function, with the diffference that it is not bound to a name.


Primaries
=========

Primary expressions represent the most tightly bound operations in the language, which consists of calls and accesses. Primaries yield the following values when they are evaluated.

A call primary calls a ``Callable`` object specified by the evaluated primary expression, by invoking the ```()``` function with the specified arguments.

A access primary accesses a field specified by the identifier in an object specified by the evaluated primary expression.


Prefix operators
================

Dauw supports the following prefix operators:

========  ================
Operator  Description
========  ================
``-``     Negation
``#``     Length
``$``     String
========  ================

The negation operation yields the negation of an ``Int`` or ``Real`` operand, and the result has the same type as the operand.

The length operation yields the following results based on the type of its operand:

* For a ``String`` operand, the operation yields the number of code points contained in the string as an ``Int``.
* For a ``Collection`` operand, the operation yields the number of items contained in the collection as an ``Int``.

The string operation yields a human-readable string representation of the operand as a ``String``, and can be overridden for user-defined types.

.. note::
    The prefix operators can be overloaded by providing a method with the name of the operator on the operand type, taking no arguments, for example::

        type Vector2 = {x: Real, y: Real}

        impl Vector2
            def `-`() = Vector2(-self.x, -self.y)
            def `$`() = "Vector2($(self.x), $(self.y))"


Arithmetic operators
====================

Dauw supports the following aritmethic operators, which are all infix operators:

========  ================
Operator  Description
========  ================
``+``     Addition
``-``     Subtraction
``*``     Multiplication
``/``     Division
``//``    Quotient
``%``     Remainder
========  ================

With the exception of the division operation, the arithmetic operators yield the following results based on the types of their operands:

* If the operands both are an ``Int``, the operation is performed over integers and the result is an ``Int``.
* If the operands both are a ``Real``, the operation is performed following the IEEE 754 standard for floating-point arithmetic and the result is a ``Real``.

The division operation always converts its operands to ``Real`` before calculating the result, which is always a ``Real``.

The quotient and remainder operations are defined as yielding the quotient and remainder of dividing both operands using floor division, respectively. The results of the two operators are related by the identity ``a == (a // b) * b + (a % b)``.

.. note::
    The arithmetic operators can be overloaded by providing a method with the name of the operator on the first operand type, taking the second operand as an argument, for example::

        type Vector2 = {x: Real, y: Real}

        impl Vector2
            def `+`(other: Point) = Vector2(self.x + other.x, self.y + other.y)
            def `*`(scalar: Real) = Vector2(self.x * scalar, self.y * scalar)


Relational operators
====================

Dauw supports the following relational operators, which are all infix operators:

========  ================
Operator  Description
========  ================
``<=>``   Comparison
``<``     Less than
``<=``    Less than or equal
``>``     Greater than
``>=``    Greater than or equal
``==``    Equality
``!=``    Inequality
``===``   Identity
``!==``   Non-identity
``=~``    Match
``!~``    Non-match
========  ================

The comparison operation yields an ``Int`` with a value less than, equal to, or greater than zero if the first operand is less than, equal or greater than the second operand, respectively. It yields the following results based on the types of its operands:

* If the operands both are an ``Int``, the operation is performed over integers, comparing the numeric value of the operands.
* If the operands both are a ``Real``, the operation is performed following the IEEE 754 standard for floating-point arithmetic, comparing the numeric value of the operands.
* If the operands both are a ``Rune``, the operation compares the operands by code point and returns a result reflecting their lexicographical order.
* If the operands both are a ``String``, the operation compares the operands by code point and returns a result reflecting their lexicographical order.

With the exception of the match operation, all other relational operators yield a ``Bool`` result. The less than, less than or equal, greater than, and greater than or equal operations are defined for two operands ``a`` and ``b`` with the following properties:

* ``a < b`` yields if the result of ``a <=> b`` is less than zero.
* ``a <= b`` yields if the result of ``a <=> b`` is less than or equal to zero.
* ``a > b`` yields if the result of ``a <=> b`` is greater than zero.
* ``a >= b`` yields if the result of ``a <=> b`` is greater than or equal to zero.

The equality, inequality, identity and non-identity operations are defined for two operands ``a`` and ``b`` with the following properties:

* ``a == b`` yields if the operands are equal to each other using **value equality**, of which the behaviour can be overridden by user-defined types. If no explicit ``==`` operation is defined for the types of the operands, the operation yields if the result of ``a <=> b`` is equal to zero.
* ``a != b`` yields exactly the negation of ``a == b``.
* ``a === b`` yields if the operands are equal to each other using **reference equality**, which compares the memory of the operands.
* ``a !== b`` yields exactly the negation of ``a === b``.

The match operation yields the following results base on the types of the two operands ``a`` and ``b``, listed in the following table:

================  ================  ================
Left              Right             Result
================  ================  ================
``String``        ``Rune``          Yields an ``Int?`` result containing the index of the first occurrence of the code point denoted by ``b`` in ``a``, or ``nothing`` if no match was found.
``String``        ``String``        Yields an ``Int?`` result containing the index of the first occurrence of the sequence of code points denoted by ``b`` in ``a``, or ``nothing`` if no match was found.
================  ================  ================

Note that since Dauw is a strongly typed language, none of the relational operators coerces the types of their operands, i.e. no conversions from ``String`` to ``Int`` or vice versa occur while performing those operations.

.. note::
    Only the ``<=>``, ``==`` and ``=~`` operators can be overloaded by providing a method with the name of the operator on the first operand type, taking the second operand as an argument. As described, the other relational operators delegate calculating the result to one of these three operators. For example::

        type Vector2 = {x: Real, y: Real}

        impl Vector2
            def `<=>`(other: Point) = self.x <=> other.x and self.y <=> other.y

    Since the ``===`` and ``!==`` relational operators always compare the values or addresses of values of the operands, those cannot be overloaded.


Logical operators
=================

Dauw supports the the logical operators ``not``, ``and``, and ``or``. These operations coerce their operands to their truth values, as specified by the ``Bool`` type, prior to calculating the result of the operation.

The logical not operation always yields a ``Bool`` result negating the Boolean value of its operand. The logical and operation yields its first operand if its truth value is ``false``, otherwise it yields its second operand. The logical or operation yields its first operand if its truth value is ``true``, otherwise it yields its second operand. Both the ``and`` and ``or`` operations use short-circuit evaluation, i.e. the second operand is evaluated only if necessary.


Precedence
==========

The following table lists the precedence and associativity of the operators, from highest to lowest priority:

======  ==============================================  ==================================================  ==========
Prec    Operator                                        Description                                         Associates
======  ==============================================  ==================================================  ==========
1       ``()`` ``.``                                    Call, Access                                        Left
2       ``-`` ``#`` ``$``                               Negation, Length, String                            Right
3       ``*`` ``/`` ``//`` ``%``                        Multiplication, Division, Quotient, Remainder       Left
4       ``+`` ``-``                                     Addition, Subtraction                               Left
5       ``..``                                          Range                                               None
6       ``<=>``                                         Three-way comparison                                None
7       ``<`` ``<=`` ``>`` ``>=`` ``=~`` ``!~``         Comparison, Match                                   None
8       ``==`` ``!=`` ``===`` ``!==``                   Equality, Identity                                  None
9       ``not``                                         Logical not                                         Right
10      ``and``                                         Logical and                                         Left
11      ``or``                                          Logical or                                          Left
======  ==============================================  ==================================================  ==========

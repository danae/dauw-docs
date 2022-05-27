======
Errors
======

Error hierarchy
===============

The following code block displays the type hierarchy for errors::

    Error
    +-- CompilerError
        +-- SyntaxError
        +-- TypeError
            +-- TypeMismatchError
            +-- TypeUnresolvedError
        +-- ValueError
            +-- ValueMismatchError
            +-- ValueOverflowError
    +-- RuntimeError
        +-- ArithmeticError
            +-- DivisionByZeroError
            +-- ConversionError
        +-- StackOverflowError
        +-- StackUnderflowError
        +-- UnimplementedError

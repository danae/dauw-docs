========================
Control flow expressions
========================

Control flow expressions are described by the following syntax grammar::

    control               → if | for | while | until | block | operation
    if                    → 'if' operation 'then' expression ('else' expression)?
    for                   → 'for' IDENTIFIER 'in' operation 'do' expression
    while                 → 'while' operation 'do' expression
    until                 → 'until' operation 'do' expression
    block                 → NEWLINE INDENT line+ DEDENT

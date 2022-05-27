===================
Appendix I: Grammar
===================

Lexical grammar
===============

The lexical grammar of Dauw is used by the lexer to group characters into tokens::

    IDENTIFIER            → ASCII_IDENTIFIER | STROPPED_IDENTIFIER
    ASCII_IDENTIFIER      → (ALPHA | '_') ( ALPHA | DIGIT | '_')*
    STROPPED_IDENTIFIER   → '`' <any source character except '`' or newline> '`'
    ALPHA                 → 'a'...'z' | 'A'...'Z'
    DIGIT                 → NONZERODIGIT | '0'
    NONZERODIGIT          → '1'...'9'
    HEXDIGIT              → DIGIT | 'a'...'f' | 'A'...'F'

    INT                   → SIGNED_INT | HEX_INT
    SIGNED_INT            → '-'? UNSIGNED_INT
    UNSIGNED_INT          → '0' | NONZERODIGIT ('_' | DIGIT)*
    HEX_INT               → '0' ('x' | 'X') HEXDIGIT ('_' | HEXDIGIT)*

    REAL                  → SIGNED_INT (('.') UNSIGNED_INT EXPONENT?) | EXPONENT)
    EXPONENT              → ('e' | 'E') ('+' | '-')? UNSIGNED_INT

    RUNE                  → "'" RUNE_CHAR | RUNE_ESCAPE_SEQ "'"
    RUNE_CHAR             → <any source character except "'", '\' or newline>
    RUNE_ESCAPE_SEQ       → '\' ("'" | '\' | 'b' | 'f' | 'n' | 'r' | 't' | 'u' '{' HEXDIGIT{1,6} '}')
    STRING                → '"' (STRING_CHAR | STRING_ESCAPE_SEQ)* '"'
    STRING_CHAR           → <any source character except '"', '\' or newline>
    STRING_ESCAPE_SEQ     → '\' ('"' | '\' | 'b' | 'f' | 'n' | 'r' | 't' | 'u' '{' HEXDIGIT{1,6} '}')


Syntax grammar
==============

The syntax grammar of Dauw is used to parse the linear sequence of tokens into the nested syntax tree structure::

    script                → line* EOF
    line                  → (COMMENT | expression COMMENT?) NEWLINE

    expression            → def | assignment
    def                   → 'def' IDENTIFIER ('(' parameters ')')? (':' type)? '=' assignment

    assignment            → (call '.')? IDENTIFIER '=' assignment | control

    control               → if | for | while | until | block | operation
    if                    → 'if' operation 'then' expression ('else' expression)?
    for                   → 'for' IDENTIFIER 'in' operation 'do' expression
    while                 → 'while' operation 'do' expression
    until                 → 'until' operation 'do' expression
    block                 → NEWLINE INDENT line+ DEDENT

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

    type                  → type_union
    type_union            → type_intersection ('&' type_intersection)*
    type_intersection     → type_maybe ('|' type_maybe)*
    type_maybe            → type_generic ('?')?
    type_generic          → IDENTIFIER ('[' type_arguments ']')? | type_grouped
    type_grouped          → '(' type ')'

    parameters            → (IDENTIFIER ':' type (',' IDENTIFIER ':' type)*)?
    arguments             → (assignment (',' assignment)*)?
    type_arguments        → (type (',' type)*)?

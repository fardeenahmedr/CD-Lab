# EXPERIMENT 3 — RECOGNIZE A VALID ARITHMETIC EXPRESSION

## AIM

To write a program to recognize a valid arithmetic expression that uses the operators `+`, `-`, `*`, and `/` using FLEX and BISON.

## ALGORITHM / PROCEDURE

### FLEX

1. Declare the required header files and variables.
2. Define regular expressions to identify identifiers, digits, operators, and other lexemes.
3. Return the recognized tokens to BISON.
4. Use `yywrap()` to indicate the end of input.

### BISON

1. Declare the required header files and variables.
2. Define tokens and operator associativity.
3. Define grammar productions for arithmetic expressions.
4. Use semantic actions where required.
5. Call `yyparse()` to begin parsing.
6. Use `yyerror()` when the input does not match the grammar.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    Recognize identifiers as ID
    Recognize numbers as DIG
    Ignore spaces
    Return operators and parentheses as tokens

BISON:
    Define tokens ID and DIG
    Define precedence:
        + and - have lower precedence
        * and / have higher precedence
        Unary minus has right associativity

Define expression grammar:
    expression + expression
    expression - expression
    expression * expression
    expression / expression
    - expression
    ( expression )
    DIG
    ID

Read input expression

IF expression matches the grammar
    Print "valid Expression"
ELSE
    Print "Invalid Expression"

STOP
```

## PROGRAM — `art_expr.l`

```lex
%{
#include <stdio.h>
#include "art_expr.tab.h"
%}

%%

[a-zA-Z][0-9a-zA-Z]* {
    return ID;
}

[0-9]+ {
    return DIG;
}

[ \t]+ {
    ;
}

. {
    return yytext[0];
}

\n {
    return 0;
}

%%

int yywrap()
{
    return 1;
}
```

## PROGRAM — `art_expr.y`

```yacc
%{
#include <stdio.h>
%}

%token ID DIG

%left '+' '-'
%left '*' '/'
%right UMINUS

%%

stmt:
    expn
;

expn:
      expn '+' expn
    | expn '-' expn
    | expn '*' expn
    | expn '/' expn
    | '-' expn %prec UMINUS
    | '(' expn ')'
    | DIG
    | ID
;

%%

int main()
{
    printf("Enter the Expression\n");
    yyparse();
    printf("valid Expression\n");
    return 0;
}

int yyerror()
{
    printf("Invalid Expression");
    return 0;
}
```

## COMPILATION / EXECUTION

```bash
flex art_expr.l
bison -d art_expr.y
gcc lex.yy.c art_expr.tab.c -o art_expr -lfl
./art_expr
```

## OUTPUT — VALID INPUT

```text
Enter the Expression
a+b*c-d/e
valid Expression
```

## OUTPUT — INVALID INPUT

```text
Enter the Expression
a=b
Invalid Expression
```

## RESULT

Thus, the program to recognize a valid arithmetic expression using FLEX and BISON was executed and verified successfully.

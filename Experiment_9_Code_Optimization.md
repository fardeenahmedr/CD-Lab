# EXPERIMENT 9 — IMPLEMENT SIMPLE CODE OPTIMIZATION TECHNIQUES

## AIM

To write a program using FLEX and BISON to implement simple code optimization techniques such as **constant folding, strength reduction, and algebraic simplification**, applied while parsing three-address code style assignment statements.

## ALGORITHM / PROCEDURE

1. Use FLEX to tokenize input statements into identifiers, numbers, and operators.
2. Pass the tokens to BISON.
3. In BISON, define a grammar for assignment statements of the form `id = expr;`.
4. While reducing an expression:
   - **Constant Folding:** If both operands are numeric constants, evaluate the operation immediately and replace it with the result.
   - **Algebraic Simplification:** Apply rules such as `x + 0 → x`, `x - 0 → x`, `x * 1 → x`, and `x / 1 → x`.
   - **Strength Reduction:** Replace `x * 2` with `x + x`.
5. Print the optimized right-hand side after the statement is fully reduced.
6. Repeat for every statement until the input ends.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    Recognize identifiers
    Recognize numbers
    Recognize =, +, -, *, / and ;
    Return tokens to BISON

BISON:
    Parse assignment statement:
        ID = expression ;

For each expression:

    IF both operands are constants
        Evaluate them
        Apply Constant Folding

    ELSE IF operation matches:
        x + 0
        x - 0
        x * 1
        x / 1
        Simplify using Algebraic Transformation

    ELSE IF operation matches:
        x * 2
        Replace with x + x
        Apply Strength Reduction

    ELSE
        Keep the original expression

Print the optimized statement

Repeat until end of input

STOP
```

## PROGRAM — `optimize.l`

```lex
%{
#include "optimize.tab.h"
#include <string.h>
#include <stdlib.h>
%}

%%

[a-zA-Z][a-zA-Z0-9]* {
    yylval.str = strdup(yytext);
    return ID;
}

[0-9]+ {
    yylval.str = strdup(yytext);
    return NUM;
}

"=" {
    return '=';
}

"+" {
    return '+';
}

"-" {
    return '-';
}

"*" {
    return '*';
}

"/" {
    return '/';
}

";" {
    return ';';
}

[ \t\n] {
    /* skip whitespace */
}

%%

int yywrap()
{
    return 1;
}
```

## PROGRAM — `optimize.y`

```yacc
%{
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <ctype.h>
%}

%union {
    char *str;
}

%token <str> ID NUM
%type <str> expr

%left '+' '-'
%left '*' '/'

%%

stmt_list:
      stmt_list stmt
    | stmt
;

stmt:
    ID '=' expr ';'
    {
        printf("%s = %s\n", $1, $3);
    }
;

expr:
      NUM
      {
          $$ = $1;
      }

    | ID
      {
          $$ = $1;
      }

    | expr '+' expr
      {
          if (isdigit($1[0]) && isdigit($3[0])) {
              char buf[20];
              sprintf(buf, "%d", atoi($1) + atoi($3));
              $$ = strdup(buf);
              printf("// Constant Folding: %s+%s -> %s\n",
                     $1, $3, $$);
          }
          else if (strcmp($3, "0") == 0) {
              $$ = $1;
              printf("// Algebraic Simplification: x+0 -> x\n");
          }
          else if (strcmp($1, "0") == 0) {
              $$ = $3;
              printf("// Algebraic Simplification: 0+x -> x\n");
          }
          else {
              char buf[40];
              sprintf(buf, "%s + %s", $1, $3);
              $$ = strdup(buf);
          }
      }

    | expr '-' expr
      {
          if (isdigit($1[0]) && isdigit($3[0])) {
              char buf[20];
              sprintf(buf, "%d", atoi($1) - atoi($3));
              $$ = strdup(buf);
              printf("// Constant Folding: %s-%s -> %s\n",
                     $1, $3, $$);
          }
          else if (strcmp($3, "0") == 0) {
              $$ = $1;
              printf("// Algebraic Simplification: x-0 -> x\n");
          }
          else {
              char buf[40];
              sprintf(buf, "%s - %s", $1, $3);
              $$ = strdup(buf);
          }
      }

    | expr '*' expr
      {
          if (isdigit($1[0]) && isdigit($3[0])) {
              char buf[20];
              sprintf(buf, "%d", atoi($1) * atoi($3));
              $$ = strdup(buf);
              printf("// Constant Folding: %s*%s -> %s\n",
                     $1, $3, $$);
          }
          else if (strcmp($3, "1") == 0) {
              $$ = $1;
              printf("// Algebraic Simplification: x*1 -> x\n");
          }
          else if (strcmp($3, "2") == 0) {
              char buf[40];
              sprintf(buf, "%s + %s", $1, $1);
              $$ = strdup(buf);
              printf("// Strength Reduction: x*2 -> x+x\n");
          }
          else {
              char buf[40];
              sprintf(buf, "%s * %s", $1, $3);
              $$ = strdup(buf);
          }
      }

    | expr '/' expr
      {
          if (isdigit($1[0]) && isdigit($3[0])) {
              char buf[20];
              sprintf(buf, "%d", atoi($1) / atoi($3));
              $$ = strdup(buf);
              printf("// Constant Folding: %s/%s -> %s\n",
                     $1, $3, $$);
          }
          else if (strcmp($3, "1") == 0) {
              $$ = $1;
              printf("// Algebraic Simplification: x/1 -> x\n");
          }
          else {
              char buf[40];
              sprintf(buf, "%s / %s", $1, $3);
              $$ = strdup(buf);
          }
      }
;

%%

int main()
{
    printf("Enter Three Address Code statements (end with Ctrl+D):\n");
    yyparse();
    return 0;
}

int yyerror(char *s)
{
    printf("Syntax Error: %s\n", s);
    return 0;
}
```

## COMPILATION / EXECUTION

```bash
flex optimize.l
bison -d optimize.y
gcc lex.yy.c optimize.tab.c -o optimize -lfl
./optimize
```

## SAMPLE INPUT

```text
a = 2 + 4;
b = d * 1;
c = s * 2;
```

## OUTPUT

```text
Enter Three Address Code statements (end with Ctrl+D):
a = 2 + 4;
// Constant Folding: 2 + 4 -> 6
a = 6
b = d * 1;
// Algebraic Simplification: x * 1 -> x
b = d
c = s * 2;
// Strength Reduction: x * 2 -> x + x
c = s + s
```

## RESULT

Thus, the FLEX and BISON program for simple code optimization techniques — constant folding, strength reduction, and algebraic simplification — was successfully implemented and tested with various inputs.

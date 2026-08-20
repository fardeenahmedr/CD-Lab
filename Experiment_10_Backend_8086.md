# EXPERIMENT 10 — IMPLEMENT BACK-END OF THE COMPILER USING FLEX AND BISON

## AIM

To write a program using FLEX and BISON to implement the back-end of a compiler which takes three-address code (TAC) as input and generates equivalent 8086 assembly language code.

## ALGORITHM / PROCEDURE

1. Use FLEX to tokenize each TAC line into identifiers and the operators `=`, `+`, `-`, `*`, `/`, and `;`.
2. Pass the tokens to BISON.
3. In BISON, define a grammar for assignment statements of the form `id = expr;`.
4. While reducing an expression:
   - On the first operand, emit `MOV AX, operand`.
   - On `+`, emit `ADD AX, operand`.
   - On `-`, emit `SUB AX, operand`.
   - On `*`, emit `MUL operand`.
   - On `/`, emit `MOV DX, 0`, `MOV BX, operand`, and `DIV BX`.
5. When the complete statement is reduced, emit `MOV result, AX`.
6. Repeat for every TAC statement.
7. Print all generated 8086 instructions.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    Recognize identifiers
    Recognize assignment and arithmetic operators
    Return tokens to BISON

BISON:
    Parse:
        ID = expression ;

For the first operand:
    Generate MOV AX, operand

For addition:
    Generate ADD AX, operand

For subtraction:
    Generate SUB AX, operand

For multiplication:
    Generate MUL operand

For division:
    Generate:
        MOV DX, 0
        MOV BX, operand
        DIV BX

After the entire expression:
    Generate MOV result, AX

Repeat for all TAC statements

STOP
```

## PROGRAM — `backend.l`

```lex
%{
#include "backend.tab.h"
#include <string.h>
#include <stdlib.h>
%}

%%

[a-zA-Z][a-zA-Z0-9]* {
    yylval.str = strdup(yytext);
    return ID;
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

## PROGRAM — `backend.y`

```yacc
%{
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
%}

%union {
    char *str;
}

%token <str> ID
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
        printf("MOV %s, AX\n\n", $1);
    }
;

expr:
      ID
      {
          printf("MOV AX, %s\n", $1);
          $$ = $1;
      }

    | expr '+' ID
      {
          printf("ADD AX, %s\n", $3);
          $$ = $3;
      }

    | expr '-' ID
      {
          printf("SUB AX, %s\n", $3);
          $$ = $3;
      }

    | expr '*' ID
      {
          printf("MUL %s\n", $3);
          $$ = $3;
      }

    | expr '/' ID
      {
          printf("MOV DX, 0\n");
          printf("MOV BX, %s\n", $3);
          printf("DIV BX\n");
          $$ = $3;
      }
;

%%

int main()
{
    printf("Enter TAC statements (end with Ctrl+D):\n");
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
flex backend.l
bison -d backend.y
gcc lex.yy.c backend.tab.c -o backend -lfl
./backend
```

## SAMPLE INPUT

```text
t1 = a + b;
t2 = t1 - c;
t3 = t2 * d;
t4 = t3 / e;
x = t4;
```

## OUTPUT

```text
Enter TAC statements (end with Ctrl+D):

MOV AX, a
ADD AX, b
MOV t1, AX

MOV AX, t1
SUB AX, c
MOV t2, AX

MOV AX, t2
MUL d
MOV t3, AX

MOV AX, t3
MOV DX, 0
MOV BX, e
DIV BX
MOV t4, AX

MOV AX, t4
MOV x, AX
```

## RESULT

Thus, the back-end of the compiler was successfully implemented using FLEX and BISON to translate three-address code into equivalent 8086 assembly language code.

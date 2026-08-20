# EXPERIMENT 8 — IMPLEMENT TYPE CHECKING USING FLEX AND BISON

## AIM

To write a program using FLEX and BISON to implement type checking of variables in simple declarations and expressions, using a symbol table built during parsing.

## ALGORITHM / PROCEDURE

1. Start the program.
2. Use FLEX to tokenize keywords such as `int` and `float`, identifiers, numbers, and operators.
3. Pass the recognized tokens to BISON.
4. In BISON, define grammar rules for declaration statements and assignment statements.
5. On a declaration such as `int a;`, insert the variable name and type into the symbol table.
6. On an assignment such as `a = b + c;`, look up the types of the result variable and operands.
7. If a variable is not found in the symbol table, report it as undefined.
8. If all types match, print `No type mismatch`; otherwise print `Type mismatch`.
9. End the program.

## PSEUDOCODE / LOGIC

```text
START

Create an empty symbol table

FLEX:
    Recognize INT
    Recognize FLOAT
    Recognize identifiers
    Recognize numbers
    Recognize assignment and arithmetic operators
    Return tokens to BISON

BISON:
    For declaration:
        Insert variable name and type into symbol table

    For assignment:
        Find the type of the left-hand variable
        Find the type of each operand

        IF a variable is undefined
            Print "Undefined variable"
        ELSE IF all required types match
            Print "No type mismatch"
        ELSE
            Print "Type mismatch"

STOP
```

## PROGRAM — `typecheck.l`

```lex
%{
#include "typecheck.tab.h"
#include <string.h>
#include <stdlib.h>
%}

%%

"int" {
    return INT;
}

"float" {
    return FLOAT;
}

[a-zA-Z_][a-zA-Z0-9_]* {
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

## PROGRAM — `typecheck.y`

```yacc
%{
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

struct sym {
    char name[20];
    char type[10];
} table[50];

int n = 0;

void insert(char *name, char *type)
{
    strcpy(table[n].name, name);
    strcpy(table[n].type, type);
    n++;
}

char* typeOf(char *name)
{
    int i;

    for (i = 0; i < n; i++)
        if (strcmp(table[i].name, name) == 0)
            return table[i].type;

    return "undefined";
}
%}

%union {
    char *str;
}

%token <str> ID NUM
%token INT FLOAT
%type <str> expr

%%

program:
    stmts
;

stmts:
      stmts stmt
    | stmt
;

stmt:
      decl
    | assign
;

decl:
      INT ID ';'
      {
          insert($2, "int");
      }

    | FLOAT ID ';'
      {
          insert($2, "float");
      }
;

assign:
    ID '=' expr ';'
    {
        char *lt = typeOf($1);

        if (strcmp(lt, "undefined") == 0)
            printf("Undefined variable: %s\n", $1);

        else if (strcmp(lt, $3) == 0)
            printf("No type mismatch in expression: %s = ...\n", $1);

        else
            printf("Type mismatch in assignment to %s\n", $1);
    }
;

expr:
      ID
      {
          char *t = typeOf($1);

          if (strcmp(t, "undefined") == 0)
              printf("Undefined variable: %s\n", $1);

          $$ = t;
      }

    | NUM
      {
          $$ = "int";
      }

    | expr '+' expr
      {
          $$ = (strcmp($1, $3) == 0) ? $1 : "mismatch";
      }

    | expr '-' expr
      {
          $$ = (strcmp($1, $3) == 0) ? $1 : "mismatch";
      }

    | expr '*' expr
      {
          $$ = (strcmp($1, $3) == 0) ? $1 : "mismatch";
      }

    | expr '/' expr
      {
          $$ = (strcmp($1, $3) == 0) ? $1 : "mismatch";
      }
;

%%

int main()
{
    printf("Enter declarations and expressions:\n");
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
flex typecheck.l
bison -d typecheck.y
gcc lex.yy.c typecheck.tab.c -o typecheck -lfl
./typecheck
```

## OUTPUT — CORRECT TYPES

```text
Enter declarations and expressions:
int a;
int b;
int c;
a = b * c;
No type mismatch in expression: a = ...
```

## OUTPUT — TYPE MISMATCH

```text
Enter declarations and expressions:
int a;
float b;
int c;
a = b + c;
Type mismatch in assignment to a
```

## RESULT

Thus, the FLEX and BISON program for type checking was successfully implemented. The program builds a symbol table from declarations and checks type consistency in assignment expressions.

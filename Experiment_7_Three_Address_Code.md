# EXPERIMENT 7 — GENERATE THREE ADDRESS CODE USING FLEX AND BISON

## AIM

To write a program using FLEX and BISON to generate three-address code (TAC) for a simple arithmetic expression.

## ALGORITHM / PROCEDURE

### FLEX

1. Include the required headers.
2. Define tokens for identifiers, numbers, and operators.
3. Use regular expressions to identify identifiers and numeric constants.
4. Return appropriate tokens to BISON.

### BISON

1. Declare tokens and define associativity for operators.
2. Use grammar rules to parse arithmetic expressions such as `a = b + c * d`.
3. Generate three-address code during the parsing actions.
4. Maintain a temporary variable counter to represent intermediate results such as `t1`, `t2`, etc.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    Recognize identifiers
    Recognize numeric constants
    Ignore spaces and newlines
    Return operators as tokens

BISON:
    Define ID and NUM tokens
    Define operator precedence
    Parse assignment statements

For each arithmetic operation:
    Create a new temporary variable
    Generate:
        t = operand1 operator operand2
    Return the temporary variable as the expression result

For assignment:
    Generate:
        identifier = final_expression

STOP
```

## PROGRAM — `tac.l`

```lex
%{
#include "tac.tab.h"
#include <string.h>
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

[\t\n ]+ {
    /* skip spaces */
}

. {
    return yytext[0];
}

%%

int yywrap()
{
    return 1;
}
```

## PROGRAM — `tac.y`

```yacc
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int tempCount = 1;
char temp[10];
%}

%union {
    char *str;
}

%token <str> ID NUM
%type <str> expr

%left '+' '-'
%left '*' '/'

%%

stmt:
    ID '=' expr
    {
        printf("%s = %s\n", $1, $3);
    }
;

expr:
      expr '+' expr
    {
        sprintf(temp, "t%d", tempCount++);
        printf("%s = %s + %s\n", temp, $1, $3);
        $$ = strdup(temp);
    }

    | expr '-' expr
    {
        sprintf(temp, "t%d", tempCount++);
        printf("%s = %s - %s\n", temp, $1, $3);
        $$ = strdup(temp);
    }

    | expr '*' expr
    {
        sprintf(temp, "t%d", tempCount++);
        printf("%s = %s * %s\n", temp, $1, $3);
        $$ = strdup(temp);
    }

    | expr '/' expr
    {
        sprintf(temp, "t%d", tempCount++);
        printf("%s = %s / %s\n", temp, $1, $3);
        $$ = strdup(temp);
    }

    | ID
    {
        $$ = $1;
    }

    | NUM
    {
        $$ = $1;
    }
;

%%

int main()
{
    printf("Enter the expression:\n");
    yyparse();
    return 0;
}

int yyerror(char *s)
{
    printf("Error: %s\n", s);
    return 0;
}
```

## COMPILATION / EXECUTION

```bash
flex tac.l
bison -d tac.y
gcc tac.tab.c lex.yy.c -o tac -lfl
./tac
```

## SAMPLE INPUT

```text
a = b + c * d
```

## OUTPUT

```text
Enter the expression:
a = b + c * d
t1 = c * d
t2 = b + t1
a = t2
```

## RESULT

Thus, the program to generate three-address code using FLEX and BISON was executed and verified successfully.

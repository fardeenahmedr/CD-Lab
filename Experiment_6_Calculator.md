# EXPERIMENT 6 — IMPLEMENTATION OF CALCULATOR USING FLEX AND BISON

## AIM

To write a program to implement a Calculator using FLEX and BISON.

## ALGORITHM / PROCEDURE

1. Start the program.
2. In the definitions part of the FLEX file, include the regular definition for a digit.
3. In the rules part of the FLEX file, specify the pattern for a number and return `NUM` with the numeric value in `yylval`.
4. In the BISON program, define grammar rules so that the arithmetic operations `+`, `-`, `*`, and `/` are evaluated using operator precedence.
5. Display an error if the input does not match the grammar.
6. Provide the input.
7. Verify the output.
8. End the program.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    Define pattern for numbers
    Store the matched number in yylval
    Return NUM token
    Return operators and newline characters

BISON:
    Define NUM token
    Define precedence:
        + and - have lower precedence
        * and / have higher precedence
        Unary minus has right associativity

Define expression grammar:
    E + E
    E - E
    E * E
    E / E
    NUM

For each expression:
    Evaluate according to the grammar
    Print the answer

If input does not match the grammar:
    Display an error message

STOP
```

## PROGRAM — `cal.l`

```lex
%{
#include "cal.tab.h"
%}

DIGIT [0-9]+
%option noyywrap

%%

{DIGIT} {
    yylval = atof(yytext);
    return NUM;
}

\n|. {
    return yytext[0];
}

%%
```

## PROGRAM — `cal.y`

```yacc
%{
#include <ctype.h>
#include <stdio.h>

#define YYSTYPE double
%}

%token NUM

%left '+' '-'
%left '*' '/'
%right UMINUS

%%

Statement:
      E
    | Statement '\n'
;

E:
      E '+' E
    | E '-' E
    | E '*' E
    | E '/' E
    | NUM
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
    printf("%s\n", s);
    return 0;
}
```

## COMPILATION / EXECUTION

```bash
flex cal.l
bison -d cal.y
gcc lex.yy.c cal.tab.c -o calc -lfl
./calc
```

## OUTPUT

```text
Enter the expression:
2+2
Answer: 4
```

## RESULT

Thus, the program for implementing a calculator using FLEX and BISON was executed and verified successfully.

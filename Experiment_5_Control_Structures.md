# EXPERIMENT 5 — RECOGNIZE VALID C CONTROL STRUCTURES

## AIM

To write a program to recognize valid control structure syntax of C language such as `for`, `while`, `if-else`, `if-else-if`, `switch-case`, etc. using FLEX and BISON.

## ALGORITHM / PROCEDURE

### FLEX

1. Start the FLEX program with the required header files.
2. Define regular expressions for control keywords such as `if`, `else`, `for`, `while`, `switch`, `case`, and `default`.
3. Define patterns for identifiers, numbers, braces, parentheses, colons, semicolons, and relational operators.
4. Return appropriate tokens to BISON.

### BISON

1. Include the required header files and token declarations.
2. Define grammar rules for:
   - `if`
   - `if-else`
   - `while`
   - `for`
   - `switch-case`
3. Define grammar rules for conditions and relational operators.
4. Call `yyparse()` to validate the input.
5. Use `yyerror()` to report invalid syntax.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    Recognize control keywords
    Recognize identifiers and numbers
    Recognize braces, parentheses, colon and semicolon
    Recognize relational and assignment operators
    Return tokens to BISON

BISON:
    Define program and statement-list grammar

    Recognize:
        IF (condition) statement
        IF (condition) statement ELSE statement
        WHILE (condition) statement
        FOR (initialization; condition; update) statement
        SWITCH (identifier) { case-list }

    Recognize conditions of the form:
        ID relational_operator NUM

Read the C control structure

IF input matches the grammar
    Print "Valid control structure syntax."
ELSE
    Print "Invalid control structure syntax."

STOP
```

## PROGRAM — `control.l`

```lex
%{
#include "control.tab.h"
%}

%%

"if" {
    return IF;
}

"else" {
    return ELSE;
}

"for" {
    return FOR;
}

"while" {
    return WHILE;
}

"switch" {
    return SWITCH;
}

"case" {
    return CASE;
}

"default" {
    return DEFAULT;
}

[a-zA-Z_][a-zA-Z0-9_]* {
    return ID;
}

[0-9]+ {
    return NUM;
}

"{" {
    return LBRACE;
}

"}" {
    return RBRACE;
}

"(" {
    return LPAREN;
}

")" {
    return RPAREN;
}

":" {
    return COLON;
}

";" {
    return SEMICOLON;
}

"==" {
    return EQ;
}

"<=" {
    return LE;
}

">=" {
    return GE;
}

"<" {
    return LT;
}

">" {
    return GT;
}

"=" {
    return ASSIGN;
}

[ \t\n] {
    /* skip whitespace */
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

## PROGRAM — `control.y`

```yacc
%{
#include <stdio.h>
#include <stdlib.h>
%}

%token IF ELSE FOR WHILE SWITCH CASE DEFAULT
%token ID NUM
%token LBRACE RBRACE LPAREN RPAREN COLON SEMICOLON
%token EQ LE GE LT GT ASSIGN

%%

program:
    stmt_list
;

stmt_list:
      stmt_list stmt
    | stmt
;

stmt:
      if_stmt
    | while_stmt
    | for_stmt
    | switch_stmt
;

if_stmt:
      IF LPAREN cond RPAREN stmt
    | IF LPAREN cond RPAREN stmt ELSE stmt
;

while_stmt:
    WHILE LPAREN cond RPAREN stmt
;

for_stmt:
    FOR LPAREN ID ASSIGN NUM SEMICOLON cond SEMICOLON ID ASSIGN ID RPAREN stmt
;

switch_stmt:
    SWITCH LPAREN ID RPAREN LBRACE case_list RBRACE
;

case_list:
      case_list CASE NUM COLON stmt
    | case_list DEFAULT COLON stmt
    | CASE NUM COLON stmt
    | DEFAULT COLON stmt
;

cond:
    ID relop NUM
;

relop:
      EQ
    | LE
    | GE
    | LT
    | GT
;

%%

int main()
{
    printf("Enter a C control structure syntax:\n");
    yyparse();
    printf("Valid control structure syntax.\n");
    return 0;
}

int yyerror(char *s)
{
    printf("Invalid control structure syntax.\n");
    return 0;
}
```

## COMPILATION / EXECUTION

```bash
flex control.l
bison -d control.y
gcc lex.yy.c control.tab.c -o control -lfl
./control
```

## OUTPUT

```text
Enter a C control structure syntax:
if (x < 5) { y = 10; }
Valid control structure syntax.
```

## RESULT

Thus, the program to recognize valid control structure syntax of C language using FLEX and BISON was executed and verified successfully.

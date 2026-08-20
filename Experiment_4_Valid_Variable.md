# EXPERIMENT 4 — RECOGNIZE A VALID VARIABLE

## AIM

To write a program to recognize a valid variable which starts with a letter followed by any number of letters or digits using FLEX and BISON.

## ALGORITHM / PROCEDURE

### FLEX

1. Declare the required header files.
2. Define regular expressions for letters and digits.
3. Return the corresponding tokens `LET` and `DIG`.
4. Use `yywrap()` to indicate the end of input.

### BISON

1. Declare the required header files.
2. Define the tokens `LET` and `DIG`.
3. Define grammar rules for a valid variable.
4. A variable must begin with a letter.
5. After the first letter, any number of letters or digits may follow.
6. Call `yyparse()` to validate the input.
7. Use `yyerror()` for invalid input.

## PSEUDOCODE / LOGIC

```text
START

FLEX:
    If character is a letter, return LET
    If character is a digit, return DIG
    On newline, end the input

BISON:
    Define:
        variable → var
        var → var DIG
        var → var LET
        var → LET

Read the variable

IF the first character is a letter
   AND all remaining characters are letters or digits
    Print "Valid variable"
ELSE
    Print "Invalid variable"

STOP
```

## PROGRAM — `valvar.l`

```lex
%{
#include "valvar.tab.h"
%}

%%

[a-zA-Z] {
    return LET;
}

[0-9] {
    return DIG;
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

## PROGRAM — `valvar.y`

```yacc
%{
#include <stdio.h>
%}

%token LET DIG

%%

variable:
    var
;

var:
      var DIG
    | var LET
    | LET
;

%%

int main()
{
    printf("Enter the variable:\n");
    yyparse();
    printf("Valid variable\n");
    return 0;
}

int yyerror()
{
    printf("Invalid variable\n");
    return 0;
}
```

## COMPILATION / EXECUTION

```bash
flex valvar.l
bison -d valvar.y
gcc lex.yy.c valvar.tab.c -o valvar -lfl
./valvar
```

## OUTPUT — VALID INPUT

```text
Enter the variable:
add
Valid variable
```

```text
Enter the variable:
add1
Valid variable
```

## OUTPUT — INVALID INPUT

```text
Enter the variable:
1add
Invalid variable
```

## RESULT

Thus, the program to recognize a valid variable which starts with a letter followed by any number of letters or digits using FLEX and BISON was executed and verified successfully.

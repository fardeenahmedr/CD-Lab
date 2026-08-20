# EXPERIMENT 2 — IMPLEMENT A LEXICAL ANALYZER USING FLEX

## AIM

To create a program that reads a C source code file and identifies individual tokens such as identifiers, keywords, constants, operators, preprocessor directives, header files, and delimiters using FLEX.

## ALGORITHM / PROCEDURE

1. Start by defining patterns using regular expressions for each type of token.
2. Create a FLEX file with three sections:
   - Definitions
   - Rules
   - C code
3. Define patterns for keywords, identifiers, numbers, operators, preprocessor directives, header files, and delimiters.
4. In the rules section, associate each pattern with an action to print the token type.
5. Compile the FLEX program using `flex` and `gcc`.
6. Run the generated executable with a C source file as input.
7. Scan the input and print each recognized token.
8. Stop after all tokens are processed.

## PSEUDOCODE / LOGIC

```text
START

Define patterns for:
    Keywords
    Identifiers
    Numbers
    Operators
    Preprocessor directives
    Header files
    Delimiters

Read the C source file

For each lexeme:
    IF it matches a preprocessor directive
        Print "Preprocessor Directive"
    ELSE IF it matches a header file
        Print "Header File"
    ELSE IF it matches a keyword
        Print "Keyword"
    ELSE IF it matches an identifier
        Print "Identifier"
    ELSE IF it matches a number
        Print "Number"
    ELSE IF it matches an operator
        Print "Operator"
    ELSE IF it matches a delimiter
        Print "Delimiter"
    ELSE
        Ignore the character

Continue until end of file

STOP
```

## PROGRAM — `lexer.l`

```lex
%{
#include <stdio.h>
%}

KEYWORD int|float|char|double|void|for|while|if|else|return|struct|switch|case|break|do

%%

"#include" {
    printf("Preprocessor Directive : %s\n", yytext);
}

"<"[a-zA-Z.]+">" {
    printf("Header File : %s\n", yytext);
}

{KEYWORD} {
    printf("Keyword : %s\n", yytext);
}

[a-zA-Z_][a-zA-Z0-9_]* {
    printf("Identifier : %s\n", yytext);
}

[0-9]+ {
    printf("Number : %s\n", yytext);
}

"=="|"<="|">=" {
    printf("Operator : %s\n", yytext);
}

"+"|"-"|"*"|"/"|"="|"<"|">" {
    printf("Operator : %s\n", yytext);
}

[(){};,] {
    printf("Delimiter : %s\n", yytext);
}

[ \t\n] {
    /* skip whitespace */
}

. {
    /* ignore rest */
}

%%

int yywrap()
{
    return 1;
}

int main(int argc, char *argv[])
{
    if (argc < 2) {
        printf("Usage: %s <input file>\n", argv[0]);
        return 1;
    }

    yyin = fopen(argv[1], "r");

    yylex();

    printf("\nEnd of file\n");
    return 0;
}
```

## SAMPLE INPUT — `iplex.c`

```c
#include<stdio.h>
void main()
{
    int x;
    x = 10;
}
```

## COMPILATION / EXECUTION

```bash
flex lexer.l
gcc lex.yy.c -o lexer -lfl
./lexer iplex.c
```

## OUTPUT

```text
Preprocessor Directive : #include
Header File : <stdio.h>
Keyword : void
Identifier : main
Delimiter : (
Delimiter : )
Delimiter : {
Keyword : int
Identifier : x
Delimiter : ;
Identifier : x
Operator : =
Number : 10
Delimiter : ;
Delimiter : }
End of file
```

## RESULT

Thus, the FLEX program for implementation of a lexical analyzer was executed and verified successfully.

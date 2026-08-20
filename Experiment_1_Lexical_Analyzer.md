# EXPERIMENT 1 — LEXICAL ANALYZER USING FLEX

## AIM

To develop a lexical analyzer using FLEX to recognize tokens such as **identifiers, constants, comments, and operators** in a C program and to create a **symbol table** while recognizing identifiers.

## ALGORITHM / PROCEDURE

1. Start the FLEX program by including the required header files.
2. Define regular expressions for:
   - **Identifier:** `[a-zA-Z_][a-zA-Z0-9_]*`
   - **Constant:** `[0-9]+`
   - **Comments:** `//.*` and `/* ... */`
   - **Operators:** `+ - * / = < >`
3. Declare a symbol table containing the identifier name and its type.
4. Define `lookup()` to check whether an identifier is already present in the symbol table.
5. Define `insert()` to add a new identifier if it is not already present.
6. Write FLEX rules to recognize and print identifiers, constants, comments, and operators.
7. When an identifier is recognized, call `insert()` to store it in the symbol table.
8. In `main()`, open the input C file and call `yylex()`.
9. After scanning the file, display the symbol table.
10. Compile and execute the program using FLEX and GCC.

## PSEUDOCODE / LOGIC

```text
START

Initialize symbol table
Set symbol count = 0

FUNCTION lookup(identifier):
    FOR each entry in symbol table
        IF identifier matches entry
            RETURN its position
    RETURN -1

FUNCTION insert(identifier):
    IF lookup(identifier) == -1
        Add identifier to symbol table
        Set its type
        Increment symbol count

Define FLEX patterns:
    Identifier → [a-zA-Z_][a-zA-Z0-9_]*
    Constant   → [0-9]+
    Comment    → //.* or /* ... */
    Operator   → + - * / = < >

For every input lexeme:
    IF comment
        Print "Comment"
    ELSE IF identifier
        Insert identifier into symbol table
        Print "Identifier"
    ELSE IF constant
        Print "Constant"
    ELSE IF operator
        Print "Operator"
    ELSE
        Ignore whitespace/other characters

After scanning:
    Print symbol table

STOP
```

## PROGRAM — `symtab.l`

```lex
%{
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

struct symtab {
    char name[30];
    int type;
} symtab[100];

int sc = 0;

int lookup(char *s)
{
    int i;

    for (i = 0; i < sc; i++)
        if (strcmp(symtab[i].name, s) == 0)
            return i;

    return -1;
}

void insert(char *s)
{
    if (lookup(s) == -1) {
        strcpy(symtab[sc].name, s);
        symtab[sc].type = 1;
        sc++;
    }
}
%}

DIGIT [0-9]
ID [a-zA-Z_][a-zA-Z0-9_]*

%%

"/*"([^*]|\*+[^*/])*\*+"/" {
    printf("Comment : %s\n", yytext);
}

"//".* {
    printf("Comment : %s\n", yytext);
}

{ID} {
    insert(yytext);
    printf("Identifier : %s\n", yytext);
}

{DIGIT}+ {
    printf("Constant : %s\n", yytext);
}

"+"|"-"|"*"|"/"|"="|"<"|">" {
    printf("Operator : %s\n", yytext);
}

[ \t\n] {
    /* skip whitespace */
}

. {
    /* ignore other characters */
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

    if (!yyin) {
        printf("Cannot open file %s\n", argv[1]);
        return 1;
    }

    yylex();

    printf("\nSYMBOL TABLE\n");
    printf("S.No\tName\n");

    int i;
    for (i = 0; i < sc; i++)
        printf("%d\t%s\n", i + 1, symtab[i].name);

    fclose(yyin);
    return 0;
}
```

## SAMPLE INPUT — `input.c`

```c
int a = 10; // sum variable
b = a + 5;
```

## COMPILATION / EXECUTION

```bash
flex symtab.l
gcc lex.yy.c -o symtab -lfl
./symtab input.c
```

## OUTPUT

```text
Comment : // sum variable
Identifier : int
Identifier : a
Constant : 10
Operator : =
Identifier : b
Identifier : a
Operator : +
Constant : 5

SYMBOL TABLE
S.No    Name
1       int
2       a
3       b
```

## RESULT

Thus, the FLEX program to develop a lexical analyzer for recognizing identifiers, constants, comments, and operators and to build a symbol table was executed and verified successfully.

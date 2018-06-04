# Brainfuck Implementing Challenge

## Goal

Implement Brainfuck language interpreter. Brainfuck is an esoteric language with just eight commands. Each command consist of one character.

| Character | Action |
|-----------|--------|
| >         | increment data pointer so that it points next location in memory       |
| <         | decrement data pointer so that it points prev location in memory       |
| +         | increment byte at data pointer by 1. If it is already at its maximum value, 255, then new value will be 0.        |
| -         | Decrement the byte pointed by data pointer by 1. If it is at its minimum value, 0, then new value will be 255.       |
| .         | Output the character represented by the byte at the data pointer.       |
| ,         | Read one byte and store it at the memory location pointed by data pointer.       |
| [         |  If the byte pointed by data pointer is zero, then move instruction pointer to next matching ']', otherwise move instruction pointer to next command.       |
| ]         | If the byte pointed by data pointer is non-zero, then move instruction pointer to previous matching '[' command, otherwise to next command.       |

## Implementation note

1. Initally all memory locations contain 0. A location can store integer in range [0 .. 255].
2. At the start of program, data pointer is at memory location 0. It is guaranteed that data pointer will never point to a negative memory index during the execution of program.
3. Program will not have a mis-matched bracket ([ or ]).

Despite language is very simple and can be implemented as stream processing text you should make it like real world interpreters.

Interpreter has several components:

1. Tokenizer. It makes a lot of dirty job like space skipping, deleting comments, keywords, and variable names recognition. On input, we put plain text, on the output we get a list of tokens. It has no structure, just long list. Example: for input Scala tokenizer `val i = 5` we can get something like `List(VAL_KEYWORD, VAL_NAME("i"), ASSIGNMENT, NUMBER(5))`

2. Parser. It gets a list of tokens and produces Abstract Syntax Tree (AST). It's tree-like structure that group tokens in expressions, make nesting and so on. It's useful when working with non-linear code with `if-then-else`, blocks, loops (and nesting loops). Example: 

```javascript
while (a < 5) { // condition 1
    while (b > 2) { // condition 2
        ... some code // inner body 2
    }
    // body 1
}
```

with definition of Loop AST Node as

```scala
class Loop(condition: Expression, body: List[Expression])
```
become
```scala
Loop(condition1, List(Loop(condition2, innerBody), body1))
```

3. Virtual Machine (VM). It makes abstraction over real hardware and environment. VM control what program can make and what can't. VM has own instruction set (bytecode), manage memory, permissions and so on.

4. Byte-code generator. It traverses AST and generates instructions for VM. The generator can make work on the fly and so it will be named interpreter. Or it can create files for VM or even native/LLVM instructions. In this case, it will be named compiler.

## General technical notes

1. Write tests. It's normal that size of code for tests and regular code are equals.
2. Write tests first (TDD). Firstly describe the design in tests, then write the implementation.
3. Implement component one by one, don't try to solve challenge from the ground to fly in one big step. Write component 1, test it, write component 2, make design changes in component 1 if needed.

## Program examples

### Hello, world

```brainfuck
+++++ +++++             initialize counter (cell #0) to 10
[                       use loop to set the next four cells to 70/100/30/10
    > +++++ ++              add  7 to cell #1
    > +++++ +++++           add 10 to cell #2
    > +++                   add  3 to cell #3
    > +                     add  1 to cell #4
    <<<< -                  decrement counter (cell #0)
]
> ++ .                  print 'H'
> + .                   print 'e'
+++++ ++ .              print 'l'
.                       print 'l'
+++ .                   print 'o'
> ++ .                  print ' '
<< +++++ +++++ +++++ .  print 'W'
> .                     print 'o'
+++ .                   print 'r'
----- - .               print 'l'
----- --- .             print 'd'
> + .                   print '!'
```

### Read the input

```brainfuck
,+. This program will 6 characters
,+. For first 3 characters it will
,+. print its successor
,-. For last 3 characters it will
,-. print its predecessor
,-.
```

on input `abcxyz` produces `bcdwxy` on output
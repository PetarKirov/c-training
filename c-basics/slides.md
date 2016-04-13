# Basics of programming in C

1. Introduction
2. Overview of the C syntax
3. The C compilation model
4. Platform abstraction
5. the c standard library
6. Overview of development tools

---

## 1. Introduction
* Writing, compiling and debugging programs
* Program structure and organization
* Execution environment
* Program correctness and ways of ensuring it

----

### Introduction

C is a staticly-typed, weakly-checked general-purpose, imperative (procedural) programming language.
It was designed to:
+ be relatively straightforward to compile
+ provide low-level access to memory
+ provide language constructs that map efficiently to machine instructions
+ require minimal run-time support

----

### History

> C was originally developed by Dennis Ritchie between 1969 and 1973 at Bell Labs,[5] and used to re-implement the Unix operating system


----

### Program structure and organization

+ A C program or library is made of one more files (a.k.a.
  compilation/translation units) compiled and linked together.
+ The convention is to separate them into two categories: headers `*.h` and
  source `*.c` files.
  + Usually each source file has a corresponding header file, but it's not
    always the case.
  + Header files contain **declarations** that present an interface to
    the functionality.
  + Source files contain function **definitions** that implement the interface.
+ `*.h` files are *included* into `*.c` files and `*.c` are *compiled* to `*.o` files. Finally `*.o` files are linked into a library or executable.

----

Most non-trivial projects are split into multiple files. This has a number of
advantages:
+ Increaseed compilations speed (incremental compilation)
+ Better orgranization
+ Code reuse
+ Share code between projects
+ Split development and maintenance responsibilities among programmers

----

### Writing, compiling and debugging programs

----

### Execution environment

> The components that are used together with the application’s code to
make the complete system: the processors, networks, operating systems and so on

----

### Software quality assurance

Software quality assurance (SQA) consists of a means of monitoring the
software engineering processes and methods used to ensure quality.

Software quality assurance encompasses the entire software development process:
+ Requirements analysis and specification
+ Software design
+ Implementation
+ Source code control
+ Code reviews
+ Software configuration management
+ Testing
+ Release management
+ Product integration.

----

#### Software design concepts

+ Abstraction
+ Refinement
+ Modularity

+ Software Architecture
+ Control Hierarchy
+ Data Structure
+ Encapsulation

----

#### Software design considerations (1)

+ Compatibility - The software is able to operate with other products that are designed for interoperability with another product. For example, a piece of software may be backward-compatible with an older version of itself.
+ Extensibility - New capabilities can be added to the software without major changes to the underlying architecture.
+ Fault-tolerance - The software is resistant to and able to recover from component failure.
+ Maintainability - A measure of how easily bug fixes or functional modifications can be accomplished. High maintainability can be the product of modularity and extensibility.
+ Modularity - the resulting software comprises well defined, independent components which leads to better maintainability. The components could be then implemented and tested in isolation before being integrated to form a desired software system. This allows division of work in a software development project.
+ Reliability - The software is able to perform a required function under stated conditions for a specified period of time.

----

#### Software design considerations (2)

+ Reusability - parts or all of the software can be used in other projects with no, or only slight, modification.
+ Robustness - The software is able to operate under stress or tolerate unpredictable or invalid input. For example, it can be designed with a resilience to low memory conditions.
+ Security - The software is able to withstand hostile acts and influences.
+ Usability - The software user interface must be usable for its target user/audience. Default values for the parameters must be chosen so that they are a good choice for the majority of the users.[5]
+ Performance - The software performs its tasks within a user-acceptable time. The software does not consume too much memory.
+ Portability - The usability of the same software in different environments.
+ Scalability - The software adapts well to increasing data or number of users.

----

####

+ 



---

## 2. Overview of the C syntax

### Scopes of identifiers

+ An identifier can denote :
  + an object
  + a function
  + a tag or a member of a structure, union, or enumeration
  + a typedef name
  + a label name
  + a macro name
  + or a macro parameter.
+ The same identifier can denote different entities at different points in the program.
+ For each different entity that an identifier designates, the identifier is
  visible (i.e., can be used) only within a region of program text called its **scope**.
+ Different entities designated by the same identifier either have
  **different scopes**, or **are in different name spaces**.
+ There are four kinds of scopes: function, file, block, and function prototype.
+ Every identifier, except label names, has scope determined by the placement
  of its declaration (in a declarator or type specifier).

----

### Scopes of identifiers - Labels

A label name is the only kind of identifier that has <u>function scope</u>.

```
wrong_label: /* error: Labels can't appear outside functions! */

void fun(void)
{
    int* arr;

    arr = malloc(16);

    if (cond1)
        doWork1();
    else
        goto cleanup;

    if (cond2)
        doWork2();
    else
        goto cleanup;

    /* ... */

cleanup:  /*  declared implicitly by its syntactic appearance */
   free()
}
```

----

### Scopes of identifiers - File scope

+ The identifier has **file scope**, if the declarator or type specifier that
  declares the identifier appears outside of <u>any block</u> or <u>list of parameters</u>.
+ This scope terminates at the end of the translation unit.

```C
/* somelib.h */

int global; /* Has file scope */

```

```C
/* main.c */

#include "somelib.h"

int bar(int x)
{
   /* global from somelib.h is visible here */

   /* ... */
}

/* global from somelib.h is also visible here and till the end of the file */

/* end of main.c file*/
```


----

### Linkages of identifiers

+ An identifier declared in different scopes or in the same scope more than
  once can be made to refer to the same object or function by a process
  called **linkage**. There are three kinds of linkage: **external**,
  **internal**, and **none**.

+ <u>In the set of translation units and libraries that constitutes an entire
  program</u>, each declaration of a particular identifier with **external linkage**
  denotes <u>the same</u> object or function.
+ <u>Within one translation unit</u>, each declaration of an identifier with **internal
  linkage** denotes <u>the same</u> object or function.
+ <u>Each declaration</u> of an identifier with **no linkage** denotes <u>a unique</u> entity.

----

+ Declaring and defining functions, structures, unions, bitfields, enums and typedefs.
+ Built-in types. Variables, constants and storage classes.

----

+ Expressions and control flow.

----

Control flow - Selection statements

A selection statement selects among a set of statements depending on the value of a
controlling expression.

+ Syntax
  + `if` ( expression ) statement
  + `if` ( expression ) statement `else` statement
  + `switch` ( expression ) statement


----

Control flow - Iteration statements (1)

An iteration statement causes a statement called the **loop body** to be executed repeatedly
until the **controlling expression** compares equal to `0`.

+ Syntax
    + `while ( expression ) statement` - evaluate the control expression before the loop body statement.
    + `do statement while ( expression ) ;` - evaluate the control expression after loop body statement.
    + `for ( [clause-1] ; [expression-2] ; [expression-3] ) statement`
       + `clause-1` it is evaluated before the first evaluation of the controlling expression.
       + `expression-2` is evaluated before each execution of the loop body
       + `expression-3` is evaluated after each execution of the loop body.       
       + If `clause-1` is an expression, it is evaluated before the first
         evaluation of the controlling expression.
       + Else, if `clause-1` is a declaration the scope of any identifiers it declares
         is the remainder of the declaration and the entire loop.
       + If `expression-2` is omitted, it is replaced by a nonzero constant.

----

Control flow - Iteration statements (2)

+ The controlling expression of an iteration statement shall have <u>scalar type</u>.
+ The declaration part of a `for` statement shall only declare identifiers for objects having
  storage class **auto** or **register**.
+ Repetition occurs regardless of whether the loop body is entered from the
  iteration statement or by a jump.

----

Control flow - Jump statements

 A jump statement causes an unconditional jump to another place.

+ Syntax
  + `goto identifier ;`
  + `continue ;`
  + `break ;`
  + `return expressionopt ;`

----

Control flow - Jump statements - `goto`

A goto statement causes an unconditional jump to the statement prefixed by the named
label in the enclosing function.

+ The identifier in a `goto` statement shall name a label located somewhere in
  the enclosing function.
+ A `goto` statement shall not jump from outside the scope of an identifier
  having a variably modified type to inside the scope of that identifier.

----

Control flow - Jump statements - `continue`

A continue statement causes a jump to the loop-continuation portion of the smallest
enclosing iteration statement; that is, to the end of the loop body

+  A continue statement shall appear only in or as a loop body.

```
while (/* ... */)
{
    /* ... */

    continue; /* equivalent to goto contin; */

    /* ... */

    contin: ;
}
```

----

Control flow - Jump statements - `break`

A `break` statement terminates execution of the smallest enclosing `switch` or iteration
statement.

+ A break statement shall appear only in or as a switch body or loop body.

----

Control flow - Jump statements - `return`

A return statement terminates execution of the current function and returns control to
its caller. A function may have any number of return statements.

+ A `return` statement without an expression shall only appear in a function
  whose return type is `void`.
+ A `return` statement with an expression shall not appear in a function whose
  return type is `void`.
+ If a return statement with an expression is executed, the value of the expression is
returned to the caller as the value of the function call expression.
+ If the expression has a type different from the return type of the function
  in which it appears, the value is converted as if by assignment to an object
  having the return type of the function.



----
+ Memory addressing. Pointers, arrays and strings.

----

### Strings

 A string is a contiguous sequence of characters terminated by and including the first null
character.

A **pointer to a string** is a pointer to its initial (lowest addressed)
character.

The **length of a string** is the number of bytes preceding the null character.
The **value of a string** is the sequence of the values of the contained
characters, in order.

----


+ Macros.
+ Differences with C++ (and Java/C#).


---

## 3. The C compilation model
* Compilation pipeline (preprocessor -> compiler -> linker).
* Headers (.h) vs source (.c) files.
* Pre-processor (includes, ifdefs, etc.).
* Organizing reusable code into libraries
* Using Make for building projects.

----

### Make - Introduction (1)

+ Make is a **build automation tool** that automatically builds executable programs and libraries from source code by reading files called **Makefiles** which specify how to derive the target program.

+ Make can be used to manage any project where some files must be updated automatically from others whenever the others change

+ makefiles consist of rules that essentially describe a graph of dependencies -
  each target depends zero or more targets. 

----

### Make - Introduction (2)

+ The general `make` command syntax is: `make [options] [target] ...`
+ Simple usage looks like this:
  ```bash
  $ make build
  # The above tells `make` to fulfill the `build` target.
  ```  
+ The `-f` is used to specify the makefile.
  ```bash
  $ make -f my_make_file.mak
  ```
  If it is omitted `make` will look for `GNUmakefile`, `makefile`
  or a `Makefile` file in the current directory. The convention is `Makefile`, but some projects may have more than one makefiles.
+ The `-j` option specifies the number of jobs to run in parallel.
  ```bash
  # 4 parallel jobs
  $ make -j4

  # Unlimited parallel jobs
  $ make -j
  ```
+ The `-s`, `--silent` and `--quiet` options suppress printing of the commands
  as they are executed.

----

### Make - Introduction (3)

+ Makefiles consist of rules for building **target**s:

```makefile
target ... : prerequisites ...
    recipe
    ...
    ...
```
+ **prerequisites** is a list of targets that must be completed before the
  **recipe** for **target** can be executed
+ **recipe** is a list of commands that make executes to build the **target**.
  By default, commands placed on separate lines, indented by a <kbd>Tab</kbd>, but
  the whole rule can also be written on a single line, by putting `;`
  after the prerequisites:
  ```makefile
  targets : prerequisites ; recipe
  ```
+ By default, `make` starts with the first target. Often makefiles are written
  so that the first target is for compiling the entire program.
+ Make uses topological sorting to find the set of recipes which need to be executed.
+ Each command is executed by a separate shell.

----

### Make - Introduction (4)

+ A rule may have no command lines defined:
  ```
  all: clean build docs install
  ```
+ Long lines can be split with a backslash followed by a newline:


---

## 4. Cross-platform

> Cross-platform programming is the practice of actively writing software that will work on more than one platform.

Platform - the combination of hardware architecture, an operating system, and runtime libraries which host program execution.

API vs ABI

API
+ expresses a software component in terms of its
  + operations
  + inputs / outputs
  + underlying types
+ defines functionalities that are independent of their respective implementations
+ allows definitions and implementations to vary without compromising the interface.
+ interfaces are defined at <u>source code</u> level

ABI - the interface between two program modules, at the level of <u>machine</u> code
+ the sizes, layout, and alignment of data types

There two important terms:
Host - the platform on which the compiler runs
Target - the platform for which the compiler generates code

Cross-platform software may be divided into two types:
+ Write once, compile anywhere (Ada, C, C++, D, Go, Pascal) - rely on API compatibility
+ Write once, run anywhere (JVM, .NET) - rely on binary compatibility



---

## 5. The C Standard Library

Each library function is declared, with a type that includes a prototype, in a
**header**, whose contents are made available by the `#include` preprocessing
directive.

The header declares a set of related functions, plus any necessary types and additional macros
needed to facilitate their use.

Error handling
<assert.h>
<errno.h>

Utilities
<stddef.h>
<stdlib.h>
<signal.h>
<setjmp.h>
<stdio.h>
<time.h>
<threads.h> (since C11)


Language support
<stdarg.h>
<stdalign.h (since C11)
<stdnoreturn.h> (since C11)
<setjmp.h>
<iso646.h> (since C95)

Types
<stdatomic.h> (since C11)
<stdbool.h> (since C99)
<complex.h> (since C99)
<stdint.h> (since C99)
<stddef.h>

Numeric limits
<limits.h>
<float.h>
<stdint.h> (since C99)
<inttypes.h> (since C99)


Numerics library
<math.h>
<tgmath.h> (since C99)
<fenv.h>
<complex.h>


Strings library and locale
<ctype.h>
<wctype.h> (since C95)
<string.h>
<wchar.h> (since C95)
<uchar.h> (since C11)
<locale.h>







---
6. Overview of development tools
    * Using the terminal.
    * Useful command-line utilities.
    * Compilers, linkers and related tools.
    * Debugging with GDB.


---



http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf


An identifier can denote an object; a function; a tag or a member of a structure, union, or
enumeration; a typedef name; a label name; a macro name; or a macro parameter. The
same identifier can denote different entities at different points in the program. A member
of an enumeration is called an enumeration constant. Macro names and macro
parameters are not considered further here, because prior to the semantic phase of
program translation any occurrences of macro names in the source file are replaced by the
preprocessing token sequences that constitute their macro definitions.o

For each different entity that an identifier designates, the identifier is visible (i.e., can be
used) only within a region of program text called its scope. Different entities designated
by the same identifier either have different scopes, or are in different name spaces. There
are four kinds of scopes: function, file, block, and function prototype. (A function
prototype is a declaration of a function that declares the types of its parameters.)

Namespaces
— label names (disambiguated by the syntax of the label declaration and use);
— the tags of structures, unions, and enumerations (disambiguated by following any32)
of the keywords struct, union, or enum);
— the members of structures or unions; each structure or union has a separate name
space for its members (disambiguated by the type of the expression used to access the
member via the . or -> operator);
— all other identifiers, called ordinary identifiers (declared in ordinary declarators or as
enumeration constants).

6.2.1 Scopes of identifiers

6.2.2 Linkages of identifiers

6.2.3 Name spaces of identifiers

6.2.4 Storage durations of objects

6.2.5 Types

6.2.6 Representations of types

6.2.8 Alignment of objects

6.3 Conversions
6.3.1 Arithmetic operands
6.3.2 Other operands

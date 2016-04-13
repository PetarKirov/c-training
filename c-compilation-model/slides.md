# The C compilation model

---

# Development process

1. Write code `vim prog.c`
2. Compile code `gcc prog.c -o prog`
3. Test the code `./prog`

----

# Compilation process (1)

1. Source file(s) -> compiler -> object file
2. Object file(s) -> linker -> executable / library

----

# Compilation process (2)

Actaully an 8 phase translation process:

| Phase   | Who is responsible?                            | Description                                                      |
|---------|------------------------------------------------|------------------------------------------------------------------|
| Phase 1 | I. Preprocessor                                | Character set mapping and trigraph replacement                   |
| Phase 2 |                                                | Line splicing                                                    |
| Phase 3 |                                                | Tokenization                                                     |
| Phase 4 |                                                | Macro expansion and directive handling                           |
| Phase 5 |                                                | Decoding of escape sequences in character/string literals        |
| Phase 6 |                                                | Concatenation of adjacent string literals                        |
| Phase 7 | II. Compiler (`gcc`, `clang`, `cl`, etc.)      | Lexical, syntax and semantic analysis and code-gen (Compilation) |
| Phase 8 | III. Linker (`ld.bfd`, `ld.gold`, `lld`, etc.) | Resolving external references (Linking)                          |

---

## I. Preprocessor (1)
+ A program (most often actually part of the compiler) that processes
  the source code before it is handed off to the compiler
+ Phases:
  1. Character set mapping and trigraph replacement
  2. Line splicing
  3. Tokenization
  4. Macro expansion and directive handling
  5. Decoding of escape sequences in character/string literals
  6. Concatenation of adjacent string literals

+ You can tell GCC to only run the preprocessor like this:
`gcc source_file.c -E -o source_file.pre`

----

### Phase 1 - Character set mapping and trigraph replacement (1)

> The individual bytes of the source code file (which is generally a text file
> in some multibyte encoding such as UTF-8) are mapped, in implementation
> defined manner, to the characters of the *source character* set. In particular,
> OS-dependent end-of-line indicators are replaced by newline characters.
>
> The source character set is a multibyte character set which includes the
> basic source character set as a single-byte subset, consisting of the following 96 characters:
> * 5 whitespace characters (space, horizontal tab, vertical tab, form feed, new-line)
> * 10 digit characters from `0` to `9`
> * 52 letters from `a` to `z` and from `A` to `Z`
> * 29 punctuation characters: `_` `{` `}` `[` `]` `#` `(` `)` `<` `>` `%` `:`
>   `;` `.` `?` `*` `+` `-` `/` `^` `&` `|` `~` `!` `=` `,` `\` `"` `’`

The multibyte characters are not specified. However every implementation
has to support characters from the basic source character set

----

### Phase 1 - Trigraph replacement (1)

Furthermore,
> Trigraph sequences are replaced by corresponding single-character
> internal representations.

| Primary             | Digraph | Trigraph |
|---------------------|---------|----------|
| `{`                 | `<%`    | `??<`    |
| `}`                 | `%>`    | `??>`    |
| `[`                 | `<:`    | `??(`    |
| `]`                 | `:>`    | `??)`    |
| `#`                 | `%:`    | `??=`    |
| `\ `                |         | `??/`    |
| `^`                 |         | `??'`    |
| <code>&#124;</code> |         | `??!`    |
| `~`                 |         | `??-`    |
| `~`                 |         | `??-`    |

```
??=define arraycheck(a, b) a??(b??) ??!??! b??(a??)
// becomes:
#define arraycheck(a, b) a[b] || b[a]
```

----

### Phase 1 - Trigraph replacement (2)

Trigraphs are not needed anymore. However they can appear unexpectedly:

```
char *str = "Enter a date <?? ?? ??>: "; /* Actually "Enter a date <?? ?? }: " */
printf("Enter date ??/??/??\n"); /* "??/??/??\n" -> "\\??\n" -> "\??⏎" */
// this is a comment ??/
printf("print some text on stdout...\n");
```

Most compilers provide an option to control this. For example `gcc` by
default ignores trigraphs and issues warning (`-Wtrigraphs`) about them.
However trigraphs are enabled if one of the following options is specified:
`-trigraphs`, `-ansi` or `-std=`.

----

### Phase 2 - Line splicing

> Whenever backslash appears at the end of a line
> (immediately followed by the newline character),
> both backslash and newline are deleted,
> combining two physical source lines into
> one logical source line

Line splicing allows definition of multiline macros.
The following is turned into a singe line:

```
#define swap(a, b) { \
  (a) ^= (b); \
  (b) ^= (a); \
  (a) ^= (b); \
}
```

----

### Phase 3 - Tokenization (1)

Q: Will the following code compile?

```
#define INC(X) (++x) // increments by one

int x = 5;
printf ("%d", INC(X));

```

----

### Phase 3 - Tokenization (1)

Q: Will the following code compile?

```
#define INC(X) (++x) // increments by one

int x = 5;
printf ("%d", INC(X));

```

A: Yes. Comments are replaced with whitespace (during Phase 3) before
preprocessor directives are processes and macro invocations are
expanded (during Phase 4).

----

### Phase 3 - Tokenization (3)

> 1) The source file is decomposed into sequences of white-space characters
> (including comments) and preprocessing tokens, which are the following:
>   + header names: `<stdio.h>` or `"myfile.h"`
>   + identifiers `[_\ch][_\ch0-9]*` where `\ch` means `[a-zA-Z]` or
      `\u[0-9a-fA-F]{1,2}` (*universal-character-name*)
>   + pp-numbers - integer and floating point constants
>   + character constants - `'a'` `L'b'` `u'c'` `U'd'`
>   + string literals `"asd"` `L"asd"` `u8"asd"` `u"asd"` `U"asd"`
>     (The encoding of `"asd"` is implementation defined (e.g. Latin 1),
>     only `u8"asd"` is guaranteed to be Unicode UTF8, however both are char*)
>   + punctuators:
>     * `[` `]` `(` `)` `{` `}` `.` `->`
>     * `++` `--` `&` `*` `+` `-` `~` `!`
>     * `/` `%` `<<` `>>` `<` `>` `<=` `>=` `==` `!=` `^` `|` `&&` `||`
>     * `?` `:` `;` `...`
>     * `=` `*=` `/=` `%=` `+=` `-=` `<<=` `>>=` `&=` `^=` `|=`
>     * `,` `#` `##`
>     * `<:` `:>` `<%` `%>` `%:` `%:%:`
>   + individual non-whitespace characters that do not fit in any other category
>
> 2) Each comment is replaced by one space character
>
> 3) Newlines are kept, and it's implementation-defined whether non-newline
>    whitespace sequences may be collapsed into single space characters.

---

## I. Preprocessor (2)

### Phase 4 - Macro expansion and directive handling (1)

> 1. Preprocessing directives are executed, macro invocations are expanded, and
>    `_Pragma` unary operator expressions are executed.
> 2. A `#include` preprocessing directive causes the named header or source
>    file to be processed from phase 1 through phase 4, recursively.
> 3. All preprocessing directives are then deleted.

The preprocessing directives control the behavior of the preprocessor. Each
directive occupies one line and has the following format:
+ `# <preprocessing instruction> <arguments>* <line break>`
+ The recognized preprocessing instructions are: `#define` `#undef` `#include`
  `#if` `#ifdef` `#ifndef` `#else` `#elif` `#endif` `#line` `#error` `#pragma`

The null directive (`#` followed by a line break) is allowed and has no effect.

----

### Phase 4 - Macro expansion and directive handling (2)

The preprocessor is responsible for the following:
+ Source file inclusion - via `#include <filename>` or `#include "filename"` directives
+ Conditional compilation - via `#if` `#else` `#elif` `#ifdef` `#ifndef` ` #endif` directives
+ Text replacement - via `#define` expansion
+ Predefined macro names
+ Compiler-specific preprocessor features - via `#pragma` and `_Pragma(str)`.
+ Error directive - via `#error` directive
+ Line control - via `#line`

----

#### Source file inclusion

Includes another source file into the current source file at the line
immediately after the directive.

There are two variants:
+ `#include <filename>` - searches only standard include directories
+ `#include "filename"` - searches the directory where the current file resides and,
only if the file is not found, searches the standard include directories

----

#### Conditional compilation

+ The conditional preprocessing block starts with `#if`, `#ifdef` or `#ifndef` directive,
+ then optionally includes any number of `#elif` directives,
+ then optionally includes at most one `#else` directive and
+ is terminated with the `#endif` directive.
+ Any inner conditional preprocessing blocks are processed separately.

Each of `#if`, `#elif`, `#else`, `#ifdef` and `#ifndef` directives control a code block until the
first `#elif`, `#else`, `#endif` directive (not belonging to any inner conditional preprocessing blocks).

+ `#if`, `#ifdef` and `#ifndef` directives test the specified expression.
+ The expression must be a constant expression, using only literals and identifiers,
  defined using `#define` directive. Any identifier, which is not literal, non
  defined using `#define` directive, evaluates to 0.

`#ifdef` and `#ifndef` check if the identifier was defined using the `#define` directive.
+ `#ifdef` identifier is essentially equivalent to `#if defined( identifier)`.
+ `#ifndef` identifier is essentially equivalent to `#if !defined( identifier)`.


```
#ifdef __unix__ /* __unix__ is usually defined by compilers targeting Unix */
# include <unistd.h>
#elif defined _WIN32 /* _Win32 is usually defined by compilers targeting Windows */
# include <windows.h>
#endif
```

----

#### Text replacement

There are two types of macros:
+ object-like: `#define <identifier> <replacement token list>`
  ```
  #define PI 3.14159
 ```

+ function-like: `#define <identifier>(<parameter list>) <replacement token list>`
  ```
  #define MAX(a,b) ((a)>(b)? (a): (b))
  ```
A macro lasts until it is undefined with the `#undef` preprocessor directive

----

#### Error directive

This directive aborts the compilation process when it is found, generating a
compilation error that can be specified as its parameter:

```
#if RUBY_VERSION == 190
# error 1.9.0 not supported
#endif
```

---

### Phase 5 - Decoding of escape sequences

> All characters and escape sequences in character constants and
> string literals are converted from **source character set** to
> **execution character set**.

+ `\'` `\"` `\?` `\\` `\a` `\b` `\f` `\n` `\r` `\t` `\v` (*simple-escape-sequence*)
+ `\[0-7]{0,3}` (*octal-escape-sequence*)
+ `\x[0-9a-fA-F]` (*hexadecimal-escape-sequence*)
+ `\u[0-9a-fA-F]{1,2}` (*universal-character-name*)

----

### Phase 6 - Concatenation of adjacent string literals

> Adjacent string literals (separated only by whitespace) are concatenated.

```
char *str1 = "This" " is a " "single string " "after Phase 6";
char *str2 = "Long strings can be split by"
            "putting whitespace between them"
            "that way they can span multiple lines";
```


---

## II. Compiler (1)

### Phase 7 - Compilation

> White-space characters separating tokens are no longer significant. Each
> preprocessing token is converted into a token. The resulting tokens are
> syntactically and semantically analyzed and translated as a translation unit.

> When preprocessing tokens are converted to tokens during translation phase 7,
> if a preprocessing token could be converted to either a keyword or an
> identifier, it is converted to a keyword.

> Preprocessing numbers acquires type and value after a successful
> conversion to a floating constant token or an integer constant token.

> A terminating null character is added to each string literal, and then each
> literal initializes an unnamed array with static storage duration and
> length just enough to contain the contents of the string literal plus
> one the null terminator.

The most complex phase of the translation process. Given that the
compiler is standard-complaint, it has implementation freedom.

This phase typically consists of the following transformations:

1. Lexical analysis -> stream of tokens
2. Syntax analysis -> parse tree
3. Semantic analysis -> semantically validated parse tree
4. Conversion to intermediate representation
   (most often language agnostic)
5. Machine independent optimizations
6. Machine dependent optimizations
7. Output -> object files (binary) or assebly

----

#### Machine independent optimizations
+ Removing recursion
+ Data-flow (constant propagation, common subexpression elimination,
  dead store elimination)
+ Interprocedural optimizations (dead-code elimination, function inlining)
+ loop optimizations (unrolling, nest optimization, splitting, loop unswitching)
+ *Optimizing compilers focus on relatively shallow constant-factor performance improvements*
+ *Compilers usually have to support a variety of conflicting objectives
  (compilation speed vs fast code vs small code)*

#### Machine dependent optimizations
+ Register allocation
+ Instruction selection
+ Instruction scheduling
+ Rematerialization

---

## III. Linker and loader

### Phase 8 - Resolving external references (1)

> All external object and function references are resolved. Library components
> are linked to satisfy external references to functions and objects not
> defined in the current translation. All such translator output is collected
> into a program image which contains information needed for execution in
> its execution environment.

Linking is the process of collecting and combining various pieces of code and
data into a single file that can be loaded (copied) into memory and executed.

Linkers enable *separate compilation*. However only **ABI compatible** object
files can be linked together.

----

### Phase 8 - Resolving external references (2)
#### Object files

They can be categorized into:
+ Relocatable - Contains binary code and data in a form that can be combined
  with other relocatable object files at compile time to create an executable
  object file
+ Executable - Contains binary code and data in a form that can be copied directly
  into memory and executed
+ Shared - A special type of relocatable object file that can be loaded into
  memory and linked dynamically, at either load time or run time

* Compilers and assemblers generate relocatable object files.
* Linkers generate executable object files.

----

### Phase 8 - Resolving external references (3)

#### Linking process

It usually consists of:
+ Symbol resolution - associate each symbol reference with exactly one symbol definition.
+ Relocation - Relocation is the process of assigning load addresses to various parts
  of a program and adjusting the code and data in the program to reflect the assigned addresses.

Usually there are two types of files that linkers can produce:
+ Executables
+ Libraries

  Depending on when the linking takes place, the libraries can be further devided into:
  + static - when the linking happens at **compile-time**
  + dynamic - when the linking happens at **load-time** or **run-time**

----

### Phase 8 - Resolving external references (4)

When talking about dynamic linking in particual, it is ofter useful have the
following categorizations:

+ Loading - mapping an object file into memory
+ Linking - resolving symbols

+ Compile-time (Phases 1 to 7) - when source files are translated into object files
+ Link-time - when external references are resolved
+ Load-time - when object files are mapped into memory
+ Run-time - when the program is executing

----

### Phase 8 - Resolving external references (5)

Loading and linking can be both static or dynamic:

| Library type | loading | linking | Description      |
|--------------|---------|---------|------------------|
| static | static  | static  | Everything is resolved at compile time. At process start everything is loaded into memory immediately and no extra resolution (linking) is necessary |
| dynamic      | static  | dynamic | The executable contains reference(s) to the dynamic/shared library(s), but the symbol table is missing or incomplete. Both loading and linking happen **load-time** when the OS starts the process execution.    |
| static       | dynamic | static  | The executable has an address/offset table generated at **compile-time**. Loading of external dependencies is delayed to **run-time**.   |
| dynamic      | dynamic | dynamic | The executable doesn't contain direct references to the dynamic library (visible at **compile-time** or **load-time**). At **run-time** the process (through an OS API) loads the dynamic library and manually resolves references to the symbol(s) it needs. |

----


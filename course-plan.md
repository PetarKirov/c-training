I. Lecture - Basics of programming in C
	1. Introduction
		* Writing, compiling and debugging programs
		* Program structure and organization
		* Execution environment
		* Program correctness and ways of ensuring it
	2. Overview of the C syntax
		* Declaring and defining functions, structures, unions, bitfields, enums and typedefs.
		* Built-in types. Variables, constants and storage classes.
		* Expressions and control flow.
		* Memory addressing. Pointers, arrays and strings.
		* Macros.
		* Differences with C++ (and Java/C#).
	3. The C compilation model
		* Compilation pipeline (preprocessor -> compiler -> linker).
		* Headers (.h) vs source (.c) files.
		* Pre-processor (includes, ifdefs, etc.).
		* Organizing reusable code into libraries
		* Using Make for building projects.
	4. Platform abstraction
	5. The C Standard Library
	6. Overview of development tools
		* Using the terminal.
        * Useful command-line utilities.
		* Compilers, linkers and related tools.
		* Debugging with GDB.

II. Lecture - The C view of the hardware. User defined types.
	1. Mapping of C language constructs to hardware
		* Comparison between the C and other higher-level languages w.r.t. hardware control.
		* Binary representation. Endianess.
		* Instruction Set Architecture <-> C.
		* Registers, stack and heap, dynamic and static memory.
		* CPU cache, RAM and virtual memory.
		* I/O. Volatile registers.
		* ABI. Static and dynamic linking.
		* Effects of compiler transformations on generated code (undefined behavior, inlining, -O2 vs Os)
	2. Data types and type casting
		* Built-in and user defined types.
		* Structure layout. Alignment.
		* Integer promotion.
		* Usual Arithmetic Conversion.
		* Pointer casts.
		* Function pointers.
	3. Memory management
		* malloc, realloc and free
		* reference counting
		* copy-on-write
	4. Implementing data structures in C
		* Abstract data types
		* Fixed-size buffers. Ring buffers.
		* Linked-lists. Single and double linked lists.
		* Resizeable arrays
		* Binary trees
		* hash tables
		* multi-dimensional arrays
	5. Emulating OOP in C
	6. Optimizing memory layout for size and/or execution speed and/or flexibility.

III. Lecture - Concurrency and parallelism. External events.
	1. Introduction
	2. Synchronization primitives
		* Semaphores, mutexes, read-write locks, etc.
		* Atomic operations
	3. Callbacks and reentrancy
	4. Processing external events
		* Signal and interrupt handling
		* Scheduling (async I/O, thread pools, task pools)
		* Message passing
		* Networking
IV. Lecture - Recommendations for writing high-quality C programs. 
	1. Code reusability
	2. Encapsulation
	3. Code style
	4. API design
	5. Selected topics from the CERT C Secure Coding Standard
		* Error handling
		* Memory management
		* Signal/interrupt handling
		* Concurrency

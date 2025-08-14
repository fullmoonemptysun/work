1. Assemblers are written in C currently. But how is that possible when they are the ones assembling C programs? Remember historical ladder. 

> *"there was once, a binary assembler which assembled a an assembler written in assembly then that assembler assembled one written in C and now this one assembles other programs in C"*

2. Four compiler stages: lexer -> parse -> assembly generation -> code emission
3. The _lexer_ breaks up the source code into a list of _tokens_. Tokens are the smallest syntactic units of a program; they include delimiters, arithmetic symbols, keywords, and identifiers. If a program is like a book, tokens are like individual words.
4. The _parser_ converts the list of tokens into an _abstract syntax tree (AST)_, which represents the program in a form we can easily traverse and analyze.
5. The _assembly generation_ pass converts the AST into assembly. At this stage, we still represent the assembly instructions in a data structure that the compiler can understand, not as text.
6. The _code emission_ pass writes the assembly code to a file so the assembler and linker can turn it into an executable.

7. gcc is a compiler wrapper that invokes the preprocessor compiler the assembler the linker.


8. **`gcc -S -O -fno-asynchronous-unwind-tables -fcf-protection=none**
	- -S : don't invoke the linker and assembler
	- -O: optimize
	- fno-asynchronous-unwind-tables: don't genarate unwind table used for debugging
	- -fcf-protection=none**: disable control flow protection (generates some instructions we are not concerned with yet).
	- output: `.global main` is an **assembler directive** provides directions for the assembler.
	- **Assembler directives** always start with a period.
	- `main` is a symbol, a name for a memory address. 
	- `.globl main` tells assembler that main is a global symbol. If this is not done, `main` is only accessible from this assemble and hence object file. But setting a symbol as globl makes it accessible from other object files too. 
	- Assembler records this fact in a section of the object file called the *symbol table* which the linker uses when it links object files together. Table contains info about all the symbols in an object file or executable.
	- *labels* are a string or number followed by a colon. Marks the location a symbol refers to. 
	- Assembler doesn't know the final address of the instructions under main but it knows what section of the object file it belongs to and also it's offset from the start of that section. 
	- Different sections are loaded on different parts of the program's address space during runtime.

9. The linker adds a small piece of wrapper code called `crt0` (C runtime) to handle setup before main runs and teardown after it exits. The wrapper code does the following:
	- Makes a call to `main`, which is why `main` needs to be globally visible.
	- Retrieves return value from main.
	- Invokes the `exit` syscall passing it the ret value from main. Then the control goes to the OS (kernel).

10. The **linker** also associates each entry in the symbol table with a memory address through a process called *symbol resolution*. Then it performs *relocation* which updates every place that uses a symbol to use the corresponding address instead. (simplified).



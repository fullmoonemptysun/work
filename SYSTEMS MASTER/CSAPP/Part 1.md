# Compiler Drivers
1. Linking can be performed at compile time or at load time (loader) or even at runtime. 

## Sample Program for this Chapter
```c
//------------main.c----------------

int sum(int * a, int n);
int array[2] = {1,2};

int main(){
	int val = sum(array, 2);
	return val;
}
```
```c
//-------------sum.c------------------
int sum(int * a, int n){
	int i, s = 0;
	
	for(int i = 0; i < n; i++){
		s += a[i];
	}
	return s;
}
```


2. **Static Linking.** The linker combines relocatable object files to form an executable object file prog.

3. When a compiler driver is run the following programs are called in sequence:
- `cpp` The preprocessor
- `cc1` The compiler
- `as` Assemble the assembly file into a relocatable object file
- `ld` Combines main.o and sum.o along with system object files, to create the binary executable object. 

Finally, when `./prog` shell invokes a function in the operating system called the **loader.** which copies the code and data in the ELF prog into memory and then transfers control to the beginning of the program.

# Static Linking 
1. Data sections and other sections withing an relocatable object files are basically contiguous sequence of bytes. 

2. To build an ELF a linker does the following:
	- *Symbol Resolution*. Symbols are defined in object files for global vars, functions, static vars. The purpose of resolution is to associate each symbol reference with single symbol definition.
	- *Relocation*. Compilers and assemblers generate code and data sections that start at address 0. Linker relocates these sections by associating a memory location with each symbol definition and then replacing all symbol references with these memory location addresses. 
	- The instructions on how to do all this is provided by the assembler to the linker through *relocation entries*

# Object Files

1. Come in 3 forms:
	- Relocatable: Can be combined with other relocatable object files at compile time to create an ELF
	- Executable: Can be directly loaded into memory and executed
	- Shared: Special type of relocatable file that can be loaded into memory and linked dynamically, at load time or run time. 

2. Different systems run different types of executable object files which are similar conceptually.


# Relocatable Object Files
![[Pasted image 20251002220607.png]]

1. ELF header begins with a 16 byte sequence that describes the word size and byte ordering of the system (LE, BE) that generated the file. 

2. Rest of the Header has information that helps linker to parse and interpret the object file. (Size of the file, object file type (r, e, s), Machine type, offset of the section header table, size and number of entries in the header table.)

3. Section header table describes locations and sizes of the sections. 

4. Some sections:
`.text` : Machine code of the compiled program
`.rodata` : read only data such as the format strings in printf statements and jump tables for switch statements. 
`.data` : initialized global and static variables. 
`.bss` : uninitialized global/static variables initialized to 0. Occupies no actual space in the object file. Merely a placeholder. The distinction between .data and .bss is important to save space. So .bss does not take any disk space in the object file. At run time, these variables are allocated in memory with an initial value of 0.

`.symtab` : symbol table. Info about functions and global variables that are defined/referenced in program. `strip` removes this information. Only contains global/static symbol entries. 

`.rel.text` : list of locations in `.text` that will need to be modified when linker combines this file with others. Any instruction that calls an external function or references a global variable will need to be modified. Instructions that call local functions don't need to be modified. Executable object files do not need this (obviously).

`.rel.data` : relocation information for any global variables that are referenced or defined by the module. Any initialized global variable whose initial value is the address of a global variable or externally defined function will need to be modified. 

`.debug` : debugging symbol table, only present with the -g option (i redacted some info cuz not neccessary).

`.line` : Mapping between line numbers in the C code and the machine code. (same like debug info. only there if -g option is used)

`.strtab` : A string table for symbol tables, and for the section names in the section headers. Sequence of null terminated character strings.

# Symbols and Symbol Tables

1. 3 different kinds of symbols  according to liker:
	- Global Symbols: defined by module, can be referenced by other modules. *Nonstatic C functions and global variables.*
	- Global Symbols referenced by the current module but defined by some other module. Called *externals* and correspond to non static C functions and variables that are defined in other modules.
	- Local symbols that are referenced exclusively by this module (and are defined here too). static C functions and variables. 

2. Local variables that are defined as `static` are not managed on the stack (local frame). Compiler allocates space in `.data` or `.bss` for each definition and creates a local linker symbol in the symbol table with a unique name. 
Example: if x is used in two functions then in the symbol table they are put in as x.1 and x.2

3. Symbol tables are built by assemblers using the symbols exported by the compiler into the assembly-language `.s` file.

4. The symbol struct in ELF
![[Pasted image 20251003001343.png]]
- Name: byte offset into the string table that points to the string name of the symbol. 
- Value: Symbols address. For relocatable modules, value is the offset from the beginning of the section where the object is defined. For executable object files, is an absolute run time address from top of the binary.

- Size: size in bytes
- Type: usually data or function
- Section: index into the section header table. 

4. 3 special pseudosections that don't have entries in section header table.
	1. ABS: is for symbols that should not be relocated
	2. UNDEF: is for undefined symbols: referenced here but defined elsewhere
	3. COMMON: is for uninit data that are not allocated yet. Size gives the min size and value gives the alignment requirement.

>[!important] COMMON vs. `.bss`
> COMMON: unitialized global variables
> `.bss`: Unitialized static variables, and global or static variables that are initialized to 0.

>[!important] Symbol Relocation vs. Symbol Resolution
>- **Symbol resolution** = finding _which definition_ of a symbol (function or variable) a reference refers to.  
>Example: The linker decides that `printf` in your code should come from `libc.so`.
>- **Relocation** = once the definition is known, _patching the machine code or data_ so the reference actually points to the correct memory address. 
> Example: The linker modifies the “call `printf`” instruction to jump to the actual address of `printf`.



  

# Reverse Engineering: Introduction

## Forward Engineering Process

1. Forward engineering process: Creation of a program:
    1. Figure out what you to code
    2. code it
    3. compile it (can include JIT)
    4. run it

![[image 53.png|image 53.png]]

  

1. At every step, information is lost. (object code, byte code, executable)
2. **Reverse Engineering:** Get this information back.

  

….But what type of info is lost???

  

## Discussion:

1. Between Design and code: What was even the code supposed to do? what was the intention? , etc. is lost
2. Between and compiling and assembly:

- Comments
- Variable names
- Function names
- Structure (classes, structs, etc.) data.
- Sometimes, entire algorithms (optimization) (a faster way to do it)

  

How do we get all this back from the binary or the executable?…

  

## Forward Engineering Tools:

1. Some tools:
    1. vim
    2. gcc
    3. strings
    4. strip

and..source code is born..

1. Can make gcc generate the assembly for a c file by using the `-S` and `-masm=intel` to generate x86 intel assembly.
2. cpp is the C pre processor that handles all the includes, the macros, etc.
3. We lost comments during preprocessing with cpp
4. Then lost variable names after compilation. Also lost type data.

> [!important] `objdump` is a versatile tool used in software development. It's part of the GNU Binutils package, and it's primarily used for displaying information about object files. You can use it to disassemble compiled code, which can be really useful for debugging or analyzing how your code is translated into machine language.
> 
> Here are some common uses of `objdump`:
> 
> - **Disassembling Code**: Viewing assembly code from binaries.
> - **Inspecting Object Files**: Checking the contents of an object file, including sections and symbols.
> - **Debugging**: It helps in understanding the binary layout and identifying any issues.

```Bash
objdump -d exe \#gives the object code details and instructions
objdump -M intel -d exe \#same thing as above but the instructions are in intel x86 assembly
```

  

1. However, the software is not just shipped like above. It is usually stripped off of any metadata and then packaged.

**Checking a files size**

> [!important] The `du` command stands for "disk usage" and is used in Unix-like operating systems to estimate file space usage. This command reports the amount of disk space used by the specified files and for each subdirectory.
> 
> Here's the basic syntax:
> 
> `du [options] [file...]`
> 
> The `-sk` option combines two flags:
> 
> - `s` (or `-summarize`): Provides a total for each argument.
> - `k` (or `-kilobytes`): Displays sizes in kilobytes, instead of the default 512-byte blocks.
> 
> When you use `du -sk [file]`, it will output the total disk usage of the specified file or directory in kilobytes. For example:
> 
> `du -sk /path/to/directory`
> 
> This will give you a summarized disk usage of the directory in kilobytes

  

and then,

`strip exe` decreases the size of the binary file. Very difficult to debug, no symbols exist

> [!important] The `strip` command is used in Unix-like operating systems to remove symbols and other information from binary files. This can make executables and object files smaller by removing all symbols (e.g., debugging and symbol table information), which aren't needed for the program to run. This is particularly useful for optimizing the size of executables in production environments.
> 
> Here's a basic example of how you might use the `strip` command:
> 
> bash
> 
> `strip your-executable-file`
> 
> After running this command, `your-executable-file` will be reduced in size. However, the removed symbols make it harder to debug the program later, so it's typically used when you don't need to debug the binary.

  

We can do `strings` exe to check for all human readable strings in the file.

> [!important] The `strings` command in Unix-like operating systems is used to find and display readable text strings embedded within binary files. This can be very helpful for extracting human-readable text from executables or other binary files, which can include error messages, print statements, or other informative strings that might be useful for debugging or reverse engineering.
> 
> Here's a basic example of how you might use the `strings` command:
> 
> bash
> 
> `strings your-binary-file`
> 
> This command will scan the binary file and print out all the sequences of printable characters it finds.
> 
> A few additional options you might find useful:
> 
> - `n` or `-bytes=number`: Minimum length of the strings to be printed (e.g., `strings -n 4` will display strings of at least 4 characters).
> - `f` or `-file`: Read files from the specified list.
> - `o`: Precede each string by its offset in the file.

  

## Reverse Engineering Process:

1. Every step in the reverse engineering process is imperfect and relies on some amount of human help.
2. Typically a reverser use several reverse engineering tools to build up a mental model of the target software
3. This art is the focus of this module: how do we reverse the design from the binary (executable).

![[image 1 8.png|image 1 8.png]]

  

  

  

# Functions and frames

## What is a program?

1. A program:
    - consists of modules
    - made up of functions
    - that contain blocks
    - of instructions
    - that operate on variables and data structures.

## Modules

1. Developers rely on libraries to build software. These libraries have well documented functionality. This makes things **easier.**
2. Read the fine manual of the libraries, focus reversing effort on your actual target. (The main module)

  

## Functions

1. Functions represent fairly well encapsulated functionality.
2. Have well defined goal:
    - Set
    - calculate, retrieve, validate
    - Call other functions
    - perform some action on the outside world

  

1. Functions can be reverse engineered in isolation. Later we can get understanding of how they fit together.

  

## Functions: Control flow graph

![[image 2 9.png|image 2 9.png]]

1. Functions are represented as a graph
2. This graph will have multiple blocks with sequence of instructions
3. They may have conditional and unconditional jumps
4. By understanding what the blocks do and what conditions trigger what edges, we can unerstand the functions’s logic

  

## Functions: Prologue

1. Functions often begin with a prologue and end with an epilogue. Prologue sets up the stack the frame.
2. Epilogue tears down the stack frame.

  

  

## The Stack

> [!important]
> 
> ### Memory Sections Notes
> 
> 1. `**.data**` **Section** (Initialized Data Segment)
>     - **Purpose**: Stores initialized global and static variables.
>     - **Properties**: Writable; variables here have predefined initial values.
>     - **Example**:
>         
>         `.data my_integer: .word 10 # Integer initialized to 10 my_string: .asciiz "Hello" # Null-terminated string`
>         
> 2. `**.bss**` **Section** (Uninitialized Data Segment)
>     - **Purpose**: Stores uninitialized global and static variables (implicitly zeroed).
>     - **Properties**: Writable; does not store values in the binary, just size.
>     - **Example**:
>         
>         ```Assembly
>         .bss
>         buffer: .space 100       # Allocates 100 bytes, zero-initialized
>         counter: .word 0         # Zero-initialized integer
>         ```
>         
> 3. `**.rodata**` **Section** (Read-Only Data Segment)
>     - **Purpose**: Stores read-only data, like constants and string literals.
>     - **Properties**: Read-only; data here cannot be modified during execution.
>     - **Example**:
>         
>         ```Assembly
>         .rodata
>         pi: .float 3.14159         # Constant float value for π
>         msg: .asciiz "Read-only message"  # Constant string
>         ```
>         

### Summary Table

|   |   |   |   |
|---|---|---|---|
|Section|Purpose|Modifiability|Typical Contents|
|`.data`|Initialized global/static data|Writable|Initialized variables|
|`.bss`|Uninitialized global/static data|Writable|Zero-initialized or uninitialized vars|
|`.rodata`|Read-only constant data|Read-only|Constants, string literals|

1. But what about local variables?, the stack is the region of memory used to store local variables and call contexts.

  

> [!important]
> 
> 1. When we do the following:
> 
> `cat /proc/self/maps`, we get the memory mapping for different processes on the system:
> 
> ### Memory Mapping and Addresses in `/proc/self/maps`:
> 
> 1. **Memory Map Structure**:
>     - Shows memory regions, permissions, offsets, device info, inode, and file path for each mapped segment.
>     - Each row represents a specific memory area (e.g., code, libraries, heap).
> 2. **Fields**:
>     - **Address Range**: Start and end address of the memory segment.
>     - **Permissions**: `r` (read), `w` (write), `x` (execute), `p` (private).
>     - **Offset**: Position in the file or object being mapped.
>     - **Device**: Majordevice number.
>     - **Inode**: Unique ID for file on filesystem.
>     - **Path**: File path (if mapped to a file).
> 3. **Key Parts**:
>     - Executable (`/usr/bin/cat`), heap, locale files, standard libraries (`libc.so.6`), and encoding cache (`gconv-modules.cache`).
> 4. **Address Length (12 Characters)**:
>     - Modern 64-bit systems use only the lower 48 bits (12 hex characters) for memory addresses.
>     - Remaining bits are used for error checking (canonical addresses) and potential future expansion.
> 
> ### 32-bit vs. 64-bit Architecture:
> 
> 1. **Registers & Data Processing**:
>     - 32-bit: Processes data in 32-bit chunks.
>     - 64-bit: Processes data in 64-bit chunks, enhancing performance for large data.
> 2. **Memory Addressing**:
>     - 32-bit CPUs can address up to 4 GB (2³²).
>     - 64-bit CPUs can theoretically address 16 exabytes (2⁶⁴) but typically use 48 or 57 bits, supporting larger memory.
> 3. **Data Bus Width**:
>     - 64-bit CPUs usually have a 64-bit-wide data bus for faster data transfer.
> 4. **Compatibility**:
>     - 32-bit runs 32-bit OS and apps with a 4 GB RAM limit.
>     - 64-bit supports both 32-bit and 64-bit software, allowing more RAM and efficiency.

  

1. Stack grows backwards.

  

1. Initial layout: Environment variables and the program arugments (argv, argc)
2. When a function is called, the address of the next instruction after the function is pushed to the stack.

![[image 3 9.png|image 3 9.png]]

  

1. Stack frame: stack pointer(latest), base pointer (old)
2. **Prologue:**

- save off the caller’s base pointer
- set the current stack pointer as the base pointer
- “allocate” space on the stack as we go

  

1. **Epilogue:**

- Deallocate the stack, (data is not removed by default)
- restore the old base pointer

  

> [!important]
> 
> ### Stack, Base Pointer (BP), and Stack Pointer (SP) Summary:
> 
> - **Stack**: Used to manage function calls, local variables, and control flow.
> - **Base Pointer (BP)**:
>     - Marks the base of a function’s stack frame.
>     - Used to access local variables and parameters consistently.
>     - Stays fixed throughout the function’s execution.
> - **Stack Pointer (SP)**:
>     - Points to the top of the stack.
>     - Moves up and down as functions are called and return.
>     - Adjusted dynamically during function calls to allocate/deallocate space.
> 
> ### Function Call Flow:
> 
> 1. **Function Call**:
>     - Return address is **pushed** onto the stack (where to return after the function).
>     - **BP** is saved (current BP), then updated to the current **SP**.
>     - **SP** is moved to allocate space for local variables and parameters.
> 2. **Function Execution**:
>     - BP remains fixed during execution, allowing consistent access to local variables.
>     - Variables are accessed using **offsets** from BP.
> 3. **Function Return**:
>     - BP is **restored** to the previous function's BP.
>     - Return address is **popped** off the stack and used to **jump back** to the calling function.
> 
> ### In MIPS (Without BP):
> 
> - **SP** manages the stack frame directly.
> - Access to variables is done **relative to SP**, with offsets for parameters and local variables.
> - SP is adjusted at the start and end of the function, but there's no fixed reference point like BP, so the frame access is based on the current value of SP.
> 
> ### Key Differences:
> 
> - **With BP (e.g., x86)**: BP provides a stable reference point, making it easier to access variables and parameters.
> - **Without BP (e.g., MIPS)**: Everything is accessed relative to SP, and stack frames are managed directly with careful offset tracking.

  

# Data Access

1. Many different data regions as we discussed.

## Accessing data on the stack

![[image 4 7.png|image 4 7.png]]

  

## Accessing data in ELF sections

1. Basically these data are stored close to the next instruction address

![[image 5 7.png|image 5 7.png]]

## Accessing data on the heap

![[image 6 7.png|image 6 7.png]]

  

> [!important] **Note on data structures:** Type information is lost in the compilation process. We wil have to piece type information together from practice. No easier way to learn this than pure intuition.

  

  

# Static Reversing

  

## Static Tools

1. Analyzing a program at rest, what is in the file not what the file does when run.

- Kaitai struct: File format parser and explorer
- nm: lists symbols used by ELF files
- strings: dumps ascii strings found in a file, variable names, function names
- objdump: simple disassembler
- checksec: analyzes security features by an executable.

  

1. Advanced Disassemblers:

- IDA pro: Gold standard of disassemblers
- Binary Ninja: IDA’s main commercial competitor
- Binary Ninja Cloud: browser version of binary ninja
- angr management: academic binary analysis framework
- ghidra: reversing tool created by the NSA
- cutter: reversing tool created by the radare2 osp.

  

> [!important]
> 
> ### Translating ‘\xff’ to Actual Bytes
> 
> The hexadecimal notation (`\xff`) is simply a human-readable way of expressing a value. When this is stored, however, it’s saved as pure binary in the machine's memory, which for `\xff` is the 8-bit binary sequence `11111111`. At the hardware level, the machine doesn’t see `\xff` or `255`—it just sees eight `1`s representing the high and low voltages that encode data in binary.
> 
> In summary:
> 
> - The computer recognizes `\xff` as a hexadecimal literal.
> - This gets stored as an 8-bit binary sequence in memory.
> - When read back, it’s interpreted as the same binary data, which can be printed back as `255` (decimal), `\xff` (hex), or `11111111` (binary), depending on the output format.

  

> [!important]
> 
> ### Memory Addresses in `objdump`:
> 
> - **Virtual Address Space**: The addresses shown in `objdump` represent virtual addresses within the program's address space.
> - **Relative Addressing**: These addresses are typically offsets in the program's code segment.
> - **Address Translation**: The operating system and Memory Management Unit (MMU) map these virtual addresses to actual physical memory when the program runs.
> - **Isolated Address Space**: Each program (process) has its own isolated virtual address space, so virtual addresses are specific to the process.

  

> [!important]
> 
> ### Sections in the Executable Linkable format (ELF) of storing bytes
> 
>   
> The sections like  
> `.init`, `.plt`, and others you see in the `objdump` output are additional sections added by the compiler and linker. They aren’t functions from your C code but are essential for initializing the program, managing external functions, and handling specific runtime operations. Here’s a breakdown of some common sections and why they’re there:
> 
> ### Key Sections Added by Compiler and Linker
> 
> 1. `**.text**`
>     - **Purpose**: Contains the main code of the program, including all functions you wrote in C (e.g., `main`, `foo`, `bar`).
>     - **Details**: It’s the executable code section and includes both your C functions and any code generated by the compiler.
> 2. `**.data**`
>     - **Purpose**: Stores global and static variables that are initialized in your code.
>     - **Details**: Variables with values assigned at compile time (like `int x = 5;`) are in this section.
> 3. `**.bss**`
>     - **Purpose**: Holds uninitialized global and static variables.
>     - **Details**: It saves memory by not storing values (initialized to zero at runtime by convention), like `int y;`.
> 4. `**.rodata**` (Read-Only Data)
>     - **Purpose**: Contains read-only data, such as string literals and constants.
>     - **Details**: For example, `const char *msg = "Hello";` would be stored here.
> 
> ### Special Sections Managed by the Linker
> 
> 1. `**.init**`
>     - **Purpose**: Contains initialization code for setting up the program before `main()` runs.
>     - **Details**: The `_init` function is called before `main()`, often to initialize libraries or set up the runtime environment. This section may be empty if there is no special setup code.
> 2. `**.fini**`
>     - **Purpose**: Contains finalization code that runs after `main()` completes.
>     - **Details**: The `_fini` function is called before the program exits, used to clean up resources (like closing files or releasing memory). It may also be empty if no cleanup is needed.
> 
> ### Sections for External and Dynamic Linking
> 
> 1. `**.plt**` (Procedure Linkage Table)
>     - **Purpose**: Handles dynamic linking for external library functions, like `printf` or `malloc`.
>     - **Details**: This table contains "stubs" or placeholders that resolve function addresses at runtime. The first time an external function is called, the `.plt` section directs the call to the **.got** (Global Offset Table) to get the real address.
> 2. `**.got**` (Global Offset Table)
>     - **Purpose**: Supports dynamic linking by storing addresses of dynamically loaded functions and variables.
>     - **Details**: Used alongside `.plt` to make function addresses accessible when the program calls shared libraries.
> 
> ### Additional Sections
> 
> 1. `**.eh_frame**`
>     - **Purpose**: Holds exception handling data, such as information needed to unwind the stack during exceptions.
>     - **Details**: Primarily used in languages or libraries that support exception handling, like C++.
> 2. `**.comment**`
>     - **Purpose**: Contains compiler and version information, often embedded as metadata.
>     - **Details**: This is typically just for informational purposes and does not affect execution.
> 3. `**.symtab**` and `**.strtab**`
>     - **Purpose**: The symbol table (`.symtab`) and string table (`.strtab`) store information about variable and function names.
>     - **Details**: These are used for debugging and symbol resolution but are often stripped from release builds to save space.
> 
> ### Summary of How They Work Together
> 
> - **Code & Data Sections**: `.text`, `.data`, `.bss`, and `.rodata` store code and variables directly used by your program.
> - **Initialization & Cleanup**: `.init` and `.fini` ensure your program is set up and cleaned up properly.
> - **Dynamic Linking**: `.plt` and `.got` enable calls to shared libraries, making dynamic linking efficient.
> - **Additional Metadata**: `.eh_frame`, `.symtab`, and `.strtab` assist in debugging, exception handling, and runtime introspection.
> 
> These sections are created by the compiler and linker to ensure the program runs correctly in its environment, handling everything from setup to external dependencies.

  

> [!important]
> 
> ### Instructions Must be Loaded into Memory for Execution
> 
> 1. **File Loading**:
>     - When an executable file (like ELF or PE) is run, the **OS loader** loads its contents from disk into **memory**.
>     - Sections (`.text`, `.data`, `.bss`, etc.) are mapped to specific **memory regions**.
> 2. **Memory Mapping**:
>     - **.text**: Contains the machine instructions, mapped to **executable memory**.
>     - **.data/.bss**: Holds initialized and uninitialized data, placed into separate memory areas.
> 3. **Execution Flow**:
>     - CPU begins at the **entry point** (e.g., `main`) and fetches instructions from the `.text` section in memory.
>     - The **program counter (PC)** updates to the next instruction, allowing the CPU to fetch, decode, and execute.
> 4. **Why Memory is Required**:
>     - **Physical Execution**: The CPU can only execute instructions stored in **RAM** or virtual memory.
>     - Instructions stored in memory are in **binary machine code**, the format the CPU can process.
> 5. **Dynamic Linking**:
>     - For external libraries, the loader maps them into memory and uses tables (e.g., **PLT/GOT** in ELF) to adjust addresses.
> 6. **Memory Addresses vs. File Addresses**:
>     - **File addresses** in disassembly (e.g., `0x22c6`) are logical offsets.
>     - These are mapped to actual **memory addresses** by the loader and may vary with each run due to **ASLR** (Address Space Layout Randomization).

  

# Binary Files

1. ELF defines the file as it will be executed by the machine. It has a lot information about the program.

![[image 7 6.png|image 7 6.png]]

  

## What is an ELF?

1. ELF is a binary file format
2. Contains the program and its data
3. Describes how the program should be loaded (program/segment headers)
4. Contains metadata describing program components

[[FALL 2024/Information Assurance and Security/REVERSE ENGINEERING/ELF]]

  

## ELF Program Headers

1. Define segments
2. Specify information needed to prepare the program for execution
3. Important types:
    1. INTERP: ==defines the library that should be used to load this ELF into memory==
    2. LOAD: defines a part of the file that should be loaded into memory.
4. Program headers are the source of information used when loading a file
5. Can use `readelf` to get human readable information about an ELF file.

  

## ELF Section Headers

1. A different View of the ELF with useful information for introspection, debugging, etc.
2. Not very important for the loading and execution itself.

![[image 8 6.png|image 8 6.png]]

![[image 9 6.png|image 9 6.png]]

  

## Symbols

1. Binaries and libraries that use dynamically loaded libraries rely symbols/names to find libraries, resolve function calls into those libraries, etc.

[[FALL 2024/Information Assurance and Security/REVERSE ENGINEERING/ELF|ELF]]

## How to Interact with these files

![[image 10 6.png|image 10 6.png]]

  

# Linux Proecess Loading

1. Example process cat /flag

  

## Portrait of a Process:

1. Browsers, Word, CMD are all processes
2. Kernel handles processes
3. Every linux processes has:
    - State (running, waiting, stopped, zombie)
    - priority (and other scheduling information) Kernel schedule
    - parent, siblings, children
    - shared resources (files, pipes (stdin, stdout), sockets)
    - Virtual memory space
    - Security context:
        - effective uid and gid
        - saved uid and gid
        - capabilities

  

## Where do these processes come from:

1. Mostly mitosis!
2. kernel uses `fork` and `clone` system calls that create a nearly exact copy of the calling process: a parent and a child.
3. Later, the child process usually uses the `execve` syscall to replace itself with another process.
4. `/bin/cat` is typed in bash → bash `fork`s itself into the old parent process and the child process → the child process execves `/bin/cat`, becoming `/bin/cat`

  

## The Main flow of a process happening:

1. A process is created by kernel
2. Cat is loaded
3. Cat is initialized
4. Cat is launched
5. Cat reads its arguments and environment
6. Cat does its thing
7. Cat terminates :(

  

Before we load, we have to figure out…

## Can we Load??

1. Before anything is loaded, the kernel checks for executable permissions.
2. If a file is not executable, `execve`will fail.

## What to Load??

1. To figure this out, the kernel reads the beginning of the file and then makes a decision about:

- if the file starts with a #!, the kernel knows its a script file and just reads the rest of the line to know what interpreter is to be used and then executes that interpreter process with the original file as an argument.

```Plain
Example:
Have /bin/echo as the shebang interpreter
and then whatever is put in it, is executed as an 
argument to /bin/echo. (/bin/echo becomes the 
interpreter while anything else inside becomes the 
file.
```

- If not a shebang script, then the kernel looks for a matching format in /proc/sys/fs/binfmt_misc. The kernel then executes the interpreter specified for that format with the original file as an argument.
- If none of above, and if the file is dynamically linked (ELF file relies on some library that needs to be loaded by the interpreter itself.), the kernel just reads the interpreter/loader defined in the ELF and executes it with the file and lets the interpreter take control.
- If the file is a statically linked ELF, the kernel straight up loads it
- Other legacy file formats are checked for
- These are all recursive!

  

  

## Dynamically Linked ELFs: the Interpreter:

1. Proecess loading is done by the ELF interpreter specified in the binary.

![[image 11 6.png|image 11 6.png]]

1. But if the overridden interpreter is not found, we run into an error.
2. `ldd` utility to figure out dependencies

  

## Dynamically linked ELFs: The loading process:

1. The program and interpreter are loaded by the kernel
2. The interpreter locates the libraries in the following order:
    
    ![[image 12 6.png|image 12 6.png]]
    

only need to worry about 2d. and 2e.

1. The interpreter loads the libraries:
    1. these libraries can depend on other libraries, causing more to be loaded
    2. relocations updated: after a library is loaded, they may have to be moved from the address they are loaded to to a different spot
2. LD_PRELOAD: can be used to overwrite functions in libraries that will be loaded in the future.
3. LD_LIBRARY_PATH: Every library needed will be first looked in this path
4. RUNPATH : can be modified with patchelf specified in the binary file.

  

## Where is all this getting loaded to?

1. Each Linux process has a virtual memory space. It contains:
    1. the binary
    2. libraries
    3. heap
    4. stack
    5. memory specially mapped by the program
    6. some helper regions
    7. kernel code in the upper half of the memory (in accessible to the process)
2. These Virtual memories is dedicated to your process
3. Physical memory is shared among the whole system
4. The whole space is at /proc/self/maps

  
  

![[image 13 6.png|image 13 6.png]]

  

## The loading process: Statically Linked

1. The Binary is straight up loaded
2. We won’t find memory mappings for libraries and loaded because everything is compiled in the binary itself which results in a bigger size too.

  

## CAT Initialized:

![[image 14 6.png|image 14 6.png]]

1. Useful in preload files (LD_PRELOAD variable)

  

…That’s it on how processes are loaded! 😃

  

# Linux Process Runtime (execution):

  

1. After initiialization, process is launched

  

## Cat is launched:

![[image 15 6.png|image 15 6.png]]

1. Can also be overidden and put in the LD_PRELOAD env variable

  

## Cat reads its arguments and environment:

![[image 16 5.png|image 16 5.png]]

  

## Using Library Functions

![[image 17 5.png|image 17 5.png]]

  

## Interacting with the environment

![[image 18 5.png|image 18 5.png]]

![[image 19 4.png|image 19 4.png]]

  

## Signals

![[image 20 4.png|image 20 4.png]]

![[image 21 4.png|image 21 4.png]]

1. pgrep tells you the process id (ps aux)

  

  

## Shared Memory:

![[image 22 4.png|image 22 4.png]]

  

## Process termination

![[image 23 4.png|image 23 4.png]]

  

  

# Dynamic Reverse Engineering

1. Analyzing a program at runtime

  

## Program tracing

1. Itrace: traces library calls
2. strace traces system calls

![[image 24 4.png|image 24 4.png]]

  

## Debugging

1. gdb will become best friend (or worst enemy)
2. Look into it more
3. gdb scripting is possible

  

![[image 25 3.png|image 25 3.png]]

  

## Timeless Debugging:

![[image 26 3.png|image 26 3.png]]

  

  

# Other Notes:

  

  

  

  

# Challenge Hints:
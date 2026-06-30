  

# Assembly Crash Course

1. Eventually, all programs end up at the CPU level (binary machine code)
    
    ![[image 26.png|image 26.png]]
    

  

1. Registers and Cache is different. Both parts of CPU. When we pull data from the memory, It goes to the cache in the CPU and then later moved to the registers.

  

  

1. Multi core processor:  


![[image 1 7.png|image 1 7.png]]

  

  

![[image 2 8.png|image 2 8.png]]

  

  

![[image 3 8.png|image 3 8.png]]

  

1. Assembly Tells the CPU what to do.
2. What we want to be one are the operations, and on what are operands (regs, immediate values, etc.)

  

![[image 4 6.png|image 4 6.png]]

![[image 5 6.png|image 5 6.png]]

![[image 6 6.png|image 6 6.png]]

  

![[image 7 5.png|image 7 5.png]]

## Representing Data:

1. Representing text in Binary

![[image 8 5.png|image 8 5.png]]

  

![[image 9 5.png|image 9 5.png]]

![[image 10 5.png|image 10 5.png]]

  

  

![[image 11 5.png|image 11 5.png]]

  

![[image 12 5.png|image 12 5.png]]

  

  

![[image 13 5.png|image 13 5.png]]

  

  

## Registers:

![[image 14 5.png|image 14 5.png]]

  

![[image 15 5.png|image 15 5.png]]

- rax is the latest evolution of 64 bit
- al, ah would be the primitve ones, 8 bit registers
- ax is 16bits
- We can still access these smaller units

  

  

![[image 16 4.png|image 16 4.png]]

  

![[image 17 4.png|image 17 4.png]]

- Missing: exchange operation

  

  

![[image 18 4.png|image 18 4.png]]

  

  

# Writing your first Program:

1. In the latest incarnation of x86, i.e, x86_64, there are 16 general purpose registers, ex: rax
2. We can move(copy) immediate data in rax like:
    
    `mov rax, data`
    

  

1. This is the intel syntax (correct syntax), Atnt syntax is loser syntax
2. The program crashed! We'll go into more details about  
    what a Segmentation Fault is later, but in this case, the program  
    crashed because, after the CPU moved the value 60 into rax, it was  
    never instructed to stop execution. With no further instructions  
    to execute, and no directive to stop, it crashed.  
    

  

1. This program was not cleanly exited from, (no exit defined)
2. Usually cleanly entering and exiting from a program is handled by the operating system
3. The operating system manages the existence of programs and interactions between the programs, your hardware, the network environment, and so on.

  

1. Programs "interact" with the CPU using assembly instructions such as the `mov` instruction you wrote earlier.  
    Similarly, your programs interact with the operating system (via the CPU, of course) using the  
    `syscall`, or _System Call_ instruction.

  

1. Pretty much everything apart from operating on data has to be done by the operating system and the program does syscalls to ask the OS to do these functions on the program’s behalf

  

1. There are a lot of different system calls your program can invoke.  
    For example, Linux has around 330 different ones, though this number changes over time as syscalls are added and deprecated.  
    Each system call is indicated by a _syscall number_, counting upwards from 0, and your program invokes a specific syscall by moving its syscall number into the `rax` register and invoking the `syscall` instruction. For example, if we wanted to invoke syscall 42 (a syscall that you'll  
    learn about sometime later!).  
    

  

1. The syscall number for `exit` is 60
2. The `exit` syscall causes a program to exit

  

1. Every program exits with an _exit code_
    1. This code is passed as a parameter to the syscall.
    2. syscalls have many parameters but `exit` only has one.
    3. To pass the first parameter, we put the value in the `rdi` register and execute the syscall

  

1. To create object (.o) files form .s files we need to assemble:

```Assembly
The assembly file contains, well, your assembly code. 
For the previous level, this might be:

hacker@dojo:~$ cat asm.s
mov rdi, 42
mov rax, 60
syscall
hacker@dojo:~$

But it needs to contain just a tad more info. 
We mentioned that we're using the Intel assembly syntax in this course, 
and we'll need to let the assembler know that. You do this by prepending a 
directive to the beginning of your assembly code, as such:

hacker@dojo:~$ cat asm.s
.intel_syntax noprefix
mov rdi, 42
mov rax, 60
syscall
hacker@dojo:~$
```

  

1. After creating the object files, we need to link them using `ld` (link editor) command like:
    
    ```Plain
    hacker@dojo:~$ ls
    asm.o   asm.s
    hacker@dojo:~$ ld -o exe asm.o
    ld: warning: cannot find entry symbol _start; defaulting to 0000000000401000
    hacker@dojo:~$ ls
    asm.o   asm.s   exe
    hacker@dojo:~$
    ```
    

  

1. Even if a file is to be run by itself, it has to be linked to turn into an executable.

  

1. The warning states there was no symbol _start found. The `_start` symbol is, essentially, a note to `ld` about where in your program execution should begin when the ELF is executed.

  

### Executable Linkable Format (ELF) Overview

- **Purpose**: Standard format for executables, object code, shared libraries, and core dumps on Unix/Linux systems.
- **Key Components**:
    - **Header**: Metadata like file type, architecture, entry point, and offsets.
    - **Program Headers**: Define memory segments for execution (e.g., code, data).
    - **Section Headers**: Organize sections (e.g., `.text`, `.data`, `.bss`, `.rodata`) for linking.
    - **Sections**: Contain code, data, and other specific information.

### Running an Object File Directly

- **Object File Limitations**: Contains machine code but lacks the structure needed for execution (e.g., entry point, headers).
- **Linker Role**: The linker finalizes the binary, adding headers and defining an entry point for the OS.

### `ld` Program on Unix

- **Function**: `ld` is the linker program used to prepare object files for execution.
- **Key Tasks**:
    - Combines multiple object files into one executable.
    - Resolves symbols (e.g., function and variable references).
    - Adds necessary headers (e.g., ELF headers) for OS compatibility.
    - Links external libraries, if any.

  

  

  

1. Adding _start Symbol:

```Plain
hacker@dojo:~$ cat asm.s
.intel_syntax noprefix
.global _start
_start:
mov rdi, 42
mov rax, 60
syscall
hacker@dojo:~$ as -o asm.o asm.s
hacker@dojo:~$ ld -o exe asm.o
hacker@dojo:~$ ./exe
hacker@dojo:~$ echo $?
42
hacker@dojo:~$
```

There are two extra lines here.  
The second,  
`_start:`, adds a _label_ called start, pointing to the beginning of your code.  
The first,  
`.global _start`, directs `as` to make the `_start` label _globally visible_ at the linker level, instead of just locally visible at the object file level.  
As  
`ld` is the linker, this directive is necessary for the `_start` label to be seen.

  

  

1. For debugging purposes, we can use `strace`, which traces the syscalls in a program:

```Bash
hacker@dojo:~$ strace /tmp/your-program
execve("/tmp/your-program", ["/tmp/your-program"], 0x7ffd48ae28b0 /* 53 vars */) = 0
exit(42)                                 = ?
+++ exited with 42 +++
hacker@dojo:~$
```

- Here the first system call that is reported is the execve(), it is kinda like the yin to exit()’s yang. This means it is the signal to start the prgram execution.

  

1. The `alarm` system call (syscall number 37!) will set a timer  
    in the operating system, and when that many seconds pass, Linux will  
    terminate the program.  
    The point of  
    `alarm` is to, e.g., kill the program when it's frozen  
      
    
2. rsi is another register like rdi.

  

  

# Memory Hierarchy

1. Registes: Fast but expensive and small
2. Computer Memory (RAM): Larger than all the registers combined, but slower (fairly fast)

  

## Memory:

1. Memory ↔ Registers
2. Memory ↔ Disk
3. Memory ↔ Network
4. Memory ↔ Video Card
5. Process Memory is addressed linearly:
    1. From: `0x10000` to 0x7fffffffffff this amount of ram is usually not possible

  

### Process’s memory:

1. we dont have 127 TB of RAM…but that’s okay, cuz its all virtual.
2. Process memory starts out partially filled in by the operating system.

  

### Memory(STACK):

1. Many uses, example: temporary data storage. Address can begin at absurd spots.
2. Registers and immediates can be pushed onto the stack to save values.

![[image 19 3.png|image 19 3.png]]

![[image 20 3.png|image 20 3.png]]

  

### Addressing the stack:

1. The CPU knows where the stack is because its address is stored in rsp.

![[image 21 3.png|image 21 3.png]]

1. Can move data between registers and memory with mov!

![[image 22 3.png|image 22 3.png]]

1. The brackets specify that it is a memory address in there.

![[image 23 3.png|image 23 3.png]]

![[image 24 3.png|image 24 3.png]]

![[image 25 2.png|image 25 2.png]]

  

### Controlling Write Sizes:

1. Can use partials to store/load fewer bits:

![[image 26 2.png|image 26 2.png]]

![[image 27.png]]

1. bh is an 8 bit register (1 byte)

![[image 28.png]]

  

### Memory Endianess:

1. Data on most modern systems is stored backwards, in _little_ endian.

![[image 29.png]]

![[image 30.png]]

1. Bytes get flipped, bits stay same.

  

### Address Calculation:

![[image 31.png]]

### RIP - Relative Addressing

1. rip is the register that points at the next instruction.

![[image 32.png]]

  

1. `lea` calculates the address and stores it (not the value at the address)

![[image 33.png]]

![[image 34.png]]

- This is useful for working with data embedded near your code!
- This is what makes certain security features on modern machines possible

  

### Writing Immediate Values to the memory

1. We can write immediate values. However we must specify their size.

![[image 35.png]]

  

1. Depending upon the assembler, DWORD PTR might just be DWORD
2. The term **DWORD** stands for "double word." On systems where a **word** is 16 bits (common in older architectures), a **DWORD** is double that size, i.e., 32 bits. Even though modern architectures like 64-bit machines have larger word sizes, the term **DWORD** is often retained for historical and compatibility reasons, consistently meaning 32 bits regardless of the system's native word size.

  

### Other Memory Regions:

1. Other regions might be and often are mapped in memory.
2. We previously talked about regions loaded due to directives in the elf headers, but functionality such as mmap and malloc can cause other regions to be mapped as well. (Future Modules)

  

## Other Notes

1. we have the following situation:

```Plain
  Address │ Contents
+────────────────────+
│ 133700  │ 42       │◂┐
+────────────────────+ │
                       │
 Register │ Contents   │
+────────────────────+ │
│ rdi     │ 133700   │─┘
+────────────────────+
```

`rdi` now holds a value that corresponds with the address of the data that want to load!  
Let's load it:  

```Plain
mov rdi, [rax]
```

Here, we are accessing memory, but instead of specifying a fixed address like `133700` for the memory read, we're using the value stored in `rax` as the memory address.  
By containing the memory address,  
`rax` is a _pointer_ that _points to_ the data we want to access!  
When we use  
`rax` in lieu of directly specifying the address that it stores to access the memory address that it references, we call this _dereferencing_ the pointer.  
In the above example, we _dereference_ `rax` to load the data it points to (the value `42` at address `133700`) into `rdi`.

  

## Challenge Notes:

  

  

# Hello Hackers!

## System Calls, Environment Interaction

1. syscall is an instruction that makes a call into the operating system

![[image 36.png]]

![[image 37.png]]

  

![[image 38.png]]

  

### “String Arguments”:

1. Some System calls take “string” arguments (for example, file paths).
2. A string is a bunch of contiguous bytes in memory, followed by a 0 byte (\0)

![[image 39.png]]

  

### Constant Arguments:

1. Some system calls require archaic “constants”.

![[image 40.png]]

  

### Quitting the program:

![[image 41.png]]

  

  

## Other notes:

1. Programs write to stdout of course, by invoking a system call.
2. Specifically, this is the `write` system call, and its syscall number is `1`.  
    However, the write system call also needs to specify, via its parameters, _what_ data to write and _where_ to write it to.
3. Remember: File descriptors from linux luminarium: This is basically how program receive the stdout or stderror or stdin file when they want to write to some other process.

  

1. Operating system has a lot of other things to do than just run system calls, it has to run the linux instructions, manage hardware, etc. So we minimize the number of syscalls. (if we keep writing character by character.)

  

1. The solution to this is to write multiple characters at the same time.  
    The  
    `write` system call does this by taking _two_ parameters for the "what": a _where_ (in memory) to start writing from and a _how many_ characters to write.  
    These parameters are passed as the second and third parameter to  
    `write`.  
    In the kinda-C syntax that we learned from  
    `strace`, this would be:

```C++
write(file_descriptor, memory_address, number_of_characters_to_write)
```

  

1. How to do this:
    1. We'll pass the first parameter of a system call, as we reviewed above, in the `rdi` register.
    2. We'll pass the second parameter via the `rsi` register.  
        The agreed-upon convention in Linux is that  
        `rsi` is used as the second parameter to system calls.
    3. We'll pass the third parameter via the `rdx` register.  
        This is the most confusing part of this entire module:  
        `rdi` (the register holding the first parameter) has such a similar name to `rdx` that it's really easy to mix up and, unfortunately, the naming is this way for historic reasons and is here to stay.  
        Oh well...  
        It's just something we have to be careful about.  
        Maybe a mnemonic like "  
        `rdi` is the **i**nitial parameter while `rdx` is the **x**tra parameter"?  
        Or just think of it as having to keep track of different friends with similar names, and you'll be fine.  
        
    4. And of course, the syscall index for `write` in the `rax`.

  

1. How do we invoke two system calls?  
    Just like you invoke two instructions!  
    First, you set up the necessary registers and invoke  
    `write`, then you set up the necessary registers and invoke `exit!  
      
      
    

  

## Challenge Notes:

1. First challenge. Loading 1 character from mem address 1337000
2. Works, but we get a segmentation fault (crash) because no syscall to exit.

```C++
.intel_syntax noprefix
.global _start

_start:
mov rdi, 1
mov rsi, 1337000
mov rdx, 1
mov rax, 1
syscall
```

  

  

  

  

# x86 ASSEMBLY crash course:

## Control Flow

1. How do we make decisions? branching etc.

  

### What To execute:

1. Assembly instructions are direct translations of binary code (stored as data)

![[image 42.png]]

1. The rip points to these address

  

### Jumps

![[image 43.png]]

1. LABELs are not instructions, just helpers for assemblers
2. We can jump backwards and forward, (rip is decreased, increased)

  

### Conditional Jumps

![[image 44.png]]

….Come in comparisons conditions

![[image 45.png]]

Conditional jumps (`jb`, `je`, `jne`, `jg`, etc.) are always **RIP-relative with an immediate displacement**.
This means they will only take a immediate value offset from the rip address, not registers, not memory accesses.
  

and then we have loops:

![[image 46.png]]

  

### Finally We have Functions

![[image 47.png]]

![[image 48.png]]

  

  

### BUILDING PROGRAMS

![[image 49.png]]

1. noprefix mean we are not adding % before register names
2. -nostdlib tells gcc this is not a C program!
3. We can use the special ? variable to access the last exit code ($?)

  

### Disassembling A program

![[image 50.png]]

![[image 51.png]]

  

  

  

## Debugging:

![[image 52.png]]

  

  

## Challenge Notes:

1. Division in x86 is more special than in normal math. Math here is called integer math, meaning every value is a whole number. As an example: `10 / 3 = 3` in integer math. Why? Because `3.33` is rounded down to an integer.
    
    The relevant instructions for this level are:
    
    - `mov rax, reg1`
    - `div reg2`
    
    Note: `div` is a special instruction that can divide a  
    128-bit dividend by a 64-bit divisor while storing both the quotient and  
    the remainder, using only one register as an operand.  
    
    How does this complex `div` instruction work and operate on a 128-bit dividend (which is twice as large as a register)?
    
    For the instruction `div reg`, the following happens:
    
    - `rax = rdx:rax / reg`
    - `rdx = remainder`
    
    `rdx:rax` means that `rdx` will be the upper 64-bits of the 128-bit dividend and `rax` will be the lower 64-bits of the 128-bit dividend.
    
    You must be careful about what is in `rdx` and `rax` before you call `div`.
    

  

  

1. It turns out that using the `div` operator to compute the modulo operation is slow!
    
    We can use a math trick to optimize the modulo operator (`%`). Compilers use this trick a lot.
    
    If we have `x % y`, and `y` is a power of 2, such as `2^n`, the result will be the lower `n` bits of `x`.
    
    Therefore, we can use the lower register byte access to efficiently implement modulo!
    
    Using only the following instruction(s):
    
    - `mov`
    
    Please compute the following:
    
    - `rax = rdi % 256`
    - `rbx = rsi % 65536`

  

> [!important]
> 
> # Why directly specifying an address for jmp not work as intended.
> 
> ### Immediate Jump (`jmp 0x1234`)
> 
> - In x86 assembly, `jmp` can take an immediate operand, but this operand is usually interpreted as a relative offset rather than an absolute address. This means the processor adds the immediate value to the current instruction pointer (EIP/RIP) to calculate the destination.
> - Example: `jmp 0x1234` would be treated as "jump to the address at `(current RIP + 0x1234)`," not directly to `0x1234`.
> - In 64-bit mode, immediate addressing restrictions are stricter. For instance, absolute addresses cannot be encoded directly with `jmp` because the instruction format does not support large immediate values in 64-bit.
> 
> ### Register-Based Jump (`mov eax, 0x1234; jmp eax`)
> 
> - When you load an address into a register (e.g., `mov eax, 0x1234`) and then jump with `jmp eax`, the processor interprets the register's value as an absolute address.
> - This approach avoids ambiguity because the CPU is directly instructed to jump to the address stored in the register. It also works in scenarios (like 64-bit mode) where absolute addressing via an immediate value is not allowed.
> 
> ### Why This Happens
> 
> 1. **Instruction Encoding**: The x86 instruction set has different encodings for immediate operands and register operands. Immediate operands for `jmp` are often treated as relative offsets to save space.
> 2. **64-bit Mode Rules**: In 64-bit mode, instructions like `jmp` cannot take 64-bit immediate values directly. Using a register to hold the address bypasses this limitation.
> 3. **Flexibility of Registers**: Registers allow you to work with full addresses directly and avoid any potential complications from instruction encoding.

  

1. In conditional jump, we must make sure that we compare/operate on consistent word size. (example, if we know something is a dword pointer, we must state that and use the right registers for that operation. not doing so will cause extra data to be operated on, yielding unpredictable results if the neighboring bits are not all 0)
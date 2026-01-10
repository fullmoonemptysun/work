1. The shell is a command-line interpreter that prints a prompt, waits for you to type a command line, and then performs the command. If the first word of the command line does not correspond to a built-in shell command, then the shell assumes that it is the name of an executable file that it should load and run. So in this case, the shell loads and runs the hello program and then waits for it to terminate. The hello program prints its message to the screen and then terminates. The shell then prints a prompt and waits for the next input command line.

![[Pasted image 20251118115722.png]]

2. Each I/O device is connected to the I/O bus by either a controller or an adapter.The distinction between the two is mainly one of packaging. Controllers are chip sets in the device itself or on the system’s main printed circuit board (often called the motherboard). An adapter is a card that plugs into a slot on the motherboard. Regardless, the purpose of each is to transfer information back and forth between the I/O bus and an I/O device.

3. L1 cache is smaller and faster than L2 and L3 (they are just smaller memory components closer to the cpu) implemented using SRAM technologies.

![[Pasted image 20251118140050.png]]


## 1.6 Storage devices form a hierarchy
![[Pasted image 20260105184204.png]]



## 1.7 The OS manages the hardware

![[Pasted image 20260105184300.png]]

>- POSIX is just standardized UNIX
### 1.7.1 Processes
- The notion of a process by an operating system gives the illusion of complete ownership of the processors, memory and IO devices.
- Concurrent running of processes implies that their instructions are interleaved in the processor. (Context switching)
- Multi core processors run different instructions that may or may not belong to the same process at 1 single instance.
![[Pasted image 20260105191335.png]]
- The operating system manages a state block for each process. This is the context (PCB) (register file, PC, etc.)
### 1.7.2 Threads
- Processes can further be divided into functional units instead of one singular flow, called threads.
- Useful in networking.
- Easier to share data between threads than between processes
- Share the same global data and code.

### 1.7.3 Virtual memory
- Illusion of the same address space to each process that looks like this (for linux).
![[Pasted image 20260105192118.png]]

- Well defined regions:
	- Program code and data: Code begins at the same fixed address for all processes, followed by data locations that correspond to global C variables. These areas are initialized directly from the contents of an Executable, object file. Fixed in size once the process is running.
	- Heap: The code and data areas are followed immediately by the Heap. Can grow and shrink because of C `malloc/free` calls.
	- Shared libraries: Near the middle. Code and data for shared libraries such as the C stdlib. A difficult concept. More in chapter 7.
	- Stack: Above Shared library region, we have the user stack. Grows downwards, grows/shrinks dynamically.
	- and Finally, Kernel VA: This always stays in all address space and user level processes cannot read/write data or call any functions from this region.


- Sophisticated translation of these Virtual addresses to actual physical addresses is done by the OS. Idea is to store data in disk and use the main memory as cache.

### 1.7.4 Files
- Every file is a sequence of bytes. Nothing more and nothing less. 
- All I/O devices: disk, keyboards, display and even networking cards are modeled as files by the OS. 
- These devices are interacted with by using a set of read/write functions for files, defined in UNIX_IO

## 1.8 Systems communicate with other systems using network
- For an individual system, the network is just like another I/O device: we write bytes into it, we read bytes from it.
 ![[Pasted image 20260105194227.png]]
- Simple interaction between 2 connected systems:
![[Pasted image 20260105194711.png]]

## 1.9 Important themes
Tour of systems is complete. Here are some themes that are common to all aspects of system design. 

### 1.9.1 Concurrency and Parallelism
- Concurrency: The general concept of a system with multiple, simultaneous activities
- Parallelism: The use of concurrency to make a system faster. 

- Parallelism can be exploited at different levels of abstraction:


#### <strong style="color:orange;">Thread Level Concurrency</strong>
- Within the process level abstraction, even more control flows.
- Two main concepts:
	- Multi core processors 
	- Hyperthreading

>[!important] How they correlate?
>**Physical Hardware: Cores**
A core is an independent processing unit with its own:
ALU (arithmetic logic unit)
Registers
L1 cache (usually)
>
Pipeline for instruction execution
A quad-core processor has 4 separate cores that can genuinely execute 4 instruction streams simultaneously. This is true parallelism.
>
**Hyperthreading (Simultaneous Multithreading)**
Here's where it gets interesting. A single core has execution resources that aren't always fully utilized. For example, when executing:
>
>```asm
>add %rax, %rbx    # Uses ALU
>load (%rcx), %rdx # Uses memory unit
>```
The ALU and memory unit could theoretically work simultaneously, but a single instruction stream doesn't always provide this opportunity.
>
Hyperthreading duplicates just the architectural state (registers, program counter) but shares the execution resources. So one physical core presents itself as 2 logical processors to the OS.
Think of it like this:
1 physical core = 1 set of execution units (ALU, FPU, load/store units)
With hyperthreading = 2 sets of registers/PC
The core rapidly switches between the two register sets, or when one thread stalls (cache miss, branch misprediction), the other thread can use the execution units

![[Pasted image 20260105201705.png]]
![[Pasted image 20260105201725.png]]
intel i7

- A program needs to be written with thread aware logic to make it actually faster on a system like this


#### <strong style="color:orange;">Instruction Level Concurrency</strong>

- Going further lower, modern processors use **pipelining** to be able to execute multiple instructions in one cycle. This is instruction level parallelism. Basically multiple pieces of an instruction are handled in parallel by the cpu, making that overall instruction faster than it was.
- Processors that can do this are *superscalar* processors.
- It is possible to write code that gets translated into instructions that can utilize this instruction level parallelism to be faster.

#### <strong style="color:orange;">SIMD Concurrency</strong>

Some modern processors have single instructions that can operate on multiple data. Although compilers try to extract SIMD instructions from normal programs written in C, the better way to do this is by intentionally using *vector* types supported by compilers like `gcc`. This style of programming is explored further later on. 



### 1.9.2 Importance of abstractions in Computer Systems

- One good habit is to formulate a simple API for a set of functions so they can be used without the knowledge of the inner workings.
- The instruction set architecture is the abstraction on the processor level. The simple sequential instruction set abstracts away the complex machinery uner the hood.
- We have looked at the abstractions at the OS level. 
- Finally, we have virtual machine. The abstraction at the entire computer level. 

---





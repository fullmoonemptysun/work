1. The operating system must ensure that all processes get a chance to execute and are isolated from each other to isolate malfunctions. (_time - share)_
2. But too much isolation is not good either. Processes should have a way to interact with each other (Pipes, etc.)
3. Therefore, OS has to do:
    1. Multiplexing
    2. Isolation
    3. Interaction

  

1. There are many ways to achieve the above, but xv6 focuses on the mainstream design centered around a _monolithic kernel_: used by many unix systems
2. Xv6 runs on a multicore 64 bit RISC-V CPU

  

1. Xv6 is written in LP64 C which means Long and pointers are 64 bits, but ints are 32 bit.

  

1. CPU in a computer is surrounded by support hardware, usually in the form of I/O interfaces.

  

1. Xv6 is written to work with simulated hardware by **QEMU**’s “-machine virt” option. (RAM, ROM containing boot code, A serial connection to the user’s keyboard/screen, and a disk for storage)

  

  

# 2.1 Abstracting Physical Resources

1. Why have OS at all? syscalls can be implemented as a library which processes load with:
    
    1. Processes must be well behaved and give up CPU periodically.
    2. Above is not usually true and processes have bugs.
    3. Need better isolation
    
    However the above approach is implemented in some operating systems for embedded devices are organized this way.
    
      
    
2. For strong isolation it is important that applications do not directly access sensitive hardware.
3. Interaction is based on an interface provided by the Operating system.
4. Applications have an easier time to interact with the interface(pathnames) rather than the actual underlying hardware (disks).
5. The `syscall`s are this interface.
6. The CPU hardware is also abstracted by the OS when it transparently switches it between processes, storing and restoring register states as necessary. The applications don’t even know about this time sharing.

  

1. Memory (RAM) is also abstracted when processes use `exec` to build their memory image, instead of directly touching the physical memory. The OS decides here how and where it wants to place the process. (if needed, this can also be on the disk).

  

1. FDs are also an example of abstraction. Nobody knows where pipes or a file is actually stored as long as they have the FDs for these.

  

1. If one application in a pipeline fails, the kernel generates an EOF signal for the next process in the pipeline.

  

# 2.2 User Mode, Supervisor Mode, and Syscalls

1. For strong isolation, we need hard boundaries between all the processes and the operating system itself. No process should be able to read the OS’s data structures/instructions or any other process’s memory.

  

> [!important] **SIDE NOTE:** There are mainly 4 major regions of a process’s memory layout:
> 
> ```mermaid
> graph TD
>   Process --> Text["Text Segment (Code)"]
>   Process --> Data["Static (DATA)"]
>   Process --> Heap["Heap"]
>   Process --> Stack["Stack"]
>   
>   Data --> data["Initialized data (.data)"]
>   Data --> BSS["Unitialized data (.bss)"]
> ```

  

1. CPU hardware supports this isolation. RISC V has three modes: _Machine Mode, Supervisor Mode_ and _User mode._ (see )

  

1. Machine mode is mainly for configuration related stuff. The CPU boots in machine mode, runs the hard coded config routines

  

> [!important] **SIDE NOTE:** The boot process in terms of the CPU:<br><br>
> 
> 1. When the CPU powers on, it resets and sets the program counter to a hardcoded address.
> 2. It jumps to that address and executes firmware code (BIOS, UEFI, or boot ROM).
> 3. This firmware initializes basic hardware and loads the next-stage bootloader from storage.
> 4. The bootloader loads the OS kernel into memory and transfers control to it.
> 5. The kernel sets up memory, devices, and starts the first user process (`init` or `systemd`

2. Then it changes to <strong style="color: aqua;">Supervisor Mode</strong> 
3. Can run privileged instructions:
		- Change value of register that points to IDT
		- Disable/enable interrupts
		- etc

4. When a <strong style="color: pink;">User Mode</strong> Process tries to execute a privileged instruction, the CPU switches to <strong style="color: aqua;">Supervisor Mode</strong> so supervisor mode code can terminate the application for doing something it is not supposed to.

5. User mode: User mode instructions only, runs in *user-space*
6. Supervisor mode: Privileged instructions also, runs in *Kernel-space*
7. The software running in this kernel space is none other than the **Kernel** itself.

8. When an application wants to invoke a kernel function (ex: `read` syscall) it needs to transition to kernel.

9. CPUs provide special instructions for this switch: `ecall` in xv6. This basically starts executing kernel code from an entry point specified by the kernel.

10. After this switch, the kernel can verify if the arguments passed with the sycall are valid and safe, (ex: If the address specified are withing the range of the process's memory)

11. Then the kernel decides whether the application is allowed to perform the requested operation (e.g: check if the application is allowed to write to a certain file), and then finally it denies/executes the syscall.

>[!ERROR] Entry point after the kernel switch
>Very important the kernel alone defines the entry point for transitions to supervisor mode. If an application could do this, a malicious user could enter the kernel at a point where the validation of arguments is skipped!

>[!NOTE]
>The `ecall` instruction on RISCV is equivalent of the `syscall` instruction in some other major CPUs such as x86.


# 2.3 Kernel Organization
1. What part of kernel should run in supervisor mode??
	1. The full thing: design is called the ***Monolithic kernel***:
		- All ~~kernel~~ OS actions happen with full hardware privileges.
		- <span style="color:lightgreen;">Convenient because developer does not need to decide which part doesn't need privilege.</span> 
		- <span style="color:red;">Any mistake from the developer is fatal  because an error in supervisor mode will likely cause the kernel to fail and computer must reboot.</span> 

	2. Alternative: Bulk of OS is executed in user mode: ***Microkernel***:
		- OS services run as processes called *servers*.
		- To allow, say a process to access the file server, the kernel provides an ipc mechanism.
		- This mechanism is implemented as a few lowlevel functions for starting new processes, opening a file, sending messages, accessing hardware, etc.

2. Linux is mostly monolithic but has some services running in the user space (window systems, et cetera).
3. Xv6 is a monolithic kernel. thus the kernel interface is the Operating system interface.

# 2.4: <span style="color:aqua;">CODE</span> : xv6 organization
![[Pasted image 20250621003825.png]]
Check these out! 

# 2.5 Process overview
1. Unit of isolation in most Unix systems is a *process*. Abstraction to prevent process's from wrecking other process's resources or the kernel itself.

2. This abstraction must be done with care because a buggy or malicious application may trick the kernel into doing something bad. 

3. Mechanisms used to implement this abstraction: User/supervisor mode flags, address spaces and time slicing of threads.

4. This process abstraction gives the illusion to a program that it is running on a private machine dedicated to itself. It provides it with what seems to be a private memory system (*address space*) that no other process can read from/write to, it also provides what seems to be its own CPU to execute instructions. This program is fooled into believing there is nothing else on the computer except itself.

5. **Page Tables** are used by xv6 to give each process its own address space. 
6. Page tables are implemented by hardware.
7. The RISC-V chip page table translates (Maps) a VA (that instructions work on) to a PA (address that the CPU chip sends to main memory).

8. xv6 maintains a page table for each process that defines that process's address space.


	 ![[Pasted image 20250621005832.png]]
9.  As we can see, VAs begin at 0.
10. Instructions come first, then global variables, then stack, heap.
11. On RISC-V, pointers are 64 bits wide. hardware only uses the low 39 bits out of the 64 bits when doing virtual addressing and xv6 only uses 38 out of those 39. Thus the maximum address is 2^38 - 1 = 0x3fffffffff which is the value of `MAXVA` [[https://github.com/mit-pdos/xv6-riscv/blob/riscv//kernel/riscv.h#L363]] 

12. Xv6 has two reserved pages on top after the heap called the ***trampoline and trapframe***. These are used to switch to kernel and back. The trampoline page contains code to transition in and out of the kernel and mapping the trapframe is necessary to save/restore the state of the process. (will be explained more further).

13. Kernel maintains multiple parameters for each process, which it gathers into a `struct proc`. 

14. Important parameters for a process's state: pagetable, kernel stack, and run state. 

15. Parameters can be referred to as `p->xxx` (ex: `p->pagetable`)
16. Each process has a <span style="color:aqua;">Thread of Execution <i><strong>(thread)</strong></i></span> which executes its instructions.

17. The kernel suspends one process's thread and resumes another process's thread and goes back and forth like this between processes.

18. Most of the suspended thread(local vars, function return addresses) are stored on the thread's stack(s).

19. Each process has 2 stacks: user stack and a kernel stack (`p->kstack`). When the process is executing its own user instructions, only user stack is being used. But when it switches to the kernel (syscall, or interrupt), then the kernel code executes using this process's `kstack` as stack. A process's thread alternates between using its user stack and its kernel stack. 

20. Process makes a system call using the `ecall` instruction:
	- `ecall` raises hardware privilege level and changes program counter to a kernel defined **entry point**. 
	- This **entry point** has code that switches to the respective kernel stack and then -
	- - executes kernel code for the system call (IDT -> System call implementation, etc.)
	- When the system call completes, the kernel switches back to the user stack
	- and then returns to the user space using `sret` instruction.
	- `sret` lowers hardware privileges and resumes executing instructions right after the system call instruction.

21. A process's thread can 'block' in the kernel to wait for I/O, and resume where it left off when the I/O has finished.

22. `p->state` : allocated/ready to run/running/waiting for I/O/exiting

23. `p->pagetable` holds the process's page table in the format that the RISC-V hardware expects. 

24. Xv6 ensures the paging hardware uses a process's 
	`p->pagetable` when executing the process in user space.

25. Conclusion: Address space gives a process the illusion of its own memory and thread gives a process the illusion of its own CPU. 
26. In xv6, a process consists of only one address apace and one thread. (No multithreading)

---
# insert multithreading for OS notes here




---

# 2.6 <span style="color:aqua;">CODE:</span> Starting xv6, the first process and system call

1. When RISC-V CPU turns on it initializes itself and eventually runs a bootloader from the hardrive.
2. Boot loader loads xv6 kernel into memory
3. Then the CPU starts executing xv6 code in machine mode, starting at `_entry` (kernel/entry.S: 7)
4. At this point, paging hardware is disabled. All VAs map directly to PAs
5. Then the loader loads the kernel at PA `0x80000000` rather than `0x0` because range `0x0:0x80000000` contains I/O devices.

6. The instructions at `_entry` also set up a stack so xv6 can start running C code. 
7. Xv6 declares an initial stack, `stack0`, in the file `start.c` (kernel/start.c:11) <span style="color: green">This is okay because no stack is used only an attribute is set</span>:
	This **declares and reserves** space in memory for a stack per CPU — it does **not execute** anything.
	- `char stack0[4096 * NCPU];` reserves an array large enough to hold one 4KB stack per CPU.
	- `__attribute__ ((aligned (16)))` ensures the array is aligned on a 16-byte boundary, which is good for stack alignment and required by RISC-V calling conventions.
	This **does not run** — it's just a global **symbol/data** that gets placed into the kernel’s `.bss` or `.data` segment by the linker (`kernel.ld`), and becomes part of the memory image loaded at boot. This is

8. The code also loads `sp` register with address `[stack0+4096]` , which is the top of the stack, because the stack on risc-v grows down. 

9. Now that the kernel has a stack, `_entry` calls into C code at `start` (kernel/start.c: 21)

10. `start` does some configuration allowed only in machine mode and then switches to supervisor mode
11. `mret` is the instruction on RISC-V to switch to supervisor mode
12. How does it switch?:
- it sets the `M previous pivilege mode (MPP)` bits (12:11) of the `mstatus` register to the one corresponding to supervisor mode (10). 
```c
// set M Previous Privilege mode to Supervisor, for mret.

unsigned long x = r_mstatus();

x &= ~MSTATUS_MPP_MASK; //~ (3 << 11). 
//x has become 0b[65:13]00[10:0] so just OR ing with required value will do the trick.


x |= MSTATUS_MPP_S; //supervisor mpp mode (1L(ong) << 11)

w_mstatus(x); //set the register to the correct value

```
- then sets the return address to `main()` by putting the address of main into `mepc` register {Machine exception program counter register: where we should return when `mret` is called}
```c
w_mepc((uint64)main);
```
- then disables page translations? (virtual addressing): because virtual memory has yet not been set up and all addresses are physical even in supervisor mode. No need (should not translate).
```c
w_satp(0);
```
- Then delegates all exceptions and interrupts to the Supervisor. Also enables the interrupt trap handling for the supervisor (important, if never done interrupts will stay pending.)
```c
// delegate all interrupts and exceptions to supervisor mode.
w_medeleg(0xffff);
w_mideleg(0xffff);
w_sie(r_sie() | SIE_SEIE | SIE_STIE | SIE_SSIE);
```

- Finally, before jumping to supervisor mode, `start` programs the clock chip to generate timer interrupts.
`timerinit()` : defined in `timer.c` 

What `timerinit()` does:
```c
timerinit() {   // each CPU sets a timer interrupt to happen in the future,   // using the RISC-V SBI (Supervisor Binary Interface)  
set_next_timer();  // typically sets mtimecmp for the next interrupt
}
```

Under the hood, this:
- Reads the current time (`mtime`)
- Adds some interval (e.g., 10ms worth of ticks)
- Writes to `mtimecmp`, which is a special register that:
- Triggers a **timer interrupt** when `mtime >= mtimecmp`

and then finally,
`mret` (switch to S mode).

13. `main()` initializes some subsystems and devices.
14. Then it creates the first ever process by calling `userinit()` (proc.c: 23)

15. `userinit()` basically creates a process (`user/initcode.S`), allocates memory for it and then sets it as RUNNABLE (the state) and done. There is a initprocess binary instruction program that calls exec("/init") assembled from `../user/initcode.S` is obtained by octal dump:
	`od -t xC ../user/initcode`
	LOOK AT ALL THE CODE IN CASE OF CONFUSION

16. **Explanation**: userinit() creates this first process which is a small program that leads into calling exec and to load /init as the first user process.

17. *init* sets up a console and opens a shell process, system is now up.

# 2.7 : **Security Model**

1. In operating system design, it is assumed that a user process's user level code will try its best to wreck the kernel or other processes.
2. Example: dereferencing pointers outside address space, execute any RISC-V instructions, read-write to any RISC V control register, directly access device hardware, pass clever values to system calls in an attempt to trick the kernel into crashing or doing something stupid.

3. Process's must be restricted by the kernel to: Read/write/execute their own memory, use 32 general purpose registers and affect the kernel and other processes in the way syscalls are intended to allow. 

4. Kernel code is expected to be bug free and certainly to contain nothing malicious. 
5. Hardware is assumed to be working right without bugs.
6. Distinction between kernel and user code is sometimes blurred: example: privileged user process loading modules into the **linux** kernel.

7. In the real world there is also multithreading, though xv6 does not support this, linux has the `clone`, a variant of `fork` to control which aspects of a process threads can share.


## Syscall flow in xv6
> User processes include `user/user.h` to see the syscall interface to the kernel.
> These functions are only headers and are never implemented on the user side.
> Stubs for them (Assembly code with their label) is generated by a perl script. 
> These stubs use macros defined in syscalls.h and syscalls.c of the form SYS_usersidesyscallname
> These correspond to a syscall code or more specifically one of the sys_funcname pointers in the syscall table that maps these two (syscall.c)
> sys_funcnames() are wrappers over the actual syscall functions so they can all have the same return type. (defined in `sysproc.c`).
> and then finally, the actual syscalls are defined in `proc.c` 


--- 
End of chapter 2.

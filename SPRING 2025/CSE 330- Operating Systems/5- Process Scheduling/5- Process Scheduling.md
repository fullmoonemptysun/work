![[5_(Process_Scheduling_Concepts_and_Algorithms).pdf]]

1️⃣ **User Requests Execution**

- When a user runs a program (`./a.out`), the shell calls `**execve()**` to start execution.

2️⃣ **Kernel Opens the ELF File**

- The kernel **opens the ELF binary file from disk** and reads its **ELF header**.
- This header contains:
    - Magic number (`0x7F ELF`)
    - Entry point address
    - Program headers (segments to load)
    - Section headers (symbols, debugging info)

3️⃣ **Memory Mapping (Using** `**mmap**`**)**

- The kernel **maps the ELF sections** (code, data, stack) into virtual memory.
- Segments in the ELF file tell the kernel **what to map where** (e.g., `.text → executable memory, .data → writable memory`).

and then finally, shifts control to the entry point of the program by changing rip

  

What happens during an I/O Burst exactly?

1️⃣**CPU sends an I/O request** (e.g., read from disk).

2️⃣**Device Controller takes over** and starts processing the request.

3️⃣**CPU moves on** to execute another process (doesn’t sit idle).

4️⃣ **Device completes I/O and raises an interrupt** .

5️⃣ **CPU handles the interrupt** and resumes the waiting process.

  

Each Process in the LL scheduling queue is a PCB process control block.

```C
struct PCB {
int pid;                // Process ID
int state;              // Process state (Ready, Running, Blocked)
int priority;           // Priority level
int program_counter;    // CPU instruction pointer (next instruction)
int registers[NUM_REGS]; // CPU registers snapshot
int memory_base;        // Base address of allocated memory
int memory_limit;       // Memory limit for the process
struct PCB *next;       // Pointer to next PCB in scheduling queue
};
```

  

Burst size prediction is only relevant in algorithms like FIFO, SJF and SRJF. These have pretty much no concept of time slices and jobs are intended to by finished after starting. Round robin and MLFQ (most current systems) do not have a Burst size prediction and simply follow the laws of the queue.

  

Where does the rip address for a process in scheduling come from?

- The **PCB (Process Control Block)** holds the process's **saved instruction pointer (RIP/EIP/PC)** when the process is not running.
- If the process is **resuming execution**, the PCB contains the **last saved RIP** from when it was last scheduled out.
- If the process is **starting for the first time**, the PCB will have the **entry point address**, which is typically set when the process is created (e.g., loaded from the ELF executable's header in Linux).
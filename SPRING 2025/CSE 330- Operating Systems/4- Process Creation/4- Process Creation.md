![[4_(Process_Creation).pdf]]

### **Process Creation & User Sessions**

### **1. First Process After Boot**

- Kernel starts `**init**` **(PID 1)** (`systemd` on modern systems).
- `init` starts **system services and user sessions**.

### **2. User Sessions**

- **GUI:** Login manager (`gdm`, `sddm`) → Starts `gnome-session`, `plasmashell`, etc.
- **CLI:** `login` starts a shell (`bash`, `zsh`).
- **Session keeps running** until the user logs out.
- Logging out **kills the session and all child processes**.

### **3. Process Creation (**`**fork()**` **&** `**exec()**`**)**

- `**fork()**` → Creates a new process (child of the parent).
- `**exec()**` → Replaces the child’s memory with a new program.
- **Clicking an executable**: GUI shell **forks & execs** the program.

### **4. Why Does** `**init**` **Always Run?**

- **First user-space process, PID 1**.
- Manages **system services, user sessions, and zombies**.
- If `init` dies, the **system crashes (kernel panic)**.

### **5. What Happens on Logout?**

- **Session process exits**, killing all child processes.
- Returns to **login screen (**`**gdm**`**,** `**sddm**`**)**.

### **6. Special Cases**

|   |   |
|---|---|
|**Event**|**What Happens?**|
|**Log Out**|Kills session & processes.|
|**TTY Switch**|Session keeps running.|
|**Suspend**|Session is paused, resumes on wake.|
|**Detached Process (**`**tmux**`**,** `**nohup**`**)**|Stays alive after logout.|

- User sessions are all processes. Init is the main root process for all processes.

  

**How does CPU go to kernel? : by looking at the syscall instruction!**

  

How does kernel control CPU?

The **CPU simply executes routines from the kernel's code** when it's in **kernel mode (Ring 0)**. The kernel **instructs the CPU** by:

1. **Executing CPU Instructions**
    - The kernel contains **low-level assembly and machine code** that directly tells the CPU what to do.
    - Example: Moving data, controlling memory, setting registers (`mov`, `cmp`, `jmp`).
2. **Configuring CPU Registers**
    - The kernel **writes to special CPU registers** to control behavior.
    - Example:
        - Setting **page tables** for memory management.
        - Configuring **CPU privilege levels** (Ring 0 vs. Ring 3).
3. **Issuing Privileged CPU Instructions**
    - Some CPU instructions **can only be executed in kernel mode**.
    - Example:
        - `**hlt**` (halts the CPU).
        - `**cli**` **/** `**sti**` (disable/enable interrupts).
        - `**sysret**` (return from syscall).
4. **Handling Interrupts & Syscalls**
    - The CPU runs **interrupt service routines (ISRs)** when an interrupt occurs.
    - On syscalls, the CPU jumps to **predefined kernel functions** (like `sys_read`, `sys_exec`).

  

  

How does context switch happen?

Kernel looks at an executable and reads it (ELF) moves it into the ram and initializes all it’s address stuff (heap, stack, data segments) and then sets up the registers (rsp, etc.) correctly and changes the IP or rip register to point to the start of the new process and CPU just starts executing. CPU does not know what process it is running.

  

Interrupts???

After an interrupt happens IRQ, a signal is sent to the CPU this changes the logic in a way that CPU starts executing kernel code, (looking in interrupt descriptor table for the right interrupt routine). Interrupt descriptor table is a superset of **trap table from system boot.** Trap table only entails routines for software based interrupts and exceptions.  
  
After the CPU has found the correct routine, it starts executing that (this is basically now the kernel running) and then according to these kernel interrupt routines, if a context switch will happen or not is decided and stuff is stored in PCB, if not, then process continues.  

  

Timer: how does it work?

The clock is usually a quartz based oscillator.

There are timer registers that are set to a value and are decremented at each oscillation of the clock.

How does the OS set the timers? well, it has an initial routine to set the timer value to something at boot. (eg. 1ms) and after that its all about interrupts. When a timer runs out, IRQ 0 is fired and we start looking at IDT table for the right interrupt, after that the **Kernel updates the scheduler**, chooses the next task from the queue, and **reprograms the timer dynamically** based on upcoming events.

**Bottom Line:** Above understanding is solid! Just remember that modern systems often use **APIC/HPET instead of IRQ 0**, and the OS **adjusts timers dynamically** instead of always resetting to a fixed interval.
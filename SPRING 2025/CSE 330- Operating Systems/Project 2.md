1. Modules extend the functionalities of a monolithic OS kernel like Linux.

  

# 1.1

### Linux Kernel Module (LKM)

- **Kernel Module**: A piece of code that extends kernel functionality without modifying the kernel itself.
- **Loading a Module**: Inserts the module into the kernel using `insmod my_name.ko`.
- **Unloading a Module**: Removes the module from the kernel using `rmmod my_name`.
- **Functions**:
    - `module_init()`: Runs when the module is loaded.
    - `module_exit()`: Runs when the module is unloaded.
- **Makefile**: Used to compile the module separately from the kernel source.
- **Commands**:
    - `lsmod` → Lists loaded modules.
    - `modinfo my_name.ko` → Shows module details.
    - `dmesg | tail` → Checks kernel log messages.

  

# 1.2

### **Kernel Logging (**`**printk()**` **vs.** `**pr_info()**`**) - Notes**

- `**printk(KERN_INFO, ...)**`
    - Used for logging messages in the kernel.
    - `KERN_INFO` specifies the log level (informational).
    - `printk(KERN_INFO "Hello, I am Vinnie, a student of CSE330 %s %d.", charParameter, intParameter);`
    - `%s` → String (`charParameter`), `%d` → Integer (`intParameter`).
    - Messages appear in kernel logs (`dmesg`).
    - **Log Levels (**`**KERN_***`**)**
        - `KERN_EMERG` → System is unusable.
        - `KERN_ALERT` → Immediate action needed.
        - `KERN_CRIT` → Critical conditions.
        - `KERN_ERR` → Error conditions.
        - `KERN_WARNING` → Warning messages.
        - `KERN_NOTICE` → Normal but significant.
        - `KERN_INFO` → Informational messages.
        - `KERN_DEBUG` → Debugging messages.

**Using** `**pr_info()**` **Instead**

- Equivalent to `printk(KERN_INFO, ...)`.
- Cleaner syntax, preferred in modern kernel development.
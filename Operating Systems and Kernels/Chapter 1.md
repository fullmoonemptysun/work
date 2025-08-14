# Introduction

1. User space processes see syscalls that the kernel provides as an interface to the hardware.
2. Remember 2 spaces: User space and Kernel Space. Two different privileges.
3. Xv6 is a subset of UNIX OS and mimics its internal workings
4. All processes mostly have memory containing: **Instructions, Data, Stack**
5. Read Notes on how a syscall enters the kernel when a process invokes it (IDT).
6. Process calls syscall, things enter kernel mode, the kernel finishes the service and returns (switch between user space and kernel space happens)

  

## 7. What happens after a process invokes a syscall

- `syscall` is a hardwired instruction in CPUs. As soon as it is encountered, CPU internally - has to look at Model specific registers (MSRs) and one of them (`IA32_LSTAR`) contains the address of kernel’s syscall handler (this is configed by the kernel itself to be like that)
- The CPU saves its next instruction pointer (rip) to rcx (hardcoded) and switches to kernel mode: This is done basically by changing the last 2 bits of CS (code segment) register which corresponds to the current privilege level (0 for kernel 3 for user).
- Then the CPU goes to the address specified by the MSR register and starts executing that routine.

  

1. Shell is a normal user space program that utilizes the power of system calls.

  

# 1.1 Processes and Memory

1. Process consists of a memory and its unique state (PCB) private to the kernel.
2. Context switch happens among the processes waiting to be executed
3. When process is not executing all register data is stored and restored when it executes again.
4. Every Process has its own PID.
5. `fork` syscall creates a new process. Gives the new process an exact copy of the calling processes’ memory, both instructions and data.
6. Original process: fork returns the PID of new proc. In the new proc. returns 0

  

## 7. System calls (in Xv6):  
  

**System call**  
int fork()  
int exit(int status)  
int wait(int *status)  
int kill(int pid)  
int getpid()  
int sleep(int n)  
int exec(char *file, char *argv[])  
char *sbrk(int n)  
int open(char *file, int flags)  
int write(int fd, char *buf, int n)  
int read(int fd, char *buf, int n)  
int close(int fd)  
int dup(int fd)  
int pipe(int p[])  
int chdir(char *dir)  
int mkdir(char *dir)  
int mknod(char *file, int, int)  
int fstat(int fd, struct stat *st)  
int stat(char *file, struct stat *st)  
int link(char *file1, char *file2)  
int unlink(char *file)  

**Description**  
Create a process, return child’s PID.  
Terminate the current process; status reported to wait(). No return.  
Wait for a child to exit; exit status in *status; returns child PID.  
Terminate process PID. Returns 0, or -1 for error.  
Return the current process’s PID.  
Pause for n clock ticks.  
Load a file and execute it with arguments; only returns if error.  
Grow process’s memory by n bytes. Returns start of new memory.  
Open a file; flags indicate read/write; returns an fd (file descriptor).  
Write n bytes from buf to file descriptor fd; returns n.  
Read n bytes into buf; returns number read; or 0 if end of file.  
Release open file fd.  
Return a new file descriptor referring to the same file as fd.  
Create a pipe, put read/write file descriptors in p[0] and p[1].  
Change the current directory.  
Create a new directory.  
Create a device file.  
Place info about an open file into *st.  
Place info about a named file into *st.  
Create another name (file2) for the file file1.  
Remove a file.  

  

  

  

1. If not otherwise stated, these calls return 0 for no error, and -1 if  
    there’s an error.  
    
2. `wait((int*) 0)` returns the PID of the first child process that exits successfully. If none have yet, it waits for one to do so. If there are no children, immediately return -1.
3. It also stores the exit status of the child in the int pointer provided to the function. Can just be 0 if we dont care about the status.
4. `exec` syscall replaces the calling processes memory with a new memory image loaded from an ELF file (that specifies which part has instructions, which part is data, which instrcution to start at, etc.)
5. When exec succeeds, it does not return to the calling program, instead the new instructions loaded from the file start executing at whatever the entry point is in the ELF header.
6. `exec` takes 2 args: name of the file containing executable and an array of string arguments.
7. Basically replaces the calling program with an instance of the program in the argument with the argument list (string list) as arguments to the new program.
8. “Xv6 allocates most user-space memory implicitly: fork allocates the memory required for the  
    child’s copy of the parent’s memory, and exec allocates enough memory to hold the executable file. A process that needs more memory at run-time (perhaps for malloc) can call sbrk(n) to grow its data memory by n bytes; sbrk returns the location of the new memory.”  
    

  

# 1.2 I/O and file descriptors

1. File descriptor is an integer representing a kernel managed object(not a region in the memory) that a process may read from or write to.
2. A file descriptor can represent a file, a directory, a device, etc.
3. This is basically a struct instance under the hood with many fields.
4. File descriptors basically abstracts away the differences between files, devices and pipes, making them all look like streams of bytes.

  

1. Xv6 uses fd as indices in per process fd table. So every process basically has their own fds starting from 0.
2. Conventionally, a process reads from fd = 0. (stdin), writes to fd=1 (stdout) and writes error messages to fd = 2 (stderr).
3. These 3 are created by default as a routine by the OS before the process is actuall run.
4. The shell ensures that it always has three file descriptors open (user/sh.c:152)
5. `read` and `write` syscalls read and write bytes from and to open files named by fds

  

1. `read(fd, buf, n)` : read at most n bytes from the file descriptor fd and copy them into buf. Return number of bytes read.
2. Every fd has an offset associated with it. Each read increases the offset by the number of bytes read. At the next read, starts from the new offset. When nothing remains, read returns 0 to indicate EOF.
3. `.read` is an operation (function) defined in any file type (device, file, directory) and so when read() is called, it ultimately calls this .read operation of the respective fd. (ex: the driver’s .read for a USB device)
4. Can check basic `cat` implementation on page 13-14.
5. `close` syscall releases a fd, and makes it free for future reuse. Newly allocated fd is always the lowest unused descriptor of the process.
6. `fork` copies parent’s fd table so all those files are open even in child
7. `exec` loads new program but keeps the original fd table going.

  

  

```C
//simplified version of the code a shell runs for the command cat < input.txt:
char *argv[2];
argv[0] = "cat";
argv[1] = 0;
if(fork() == 0) {
close(0);
open("input.txt", O_RDONLY);
exec("cat", argv);
}
```

Child closes stdin so when child shell process opens input.txt it gets fd = 0. then runcmd does exec, the cat program is loaded with fd = 0 pointing to input.txt so input.txt becomes stdin for cat. And so this is file redirection.

Sidenote: Passing input.txt in argv would also print out the same thing, just the inside working is different clearly.

  

  

1. The second argument to open() is a flag that controls data I/O for that fd.
2. Defined in fcntl header (kernel/fcntl.h)
3. O_RDONLY, O_WRONLY, O_RDWR, O_CREATE, and O_TRUNC, which instruct open to open the file for reading, or for writing, or for both reading and writing, to create the file if it doesn’t exist, and to truncate the file to zero length.
4. Even though child process copies fd table, the offset is shared for them between parent and child. Child continues where parent left off, parent continues where child leaves.
5. `dup` syscall duplicates an fd and points to the same underlying object. The offsets are also preserved just like in fork parent child.

  

1. Otherwise, fds don’t share offsets even if they are opened for the same file object.

```C
ls existing-file non-existing-file > tmp1 2>&1
```

1. Code analysis: shell sees the 2>&1 and does `close(2); dup(1)` and so stderr is also stdout.  
    now because of the line before (…. > tmp1) tmp1 has become stdout. So stderr also points to tmp1 and so the output for  
    `existing-file` and the error for `non-existing-file` both are written to tmp1.

  

1. The xv6 shell does not support redirection for the error fd. But now we know how to implement it.

  

1. Fds are very powerful because they abstract what actual object is being written to.

  

# 1.3 Pipes

1. Pipe is a buffer (a simple part in kernel memory when it is created, typically 4kb stores data).

```C
int fd[2]
pipe(fd) //syscall
```

creates a pipe buffer. fd[0] is the read fd and fd[1] is the write fd

  

1. Provides a way for IPC (inter process communication)

  

```C
int p[2];
char *argv[2];
argv[0] = "wc";
argv[1] = 0;
pipe(p);
if(fork() == 0) {
	close(0);
	dup(p[0]);
	close(p[0]);
	close(p[1]);
	exec("/bin/wc", argv);
} else {
	close(p[0]);
	write(p[1], "hello world\n", 12);
	close(p[1]);
}
```

runs `wc` with stdin connected to the read end of a pipe.

- creates int array of 2 that must be fed into pipe() syscall. This syscall then gives 2 fds (read and write for the pipe)
- Gets the wc ready (wc as the first argv then 0 to represent end of argv)
- calls pipe(p) with the array created above.
- Child shell closes stdin and dup’s the read side of pipe and so 0 is allotted. Now stdin is p[0]
- Child closes read side of p, also closes write side of p to free p.
- executes wc. `wc` somewhere inside its code calls a read from stdin and ultimately reads from the reading side of pipe (p[0]).
- If there is nothing written there, calling read will **wait** until something is written or until all the write fds to the pipe are closed.
- Parent closes its read fd to p and then writes to the pipe. Then closes write side.
- The child then reads until EOF and then does whatever wc does before closing down.
- The child must close p[1] because if it stays open, wc will run forever (unless wc `wc` closes p[1] in its code. (because if a fd in wc refers to p[1] it will keep writing and reading infinitely.

  

  

1. How the shell implements piping for stuff like `grep fork sh.c | wc -l`:

```C
  case PIPE:
    pcmd = (struct pipecmd*)cmd;
    if(pipe(p) < 0)
      panic("pipe");
    if(fork1() == 0){
      close(1);
      dup(p[1]);
      close(p[0]);
      close(p[1]);
      runcmd(pcmd->left);
    }
    if(fork1() == 0){
      close(0);
      dup(p[0]);
      close(p[0]);
      close(p[1]);
      runcmd(pcmd->right);
    }
    close(p[0]);
    close(p[1]);
    wait(0);
    wait(0);
    break;
```

  

1. the right end may actually have pipes in themselves and thus break into 2 forks themselves and so on
2. Pipes have the following advantages over something like
    
    `grep fork sh.c > /tmp/xyz; wc -l </tmp/xyz` :
    
    1. The xyz file needs to be carefully removed.
    2. we can pass arbitrarily long data to pipes, but in case of temp files, we must have enough disk space.
    3. In temp files method, one command has to finish for the next to start, meanwhile, in pipes the commands are executing in parallel (even if the right has to wait until the left side writes).

  

# 1.4 Filesystems

1. File system is a software construct. It is a way to organize a disk.
2. Implemented in C mostly (structs like (inodes, superblocks, etc.)).
3. It is created when a disk is formatted.
4. Implemented using **C structs** (inodes, superblocks, etc.).
5. Detected and mounted by the kernel at boot via file system drivers.  
      
    
6. If there are multiple fs on a disk, they’re just in different partitions (Remember C:, D:, E:) They are all different file systems, either on different partitions or different drives altogether.
7. **PARTITIONING:** Divides the drive into logical sections. (MBR and GPT) are two partitioning schemes. Puts metadata, drive info, et cetera
8. FS in xv6 contains : i) Data files (just uninterpreted byte arrays) and ii) Directories (which contain references for other dirs and data files)
9. Paths that don’t start at / (root) are evaluated relative to the calling process’s current directory, which is changed with the `chdir` syscall.
10. `mkdir` creates a new directory, `open` with O_CREATE flag creates a new data file and `mknod` creates a new device file.
    
    ```C
    mkdir("/dir");
    fd = open("/dir/file", O_CREATE|O_WRONLY);
    close(fd);
    mknod("/console", 1, 1);
    ```
    

  

1. `mknod` creates a special file that refers to a device. It is associated with its major and minor device numbers (look above, the 2 arguments.)
2. These numbers uniquely identify a kernel device.
3. When a process later opens a device file, the kernel diverts `read` and `write` calls to the kernel device implementation instead of passing them to the file system itself. (remember `.read` of each file type from file descriptors?)

  

1. A file name is distinct from the file itself. These names are just links to the underlying actual inodes (strcuts) representing files. Obviously an inode can have multiple names (links)
2. Each of these links consists of an entry in a directory. This “Entry” contains a file name and a reference (pointer?) to an inode.
3. **Inode:** Holds metadata about a file, including file type (file, dir, or dev); its length and its location in disk, and number of links pointing to it.
4. `fstat` syscall gets info from an inode that an fd refers to (fills a `struct stat` defined in `kernel/stat.h` as:
    
    ```C
    \#define T_DIR 1
    \#define T_FILE 2
    \#define T_DEVICE 3
    
    
    struct stat {
    int dev; // File system’s disk device
    uint ino; // Inode number
    short type; // Type of file
    short nlink; // Number of links to file
    uint64 size; // Size of file in bytes
    };
    ```
    

  

1. The `link` syscall creates another file system name referring to the same inode as an existing file:

```C
open("a", O_CREATE|O_WRONLY);
link("a", "b")
```

  

1. each inode has a unique inode number.
2. The `unlink` syscall is to remove a name from the FS. File’s inode and the disk space holding its content are only freed when link count becomes 0 and no fds refer to it:

```C
unlink("a");
```

  

  

```C
fd = open("/tmp/xyz", O_CREATE|O_RDWR);
unlink("/tmp/xyz");
```

Creates an inode with no name, which is cleaned when the process closes the fd or exits.

  

1. `cd` is a program that is built into the shell itself unlike separate programs like `ls, ln, rm`. This is because when we call cd (`chdir` is called under the hood) we want the working directory of the shell to change. If it were a separate process, then as we know, a child would be forked and it’s working directory would be changed.

```C
 while(getcmd(buf, sizeof(buf)) >= 0){
    if(buf[0] == 'c' && buf[1] == 'd' && buf[2] == ' '){
      // Chdir must be called by the parent, not the child.
      buf[strlen(buf)-1] = 0;  // chop \n
      if(chdir(buf+3) < 0)
        fprintf(2, "cannot cd %s\n", buf+3);
      continue;
    }
    if(fork1() == 0)
```

implementation of `cd`: we can see that if `cd` is called, we do not fork and run it in the parent shell process. `chdir(const char* path)` and so buf+3 is just passing the substring starting at buf + 3.

  

# 1.5 Real World

1. The idea Unix goes by is “Resources are files”. This is transformative and very important and useful. However for network, graphics, etc.. most unix derived Kernels have not followed this moto.
2. All xv6 processes run as root. Xv6 does not have a notion of user privilege.

  

---

  

# Exercise Notes

Just have two pipes with two while loops in a parent-child combination. Thats all
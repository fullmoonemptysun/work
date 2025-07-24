> [!important]
> 
> ### System calls in xv6
> 
> int fork() Create a process, return child’s PID.  
> int exit(int status) Terminate the current process; status reported to wait(). No return.  
> int wait(int *status) Wait for a child to exit; exit status in *status; returns child PID.  
> int kill(int pid) Terminate process PID. Returns 0, or -1 for error.  
> int getpid() Return the current process’s PID.  
> int sleep(int n) Pause for n clock ticks.  
> int exec(char *file, char *argv[]) Load a file and execute it with arguments; only returns if error.  
> char *sbrk(int n) Grow process’s memory by n bytes. Returns start of new memory.  
> int open(char *file, int flags) Open a file; flags indicate read/write; returns an fd (file descriptor).  
> int write(int fd, char *buf, int n) Write n bytes from buf to file descriptor fd; returns n.  
> int read(int fd, char *buf, int n) Read n bytes into buf; returns number read; or 0 if end of file.  
> int close(int fd) Release open file fd.  
> int dup(int fd) Return a new file descriptor referring to the same file as fd.  
> int pipe(int p[]) Create a pipe, put read/write file descriptors in p[0] and p[1].  
> int chdir(char *dir) Change the current directory.  
> int mkdir(char *dir) Create a new directory.  
> int mknod(char *file, int, int) Create a device file.  
> int fstat(int fd, struct stat *st) Place info about an open file into *st.  
> int stat(char *file, struct stat *st) Place info about a named file into *st.  
> int link(char *file1, char *file2) Create another name (file2) for the file file1.  
> int unlink(char *file) Remove a file.  
> **If not otherwise stated, these calls return 0 for no error, and -1 if**  
> **there’s an error.**  
>   

1. **Time sharing:** Xv6 **time-shares** processes: it transparently switches the available CPUs  
    among the set of processes waiting to execute.  
    
2. The kernel associates a process identifier, or PID, with each process.
3. A process may create a new process using the fork system call. fork gives the new process  
    an exact copy of the calling process’s memory, both instructions and data. fork returns in both  
    the original and new processes. ==In the original process, fork returns the new process’s PID. In the new process, fork returns zero.== The original and new processes are often called the parent and child.
4. Execution in the child process does not restart at beginning of instructions, it continutes after the fork call in both parent and child.

  

```C++
int pid = fork();
if(pid > 0){
	printf("parent: child=%d\n", pid);
	pid = wait((int *) 0);
	printf("child %d is done\n", pid);
}else if(pid == 0){
	printf("child: exiting\n");
	exit(0);
}else {
	printf("fork error\n");
}
```

1. `wait(int*status)` : the kernel takes the status code from the exit and puts in the address _status._ it can be retrieved by simply doing *status.
2. If a parent has multiple children that exit the PID wait returns is of the child that gets reaped first by the OS (depends on scheduling and not the order of creation).
3. If none of the children have exited, wait just waits for one to do so.
4. If the caller has no children, wait immediately returns -1.
5. If the parent doesn’t care about the exit status of a child, it can pass a 0  
    address to wait and it will just wait for a child to exit.
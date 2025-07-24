  

1. `find` command searches under the directory for the file after -name argument as:

```Bash
find directory -name flag
```

looks for files/directories called `flag` under the `directory` directory (the full filesystem).

will return the whole file system if nothing is specified.

  

1. `ln` combined with `-s` option is used to create symbolic links/symlinks.
    
    _**symlinks:**_ A **soft** link is when you move appartments and have  
    the postal service automatically forward your mail from your old place  
    to your new place.  
    
    ```Bash
    ln -s originalfilepath slinkpath
    ```
    
    _**hard links:**_ A **hard** link is when you address your apartment using multiple addresses that all lead directly to the same place (e.g., `Apt 2` vs `Unit 2`).
    
      
    

  

1. `file` helps us know the type of file something is. it will recognize links.

  

1. when looking at the `man` of a program, we can use `/` to start a search and then `n` to go to the next match and `N` to go to the previous one. If we use `?` instead of `/` search starts from the end

  

1. can use `help` command to get details about builtin programs instead of man or `--help`.

  

1. **GLOBING & EXPANSIONS:**
    1. `*` corresponds to multiple characters
    2. `?` corresponds to one character
    3. `[abc]` will match a, b and c only:
        
        `/challenge/run file_[bash]`
        
    4. add a ! before the content in [] to invert it and exclude those characters.

  

1. I/O in shell:
    
    1. File descriptor number is a number that determines I/O channel in linux.
        1. FD0 : stdin
        2. FD1 : stdout
        3. FD2 : stderr
    2. `>` by itself is equivalent to `` ` 1 > ` ``
    3. `>&` redirects from one channel to another
    
      
    
    d. The `tee` command, named after a "T-splitter" from _plumbing_ pipes, duplicates data flowing through your pipes to any number of files provided on the command line.  
    For example:  
    
    ```Shell
    hacker@dojo:~$ echo hi | tee pwn college
    hi
    hacker@dojo:~$ cat pwn
    hi
    hacker@dojo:~$ cat college
    hi
    hacker@dojo:~$
    ```
    

e. Can refer to the stdin/stdout of a command that takes in something by doing `>(command)/<(command)` this can be used as a file with commands that take files as inputs

```Shell
echo HACK | rev
>>> KCAH 
echo HACK | tee >(rev)
>>> HACK
>>> KCAH


EXAMPLE: 

echo hi | rev \#is the same as
echo hi > >(rev)
```

  

  

# LL- II

  

1. Can have variables like a programming language

- No data types
- variables can hold numbers, chars and strings

1. Can print value of variables by
    
    `echo $variablename`
    
    appending $ before the variable name
    

  

1. Can create variables like:
    
    `Varname=Value` (no spaces allowed)
    
2. Any spaces that are not inside quotes will terminate variable assignment
    
    and take inputs as commands.
    

  

1. can invoke a child shell process by using `sh`
2. to export variables to other processes, we must declare it as export like:
    
    `export VAR=VAL`
    

  

1. `env` prints out every exported variable in the shell

  

1. can read the output of a command into a variable by
    
    `VAR=$(CMDSTATEMENT)`
    
    or `` VAR=`CMDSTATEMENT` ``
    
      
    
2. can read inputs to Variables by:
    
    `read -p prompt var`
    
    or `read var`
    

  

1. `ps` gives us the running processes.
2. PID or process id is a unique identifier for a process

  

1. it can be run in the following different ways
    
    ```Shell
    ps -ef # -e lists every process and -f gives a full format output
    
    ps aux # a to list all users' processes , x to list the ones that are 
    \#not running in a terminal, and u for a user readable output. 
    ```
    

  

13 Default way to kill a process:

`kill pid`

  

1. We can suspend processes for future continuation by using ctrl + z
    
    can resume suspended processes using the `fg` command by itself. (builtin)
    
    can also resume in the background by using the `bg` command.
    
      
    
2. invoking `fg` also foregrounds any background process

  

1. To start a program in background, we just have to use the `&` after it like:
    
    `sleep 1971 &`
    
      
    
2. Commands in the shell usually end with an exit code. This can be grabbed from the variable ? right after a command has finished running. Like:
    
    `echo $?`
    
      
    
      
    

# LL - III

  

1. using the -l argument with the ls command will tell us about file permissions
2. the output is in a format like:
    
    `filetype owner-read write exe group-read write exe allelse-read-write-exe`
    
      
    
3. we can use `chown` to change a files owner, only if we are the **root user** like:
    
    `chown user file.`
    

  

1. File sharing is done within Linux among users in a group.
    - to know what group i am part of, we have to type the id command.
    - Group ownership over system resources is done in the form of ownership of files
        
        that correspond to different device/component files, ex: A file for graphical input
        
          
        

  

1. `chgrp` can be used to change group ownership:
    
    `chagrp groupname filename`
    

  

1. r - read the file or ls the directory
    
    w - can modify the file or create/remove files in the directory
    
    x - can execute the file or can enter the directory using cd.
    
    - - nothing
    

  

1. We can use `chmod` to change permissions: 
    
    ```Shell
    chmod ugo +/- rwx filename  # u means owner, g means group, o means other
    														# +/- mean add/remove permission
    														# and rwx imply what permissions.
    ```
    

  

1. In addition to adding and removing permissions, as in the previous level, `chmod` can also simply set permissions altogether, overwriting the old ones.  
    This is done by using  
    `=` instead of `-` or `+`.  
    For example:  
    - `u=rw` sets read and write permissions for the user, and wipes the execute permission
    - `o=x` sets only executable permissions for the world, wiping read and write
    - `a=rwx` sets read, write, and executable permissions for the user, group, and world!

  

1. If we want to set different permissions for different users, we can chain multiple modes with commas (,) like:
    
    `chmod u=rwx,g=rw,o=r`
    
    can set a permission to `-` to remove it or set it to nothing.
    

  

1. when we add `s` instead of `x` for a file, we give access to that user group as if they owned the file. We must be the original owners of the file though.

  

  

# Untangling Users

  

1. An old, almost **obsolete** command is `su` used to change the user and often raise privileges.
    - will ask for a password for the user that you’re trying to sign in as
    - has the SUID bit set so runs as root

  

1. usage:
    
    ```Shell
    su   \#will switch to root
    su USER \#will switch to the USER specified
    ```
    
    - When we enter a password for `su`, it compares it against the stored password for that user. These passwords _used_ to be stored in `/etc/passwd`, but because `/etc/passwd` is a globally-readable file, this is not good for passwords, these were moved to `/etc/shadow`.

  

1. we can use `john` johntheripper, to crack hashed passwords from a potential data leak of /etc/shadow

  

1. Unlike `su`, rather than authenticating via password, `sudo` relies on policies that it checks to determine the user's authorization run things as root.  
    These policies are defined in  
    `/etc/sudoers`, and though it's mostly out of scale for our purposes, there are plenty of [resources](https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file) for learning about this!

  

1. We can run successive commands after one another by chaining them with `;` .

  

1. when the string of commands to be ran is long, we can simply write shell scripts to run them.
    
    ```Shell
    touch x.sh \#scripts usually end in .sh
    echo "command list" > x.sh \#can also be written with a text editor
    bash x.sh \#to run the script. Tells the shell to take x.sh as input instead of stdin
    ```
    

  

1. can eliminate the need to manually use bash every time by making a .sh file executable like:
    
    ```Shell
    touch x.sh \#scripts usually end in .sh
    echo "command list" > x.sh \#can also be written with a text editor
    chmod ugo+x ./x.sh
    ./x.sh \#will run the script
    ```
    

  

# Pondering PATH

  

1. A shell usually has a PATH variable that contains the directories for many programs such as ls and rm.

  

1. If this is lost, there will be troubles.

  

1. Can add the path to our programs such as scripts to the PATH variable to be able to invoke them by just their names (instead of the full directory address)
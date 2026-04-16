
1. Start debugger with gdb (name of binary)
2. Start running the program with `r(un) args`

3. x is examine, use it with x/i reg to say the reg is pointing to an instruction
4. can do `x10i, x20i....` to get the next 10 instructions at register.
5. p
6. p reg prints the values of the reg
7. info reg lists all the regs with their values


8. When the length of a code segment is not known (function) just use:
```
disassemble fun_name
```

9. x/ variations for data
```asm
x/4gx $rsp # get 4 giant hex nums at sp

x/4u $rsp #get 4 unsigned ints at sp
x/4d $rsp #get 4 signed ints at sp
x/a $rsp #examine the address contained at the memory location pointed to by rsp and resolve symbol name of that address if known


the last one can also be acheived using p (print can only do 1 examination at a time)

p/a *(long *) $rsp #deference the address in $rsp and cast to see the address at that address.


doing p saves the value for later use in a variable
that can be printed like this: printf "%lx\n", $7
```

10. we can step to the next instructions inside the same block using `si` or `ni`.

11. The difference between these is that one of them will not step through any functions that are called from the current block and then other will, (go exactly line by line as the program executes).

12. we can do `display/a $rip` to display something we want to check every si/ni
*Example:*

```
(gdb) display/4i $rip
3: x/4i $rip
```
![[Pasted image 20250830010651.png]]

13. `finish` finishes the current running function 
14. info break gives us all the breakpoints
15. Can set breakpoints at exact address also
16. use `c` to continue after a breakpoint
17. User defined variables are possible in the following way:
  ```
    set $my_var = $rsp (set my_var to the value in rsp)
    set $my_var = *(long *) $rsp to the value pointed to rsp
```



18. Just as we can set the values of user defined variables during runtime in the debugger, we can also change the values in the registers and in the memory as well!:
```
set *(long *) $rip = 0x13333337
```

This can definitely break the program

19. Scripting is possible in gdb using the same commands we use in normal debugger:

*my_script.gdb*: run using `gdb -x scriptfile binary`
```
break *main # breakpoint at main
commands # do these commands everytime we hit this break point
	p $rip
end
run vinnie #run the program
continue #continue

```


20. gef is a plugin for gdb that makes it more informative but very overwhelming
21. 
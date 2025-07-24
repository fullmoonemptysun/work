- Example C code:  
    `printf("hehe\n");`
- Compiler stores `"hehe\n"` as bytes (`68 65 68 65 0A 00`) in `.rodata` section labeled `.LC0`

- Assembly snippet:

```asm
lea rdi, [rip + .LC0]   ; Load address of string into rdi   
call printf             ; Call printf with string address .section .rodata   

.LC0:     
.string "hehe\n"       ; String bytes stored here
```


- `.LC0` is a symbolic label representing the string’s memory address
- Assembler encodes `lea` instruction with a relative offset from RIP to `.LC0`
- Linker fixes the offset based on final executable layout
- At runtime, CPU computes address by adding RIP and offset, loads it into `rdi` to pass to `printf`


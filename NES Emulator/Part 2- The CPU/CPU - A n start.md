1. We have a 16 by 16 matrix of opcodes and instruction. The first byte's lower 4 bits represent the column in the matrix and the upper 4 bits represent the row.

2. The blank spots in the datasheets are all illegal opcodes. CPU will still do something but not something it was designed to do officially.

3.  ```cpp
   //first we did this
   
   struct INSTRUCTION{
	   string name;
	   uint8_t (wit6502::*operate)(void) = nullptr
   }
   
   
   //it expects a function pointer obviously.
   //When we define a member function IMM(), IMM is basically a pointer to the function
//so we pass as &a::IMM
/**
Basically, & in the table is not reference. It is the address of operator. It basically gets the address of a::IMM and assigns it to the pointer 'operate'
*/

//it is called like:

(this->*lookup[opcode].addrmode)() 
//inside the clock() function.


   
   ```

4. The clock function looks like this:
```cpp
if(cycles == 0){
	opcode = read(pc)
	pc++
	
	cycles = lookup[opcode].cycles
	(this->*lookup[opcode].addrmode)() 
	(this->*lookup[opcode].operate)()
	
}
```
---
I decided that i will implement all of this myself. 


1. Why do we need clock? or cycles? what is their purpose?
	A: To synchronize all the components of the computer with the processor. If we did not have a clock or its cycles, signals would arrive at different components at different times.


## Addressing modes in the 6502 

>[!warning] Reminder: Indirect addressing 
>Basically memory[address] contains the main address



| Abbr  | **Addressing mode** | **Formula**                                               | **What it does**                                                                                                                                                                                                                                            | **Cycles** |
| ----- | ------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- |
| d,x   | Zero page indexed   | Peek((arg + X) % 256)                                     | all addresses are withing the 00 - FF range and so form an index inside the 0 page (the first page) which is 256 bytes in 6502. This provides faster access since address is only 8 bits instead of 16                                                      | 4          |
| d,y   | Zero page indexed   | Peek((arg + Y) % 256)                                     | Same as above but with Y register                                                                                                                                                                                                                           | 4          |
| a,x   | Absolute indexed    | Peek(arg + X)                                             | Simply add register X's value to the provided number to get the final address.                                                                                                                                                                              | 4+         |
| a,y   | Abs indexed         | Peek(arg + Y)                                             | Same as above but with Reg. Y                                                                                                                                                                                                                               | 4+         |
| (d,x) | Indexed indirect    | Peek(Peek((arg + X)%256) + Peek((arg+X + 1) % 256) * 256) | Both bytes of the address are stored in addresses in the zero page. we peek for the lower byte at (arg + X) % 256 and (arg + X + 1) % 256 for the higher byte. Multiplying by 256 shifts the higher byte 8 bits and both are added to form the full address | 6          |
| (d),y | Indirect indexed    | PEEK(PEEK(arg) + PEEK((arg+1) % 256) * 256 + Y)           | Basically the reverse of above, indirect address from zero page is calculated first and the index is added from the register later.                                                                                                                         | 5+         |
- `$deadbeef` is hexadecimal notation in 6502 lingo, `%` is used for binary

>[!important] Note on `arg`
>It is important to note that value of `arg` is `$FF` at max in all indexed addressing modes except absolute addressing. That is basically how indirect indexed addressing guarantees that the first peek will be in zero page aswell. 

### Other simple addressing modes


| Abbr  | Name        | Description                                                                                                                                                     |
| ----- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|       | Implicit    | Destination of results are implied and addressing mode is not really relevant for operands here. EX: `RTS (return from subrouting) and CLC (set carry or smth)` |
| A     | Accumulator | Instructions can operate on the accumulator, e.g. LSR A                                                                                                         |
| #v    | Immediate   | Use the 8 bit value provided itself                                                                                                                             |
| d     | Zero page   | Fetches value from an 8bit address on the 0 page                                                                                                                |
| a     | absolute    | use the 16 bit address to fetch from anywhere in the memory                                                                                                     |
| label | Relative    | branch instructions and jumps have a relative offset operand from the current PC (8 bit)                                                                        |
| (a)   | Indirect    | Find the actual jump address at the 16 bit pointer operand.                                                                                                     |


## What exactly is the FLAGSTAT Enum doing?
>[!important]
>On the 6502, all CPU status flags live in a single byte (the `P` register), and we represent each flag as a **bitmask enum** so we can manipulate individual bits efficiently, just like real hardware; for example: 
>
>```cpp
>enum FLAGSTAT { C = 1<<0, Z = 1<<1, I = 1<<2, B = 1<<4, U = 1<<5, V = 1<<6, N = 1<<7 };
uint8_t status = 0;                    // actual CPU status register
void setFlag(FLAGSTAT f, bool v) {     // set or clear a flag
>    if(v) status |= f; else status &= ~f;
}
uint8_t getFlag(FLAGSTAT f) {          // read a flag
>    return (status & f) ? 1 : 0;
>}
>
>// Usage:
setFlag(C, true);   // turn Carry on
setFlag(Z, false);  // turn Zero off
bool negative = getFlag(N);
>```
>**Key idea:** the enum values are just **bit masks**, `status` is the actual storage, and all flag operations are **bitwise**, exactly imitating how the CPU stores and manipulates flags in one byte.
>
>- We could have used variables instead of the enum to store these bit masks but having them as enums provide a type safety and localization? (FLAGSTAT)


## My mental flow of how instructions will be executed:

**Step 1** : CPU grabs the opcode byte at PC (The clock function has some relevant logic to implement this. look at it later.)
**Step 2:** The corresponding `baseInstruction()` function is called immediately using the Instruction struct
**Step 3:** This `baseInstruction()` in turn calls the `addrMode()` function or the fetch() function depending upon the type of operand it is working on (decided by addrMode). For all 16 bit operands the addrMode() handles all the logic, while for all 8 bit operands, `fetch` is the intermediate function that is called and puts the final 8 bit value into the fetched `variable`

>[!important] addrMode function reference:
imm = #$00  
zp = $00  
zpx = $00,X  
zpy = $00,Y  
izx = ($00,X)  
izy = ($00),Y  
abs = $0000  
abx = $0000,X  
aby = $0000,Y  
ind = ($0000)  
rel = $0000 (PC-relative)

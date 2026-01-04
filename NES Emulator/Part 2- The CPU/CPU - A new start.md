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

I decided that i will implement all of this myself. 


1. Why do we need clock? or cycles? what is their purpose?
	A: To synchronize all the components of the computer with the processor. If we did not have a clock or its cycles, signals would arrive at different components at different times.
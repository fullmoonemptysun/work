  
> [!NOTE] C++ Notes about how to define class and its members between `.h` and `.cpp` files
> 
>- Non-static data members must be declared and optionally initialized inside the class definition in the header file.  
>- They cannot be defined or set outside the class (such as in the .cpp file).  
>- Non-static member functions can be declared in the header and implemented in the .cpp file.  
>- Non-static member functions can access and modify non-static data members.  
>- To access non-static members from outside the class, you need an object instance.  
>- Static data members are shared across all instances and must be defined outside the class, usually in the .cpp file.  
>- Static member functions can only access static data members unless given an object to work with.





![[image 56.png|image 56.png]]

  

1. When cpu sets address of the bus it expects devices to respond.

![[image 1 10.png|image 1 10.png]]

  

  

1. When CPU outputs address 0x3000, first device is sensitive to it and responds with some data

![[image 2 11.png|image 2 11.png]]

  

1. For this part, we take this von neuman example:
    
    ![[image 3 11.png|image 3 11.png]]
    

  

1. CPU structure:

![[image 4 9.png|image 4 9.png]]

1. Status Register: State of the CPU: Last was zero?, carry?
2. Program Counter: 16bits counter
3. Stack Pointer: 8 bits
4. PC is not incremented each cycle, because instruction length differs and some insctructions take multiple cycles to execute.
5. 56 legal instructions: The first byte of the instructions provide:
    
    1. Size
    2. Duration
    
    `LDA 0x41` : 2 bytes of data
    
    `LDA $0105`: 3 bytes of data
    
    `CLC`: 1 byte
    

  

1. To conclude, an instruction has a function, address mode, and cycles. Like mips has opcode and functions

  

1. Sequence of events we have to program:

- Read Byte @PC
- OPCODE[BYTE] → Addressing mode and Cycles
- Read remaining of instruction from PC
- Execute
- Wait, count cycles until instruction complete

  

1. 6502 has 16 bits address length, which means 65000 different addresses. Each of these addresses correspond to a byte in the memory.

  

1. All data is 8 bit on this processor

  

1. `for (auto &i : arr)` iterates over the array and gives i a direct reference to the elements. (not the address but the elements themselves).

  

2. We need the `<cstdint>` library to get generic unsigned 8, 16, 64, 32 bit int types.

  

3. We declare everything in the header file and implement everything in the cpp file

  

4. We create a bus class, and a CPU class that will work together. The ram was implemented as an array with 8bit ints (data) and the bus has members:
    1. Read method, write method
    2. Devices: RAM, CPU

  

5. **Forward declaring a class before another class in a file vs. including the header file:**
    
    1. Forward declaration only tells the compiler that a class exists. Nothing else about the class’s members is known
    2. including header files basically brings the whole implementation.
    3. No need for forward declaration if already including.
    4. We did the forward declaration in the header file, because we did not include anything in there. That way, when we use the bus in the cpu header file, it will understand that it is a class. The implementaion can be found in the cpp file.
    
> [!important] **THIS ALSO PREVENTS CIRCULAR INCLUSION**
> In C/C++, circular inclusions refer to a situation where two or more header files include each other directly or indirectly, leading to a loop. This can cause compilation errors, as the compiler ends up trying to repeatedly process the same files.
    

  

6. The status register(flags) is implemented as an enum with each flag corresponding to some significant bit in a bitmask: bunch of bits where each corresponds to a flag.

7. CPU parameters are the registers, pc and stack pointer.
8. Methods for instructions include include:
- Methods to set and get flags
- Methods to implement addressing mode
- Methods for the instructions themselves.

  
$$
1. For the flag enums, we do C = (1 <shiftrightshiftright>  0) and not C = 0b0001 for accuracy and readability.
$$

  

2. **DECIMAL MODE:** BCD binary coded decimal operations. Each 4 bit nibble represents one digit of the number. Greatly reduces the total possible numbers.

  
3. The standard interrupts (irq()) can be disabled based on if the iterrupt flag is set or not. The non maskable ones cannot be disabled.

  
4. In C/C++ when we do: `int func(){...};` this means that the function takes unspecified number of parameteres (not 0) like in java or python. This may lead to execution of the function anyway with unknown parameters (1, 1000 or even million of them) in C and undefined behavior in C++

5. We are adding a disassembler to see the assembly instructions as instructions get executed in the compilation. We have the INSTRUCTION struct for that.

6. using a = someClass or Struct: is a new syntax for typedef. It basically creates an alias for a class or data type.
7. &a :: func means find the address of the function func in the a class.

  

8. Instruction lengths are variable. Data is a byte. Addresses are 2 bytes.


```cpp
/*
Converts raw hex numbers such as 0x1bcd to hex strings "1bcd"
The [] takes any environment parameters (like events in javascript)
*/

auto hex = [](uint_32 n, uint_8 d){ //n is number to be converted-d is no. of hex digits
				std::string s(d, "0"); //create a string of length d with 0s
				
				/*start at the right most position, and shift n 4 bits to the right after each
				Hex Digit place is done
				*/
				for (int i = d-1; i >= 0; n >>= 4){ 
					s[i] = "0123456789ABCDEF"[n ampersand 0x0F]; // n and 0x0F gives last four bits of n
				}
				
				return s;
		};
```




> [!NOTE]
> > [!important]
> > 
> > ### How the disassembler function works
> > 
> > 1. Takes two inputs: **address start and address end**: We need this because the instructions are within some range in the memory (code section)
> > 2. We have **value** variable to store immediate values, **lo** and **hi** variables to store 2 halves of a 16 bit address: 6502 uses little endian so to put the two halves together, the address is (hi >> 8 || lo).
> > 3. The disassembler first takes the **currAddr** and sets the **lineAddr** to this
> > 4. Create a string **sInst** = ‘$’ + hex(currAddr, 4) + “:”
> > 5. Get opcode = bus → read(currAddr, true), currAddr++ (read 1 byte)
> > 6. modify sInst += lookup[opcode].name (Lookup is designed in a way that the right instruction is at the right opcode (index))
> > 7. Do if else if checks to figure out the addrMode and then get the respective values accordingly for variables, **value, lo, hi** and modify sInst accordingly, all while incrementing currAddr ofc.
> > 8. Finally, add (key: lineAddr, value: formed string) to the map.
> > 9. Return this map containing all the instructions disassembled.












1. Bits only become useful when they are grouped and some *interpretation* is applied to them. 
2. Can represent non negative numbers by grouping bits. 
3. Can create a standard code for characters and then encode them in a document.
4. 3 main encodings for numbers:
	- Unsigned encoding: Traditional binary notation (n >= 0)
	- Signed Encoding: Usually 2's complement. (n is all positive and negative integers)
	- Floating point encoding: To represent real numbers. Basically, a base 2 version of scientific notation. (10^x)

5. Limited number of bits is used to represent and so *overflow* is possible. 

6. When it comes to floating point arithmetic, products of positive numbers will always be positive (no overflow negative number like integers). Overflow instead results in a special value of $${+ \infty}$$
7. Floating point arithmetic is not associative because of lack of precision:
$${(3.14 +1e20)} - 1e20 = 0.0$$
on most machines however, 
$${3.14 + (1e20 - 1e20) = 3.14}$$


8. This difference between floating point and integer number representations is because of their finiteness: integer rep. can encode a small set of values but accurately. Meanwhile floating point numbers include a big set but not accurately.



# 2.1 Information storage

1. To the machine level program, memory looks like a byte array. This is only a virtual image and it is different under the hood. 
2. Compiler associates type info with each pointer. This helps it generate different machine level code to access the value stored at the location designated by the pointer depending on the type of that value.
3. The actual machine level code that it generates has no awareness of these data types. 
4. Machine level code treats all these objects as just blocks of byytes and the program itself a sequence of bytes. 

## 2.1.1 Hex Digits 
Why do decimal to hex conversion works by using the division algorithm? 

it works for the same reason it works for binary number system:
![[Pasted image 20260109211447.png]]
## 2.1.2 Words
- Word represents nominal size of integer and the size of a pointer or address.
## 2.1.3 Data sizes
- There are different data types. And then there are different lengths even inside data types.
- in C, different length integers can be used using prefixes short, long and long long. 

>[!important] ## What do compilers and CPU specifications do in defining lengths for data types???
>### 1. CPU itself: hardware-level constraints
>
The CPU **does not define “C types” like `int` or `long`**. That’s a language-level concept.
>
What the CPU provides:
>
>1. **Word size / register width** – e.g., 32-bit or 64-bit.
  >  
    >- Determines how many bits it can operate on efficiently in a single instruction.
  >      
    >- Sets natural alignment boundaries (e.g., 64-bit CPU likes 8-byte alignment).
    >    
>2. **Instruction set / allowed memory accesses** – e.g., which data sizes can be loaded/stored in one instruction.
  >  
    >- Some CPUs only allow 32-bit aligned loads for 32-bit integers.
 >       
>3. **General constraints** – like maximum addressable memory, atomicity of loads/stores, etc.
  >  
>
✅ The CPU does **not define the size of a “long int”**. That’s up to the compiler and ABI.
>
>---
>
>### 2. ABI: the middleman
>
>The **Application Binary Interface (ABI)** is the rulebook that says:
>
>- “On this platform, a `long int` is 8 bytes, aligned to 8 bytes.”
  >  
>- “Function arguments must be passed in registers or stack slots aligned to X bytes.”
>    
>- “Struct members must have padding so the struct aligns to Y bytes.”
>    
>
>The ABI exists to ensure:
>
>- **Different compilers agree** on memory layout.
>    
>- **Libraries and OS calls** can interoperate safely.
>    
>
>So the **ABI provides the concrete sizes and alignment rules for each type**, based on the CPU capabilities, but it’s not the CPU itself giving these rules—it’s a **contract for software running on the CPU**.
>
>---
>
>### 3. Compiler: implements the ABI
>
The **compiler**:
>
>- Maps C/C++ types (`int`, `long`, `double`) to **specific sizes and alignments** according to the ABI.
  >  
>- Inserts **padding** to enforce alignment.
  >  
>- Generates code that respects CPU instructions and ABI conventions.
  >  
>
Without the compiler enforcing the ABI, your structs or function calls could **break when calling system libraries or other compiled code**.


- Programs should be written in a way that they are portable to archs with different sizes. For example, an int can store a pointer in a 32 bit architecture but not in 64 bit, so developer should steer clear of coding styles like this. 

## 2.1.4 Addressing and Byte ordering 
- Any program object is stored as a contiguous byte sequence in memory, with the lowest byte address representing the address of the object. 

- Little endian: 0x100: Least significant byte
			 0x101: More significant byte
			 0x102: Even more significant byte
			 ...
			 ...
			 0x107: Most significant byte
	is used by most intel compatible machines.
- Big endian is just the opposite.

- Usually endianness is never an issue as long as the program has been compiled for it.
- However, in networking issues may arise when a LE machine sends data to a BE machine.
- To avoid this, all network facing application programs should adhere to endianness standard of that network. (sender converts to network standard, receiver converts from network standard to internal).

>Why are `unsigned char` s often used in system level programming?

- Typedef is used to define new data types
`typedef unsigned char *byte_pointer`



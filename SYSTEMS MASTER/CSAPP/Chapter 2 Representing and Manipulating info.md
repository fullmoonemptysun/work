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
4. Machine level code treats all these objects as just blocks of bytes and the program itself a sequence of bytes. 

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


## 2.1.5 Representing strings
- Strings are char* at the core. A sequence of chars terminated by the null char "\x00"
- As a consequence if we print the bytes at a char*, we will see the character bytes printed in the right order and not in little endian. 
- The reason for this is that endianness only effects bigger, multi-byte objects, meanwhile is string is not a whole object but instead an array of bytes (chars) and so bytes are stored as they are stored. 

## 2.1.6 representing code
- The same high level code will be different after machine code translation for different systems. Even if the processors and everything else is the same, if the OS is different the binary code will be different (slightly or vastly) and have different bytes. 
- This is why binary code is not cross compatible across systems (plain binary code).
- To a machine, code is just a sequence of bytes that it executes as instructions. It has no clue of the source of the instructions.

## 2.1.7 Boolean Algebra

- General Boolean algebra concepts.
- Bit - vectors = $[bbbbbbbbb] {b \subset (0,1)}$
- Not the same as binary strings. These can be used to encode finite sets or even signals (masks). 
- $[01010110]$ encodes the set ${1,2,4,6}$ (basically, if i in the set, ith bit is 1 otherwise 0.) Boolean operations on bit vectors like these translate to set operations on the set. These bit vectors can also be used to represent signals in a program. 

## 2.1.8 Bit level operations in C
- Some logical equivalents to remember:
- a ^ a = 0. And so we can replace 2 values in place in memory by doing:
```
a = a^b;
b = a^b; (b = a ^ b ^ b);
a = a^b; (a = a^b ^ a^b^b);
(a = b, b = a). NO NOTICEABLE PERFORMANCE IMPROVEMENT.
```

- When masking it is important to think of these operations we can get to. For example:

```
a ^ b = a&~b | b&~a   or  (a|b) & ~(a&b)
```

- When masking we should try to make our code portable. 
example: ~0 instead of 0xFFFFFFFF on a 32 bit machine. (32 bit int)
## 2.1.9 Logical operations in C
- Are greedy. If the actual result can be derived from the first expression, then the full expression is not evaluated. 
e.g: a && 5/a will never have a divide by 0. 


## 2.1.10 Shift operations in C
- Left shift (<<) is just left shift by k.
- Right shift >> can mean 2 different things:
	- Logical right shift (zeroes fill on the left side)
	- Arithmetic right shift (MSB fills on the left side)
		- Most C compilers do this for signed types and logical for unsigned.
		- The reason (from my thinking): If we shift -24 by 2, we are essentially dividing by 4 which should give us -6 (11111010). But if we do a logical shift by 2 we will get 00111010 (two zeroes instead of the msb (1)) and so this is 58. Which is wrong. So we need to use arithmetic shift here. 
- There is a limit on the value k of how much we should shift. It cannot be more than the width of the data being operated on. It is defined as undefined behavior. (some compilers will try to shift it by k mod w where w is the bit width, but it is not guaranteed.). 
and so,
```c
uint32_t x = 1;

x << 35; // UNDEFINED BEHAVIOR
uint64_t y = x << 35; //STILL UNDEFINED
uint64_t y = (uint64_t)(x) << 35; // CORRECT ✔✔✔
```

- Arithmetic operations take precedence over shift so
```c
1 << 2 + 3 << 2
//NOT (1<<2) + (3<<2) 
//IS 1 << (2+3) << 2
```


# 2.2 Integer Representations

## 2.2.1 Integral data types
- The sizes depend heavily on the compiler and machine's word size. 
- However the C standard guarantees certain data sizes which may be equal or less than the 32 bit equivalents. 
example: int is guaranteed to be 2 byte long (16 bit computers). But is 32 bit on x86-32 and x86-64.
- `long long` is guaranteed to be 8 bytes long no matter what by the c99  standard.

- `long` is the same size as an `int` on x86 32, and `long` is the same size as a `long long` on a x86  64 bit machine (usually 8 bytes).


## 2.2.2 Unsigned Encodings
- Simple unsigned $2^i$ Encoding:
Binary to unsigned:
	$B2U_w(x) = {\Sigma}^{w-1}_{i=0} {x_i \cdot 2^i}$


## 2.2.3 Two's Complement
- The sign bit (MSB) becomes negative weight when we are dealing with negative and positive integers.
$B2T_w(x) = x_{w-1} \cdot (-2^{w-1})+{\Sigma}^{w-2}_{i=0} {x_i \cdot 2^i}$

- The range of two's complement:
	- Smallest number = [100.......0] , Only the negative weight on the positive weights all 0. = $-2^{w-1}$
	- Largest number = [0111....1] , only the negative weight off, all the positive weights = 1. = ${\Sigma}^{w-2}_{i=0} {x_i \cdot 2^i}$ = $2^{w-1} - 1$
	- And this is why the range is not symmetric and negative number is one more than the positive numbers.

- Both these functions are bijective, that is one to one and one from one. 

- Even though most machines use 2's complement, we should not assume this representation in all machines. 
- Only the ranges guaranteed by the C standard are guaranteed, nothing else is defined surely.
- `<limits.h>` library defines set of constants such as `INT_MAX, INT_MIN, UINT_MAX` to defined these sizes for the machine the program is being compiled on. 

- 
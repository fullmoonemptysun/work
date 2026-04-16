  

## Bit Fields

1. Bit Field is a feature in C++ for specifying the size of struct and class members so that they occupy a specific number of bits, not the default memory size.

  

Example:

```C++
struct name{
	char x : 3 //use 3 bits for this member.
	int y : 6 //use 6 bits for this member.
}
```


1. The number of bits can be more than the member’s type’s default size.

2. Cannot have pointers to bit fields or references (&) **because they might not start at a byte boundary.**

3. Uses:
    - Memory optimization: uses less memory
    - Compress structs and data for easy transfer over network
    - In graphics applications, color components, pixel formats, and image data often have specific bit requirements which can be customized using bit fields.


## Unions

1. Same as structs, only difference is that all the members share the same memory location, instead of being assigned dedicated memory locations for each.
    Example: Say we have the following struct:
    
    ```C++
    struct{
    	char unu: 1;
    	char sw2: 1;
    	char sw1: 1;
    }
    ```
    

and if we want to be able to set all these bits to be 0 at the same time, we can do:

```C++
union{
	struct{
	char unu: 1;
	char sw2: 1;
	char sw1: 1;
	}
	
	char reg;
}

//we can just do:
union A;
A.reg = 0x0
```

  

  

## Overview of NES structure:

![[image 55.png|image 55.png]]

1. CPU: 6502
2. Memory
3. APU: Audio Processing
4. PPU: very complex: Picture processing
5. VRAM: video
6. Graphics: Like graphics card
7. Palettes: color palettes (maybe?)

### 8. Mappers:

In older gaming consoles like the **Nintendo Entertainment System (NES)**, the hardware was limited in how much memory (ROM) it could access at once. To get around this limitation, **memory mappers** were used to make games larger and more complex.

Here's how it worked in simpler terms:

1. **Limited Memory**: The NES could only access a small portion of a game’s memory at a time (32 KB for program data and 8 KB for graphics). But many games needed more memory than this.
2. **Bank Switching**: A **memory mapper** acted like a switchboard. It would swap different sections (or "banks") of the game’s memory in and out, so the console could access more data without needing more physical memory.
    - Think of the game's memory like a book, but the NES could only read a few pages at a time.
    - The memory mapper allowed the NES to "switch" between different sets of pages when needed, even though it couldn't have all the pages open at once.
3. **Larger Games**: With this technique, games could be much larger than the NES itself could handle in one go, because the mapper allowed the system to load only what was needed at a specific time.
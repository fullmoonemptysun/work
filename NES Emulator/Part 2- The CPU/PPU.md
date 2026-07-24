- 8 registers memory mapped and exposed to cpu ($2000 - $2007)  (mirrored from $2008 - $3fff)

- Sprites and backgrounds are created off of fundamental graphic unit called tile. A tile is 8x8 pixel. Each pixel is defined by 2 bits (1 from plane 0 and 1 from plane 1)
	- 00 - transparent
	- 01 - color 1 from palette
	- 10 - color 2 from palette
	- 11 - color 3 from palette. 

- And so any tile can only have 3 colors at max. 



## Nametables

- Store tiles for the background of 1 screen (256 x 240 pixels). 

The NES screen can scroll. To scroll smoothly, the PPU needs more than one screen's worth of background data — so it has 4 logical nametable slots arranged in a 2x2 grid (two screens wide, two screens tall). This gives you a larger "world" to scroll around in.

But the NES only has enough physical RAM (CIRAM) for 2 nametables, not 4. So two of the four logical slots have to be mirrors of the other two. Which ones mirror which depends on the game's scrolling direction:

- **Horizontal mirroring** — for games that scroll vertically (like Kid Icarus). Left and right nametables are the same.
- **Vertical mirroring** — for games that scroll horizontally (like Super Mario Bros). Top and bottom nametables are the same.

That's all it's saying. For NROM you just need to implement one of these two mirroring modes based on the flag in `header[6]` that you already read. You don't need to worry about four-screen or mapper-controlled mirroring for now.


> **So how does the ppu render one scanline (left to right) of the background?**
> 
> - Fetch a nametable from 0x2000-0x2fff (which tiles)
> - Fetch the corresponding attribute table entry from $23C0-$2FFF and increment the current VRAM address within the same row.
> - Fetch the low-order byte of an 8x1 pixel sliver of pattern table from $0000-$0FF7 or $1000-$1FF7.
> - Fetch the high-order byte of this sliver from an address 8 bytes higher.
> - Turn the attribute data and the pattern table data into palette indices, and combine them with data from [sprite data](https://www.nesdev.org/wiki/PPU_sprite_evaluation "PPU sprite evaluation") using [priority](https://www.nesdev.org/wiki/PPU_sprite_priority "PPU sprite priority").



## HIGH LEVEL FLOW OF PPU
![[Pasted image 20260722211932.png]]



## Note on timing
In the actual hardware, cpu and ppu tick almost at the same time but in serialization of this in software it is not possible to do this accurately for me at the time of writing. 

An educated guess is that most NROM games will not depend on these bleeding edge timing cases and if they are encountered, i will implement edge case handling for those situations specifically (change the order of ticks accordingly). 

In the future, I may create a masterclock class that controls the ticks for both the components depending on the last tick, the upcoming instruction, and the current state. 


For the first implementation, I will go with this tick order: 
```
ppu.clock();
ppu.clock();
ppu.clock();
cpu.clock();
```


[A Visible Glitch](https://www.nesdev.org/wiki/PPU_registers#:~:text=backdrop%20override.-,Bit%200%20race%20condition,-Be%20careful%20when) that happens because of certain CPU-PPU alignments and timing edge cases, causes some visual bugs in some games. I will not emulate this behavior (atleast, not as of right now.)



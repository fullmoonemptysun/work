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


## Scrolling
There are some internal registers:
`x`, `v`, `t`

Imagine one screen as a "camera". This camera can be moved anywhere in the 2d space within the 4 nametables. 
Scanlines and dots only take care of the counts for the current frame. They do not track position through the different screens. To track position we have scrolling. 

Mainly 5 components:
- nametable bits: which nametable is current tile in
- coarse_x: How many tiles has the camera moved horizontally from original position (original NN bits set by cpu in ppctrl)
- coarse_y: same as above but vertically.
- fine_x: how many pixels has the camera move within the coarse_x tile horizontally (pixel level detail)
- fine_y: how many pixels has the camera moved within tile vertically (changes every scanline we render). 

Conceptual flow:
	- CPU sets PPUSCROLL before each frame. This ultimately sets the staging register t with the values of above variables.
	- before rendering starts, this t value is copied into v
	- v contains current nametable bits, coarse_X, coarse_Y, fine_y.
	- x contains fine_x. 
	- when we do nametable and ptable fetches we use the coarse_X and coarse_Y to get the correct tile no. from nametable. Then we use fine_y to get the right row (vertically) within the tile. 
	- we don't take into account fine_x while grabbing the row so we grab the full 8 bits of the tile from the pattern table and load into the shift registers.
	- Finally when we render, we use fine_x to offset into the shift registers (if camera is 3 pixels in the current tile, use bits from 12th bit of the register).

**Transitions and wraparound system:**
- At each 8 dot boundary, we increase coarse_x and check if increasing it makes it >= 32 (out of current nametable horizontally) if yes then we flip the horizontal nametable bit in v and reset coarse_x to 0. Otherwise, we just increase coarse_x by 1. 
- At dot 256 we also increment fine_y and then check if it is flowing over 7 (vertical tile boundary) if that happense, it is reset to 0 and coarse_y is incremented by 1 then if coarse_y is out of the 0-29 range (vertically over current nametable)  we flip vertical nametable bit in v and reset coarse_y to 0 also.
- At dot 257 (after rendering, we have to set v = t (only coarse_X and horizontal nametable bits) again to bring the rendering back to where it was horizontally). 



- The documentation says:
> *"The PPU uses the current VRAM address for both reading and writing PPU memory thru $2007, and for fetching nametable data to draw the background. As it's drawing the background, it updates the address to point to the nametable data currently being drawn. Bits 10-11 hold the base address of the nametable minus $2000. Bits 12-14 are the Y offset of a scanline within a tile."*

When it says current VRAM address for nametable fetches, it may sound confusing but if we really think about it, what are VRAM addresses? they are a combination of nametable bits, coarse_x, coarse_y. They're addresses into the nametables. When the CPU uses it outside of rendering however, v and t become general purpose addressing registers into the full PPU address space by the CPU. 

The 15 bit registers _t_ and _v_ are composed this way during rendering:

```
yyy NN YYYYY XXXXX
||| || ||||| +++++-- coarse X scroll
||| || +++++-------- coarse Y scroll
||| ++-------------- nametable select
+++----------------- fine Y scroll
```





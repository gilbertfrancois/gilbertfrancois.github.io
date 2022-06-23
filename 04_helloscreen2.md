# Hello Screen2: VDP programming for MSX1

_Gilbert Francois Duivesteijn_

[< Back to main page](index.html)

![](/Users/gilbert/Development/git/gilbertfrancois.github.io/04_helloscreen2_title.jpg)

The goal of this page is to show how to create graphics in screen mode 2. The examples in this page are the very minimum to create a tile, an 8x8 pixel sized block. Both examples give the output as seen in the screenshot above.

**Step 1**: Watch the tutorial from ChibiAkumas to learn how the tile based Graphics system of the MSX works.

[Lesson S6 - Easy Tiles on the MSX1](https://www.chibiakumas.com/z80/simplesamples.php#LessonS6)

[Lesson P5 - Bitmap graphics on the TI-83 and MSX](https://www.chibiakumas.com/z80/platform.php#LessonP5)

*Note: On the MSX1, there are some details to be careful with!* The command **otir** to copy a block of memory to the VRAM cannot be used on an MSX1. There is a speed limit when accessing the VRAM of 29 cycles. The otir instruction is too fast and it results in incomplete tiles in VRAM. On the (real) MSX1, I had lines skipped in the tiles. The examples on this page will be using an alternative routine for the **otir** instruction, as suggested by Grauw (see references). The MSX2 does not have these speed limits, since the VDP is faster than the **otir** instruction.

## Method 1: using mostly easy BIOS functions

Using bios functions gives the benefit of smaller code and guaranteed to work subroutines. The downside is that it might be not the most efficient way to perform tasks.

**Step 2a**: Type in the code below and save to a file named `hello_screen2.asm`.  The code is made for a ROM. If you prefer to compile to a CAS or BIN file, look at the [How to create a ROM, BIN or CAS file](02_rombincas.html) page.

```assembly
; Hello Screen2
; Minimal example for showing an 8x8 bitmap in screen 2,
; using BIOS functions.
;
    ; ROM header
    org $4000
    db "AB"
    dw Main
    dw 0, 0, 0, 0, 0, 0

CHGMOD      equ $005f
    ; a for screen mode
LDIRVM      equ $005c
    ; HL for source address (memory), 
    ; DE for destination address (VRAM), 
    ; BC for the length. All bits of the VRAM address are valid.
SETWRT      equ $0053
    ;HL for VRAM address
VDPData     equ $98
VDPControl  equ $99
RomSize     equ $4000
TilePos     equ 0 * 8

Main:
    ; Change to screen 2
    ld a, 2
    call CHGMOD
	 
    ; Copy Pattern0 to VRAM
    ld hl, Pattern0
    ld de, $0000 + TilePos
    ld bc, $0008
    call LDIRVM

    ; Copy Color0 map to VRAM
    ld hl, Color0
    ld de, $2000 + TilePos
    ld bc, $0008
    call LDIRVM

    ; Place pattern0 to (x,y)=(2, 1)
    ld hl, $1800 + 1 + 1*32
    call SETWRT
    ld a, $00
    out (VDPData), a

    ; Place pattern0 to (x,y)=(4, 2)
    ld hl, $1800 + 2 + 2*32
    call SETWRT
    ld a, $00
    out (VDPData), a

    di
    halt


Pattern0:
    db %10101010
    db %01010101
    db %10000010
    db %01000001
    db %10000010
    db %01000001
    db %10101010
    db %01010101

Color0:
    db $16
    db $19
    db $1b
    db $13
    db $12
    db $15
    db $14
    db $1d


ProgEnd:
    ds $4000 + RomSize - ProgEnd, 255

```

The listing above does the following things:

- Change to screen 2, using the bios function `CHGMOD`.
- Copy `Pattern0`, a 8x8 pixel block to VRAM in the Pattern Generator Table, using the bios function `LDIRVM`, where `hl` has the Pattern0 address in RAM, `de` holds the destination address in VRAM, `bc` is the block size to transfer.
- Copy `Color0`, a 8x8 colour code block to VRAM in the Color Table, using the bios function `LDIRVM`, where `hl` has the Pattern0 address in RAM, `de` holds the destination address in VRAM, `bc` is the block size to transfer.
- For placing the pattern on other places on screen than the default place, initialized by the bios function CHGMOD, we can copy the index number of Pattern0 to the desired location in the Pattern Name Table. First, set the destination memory location in `hl`. Then make the VDP ready for writing a value with the bios function `SETWRT`. Finally load the pattern index number in register `a` and send the value via the `out($98)` to the VDP. 

**Step 3a**: Compile

*with VASM*

```shell
$ vasmz80_oldstyle hello_screen2.asm -chklabels -nocase -Dvasm=1 -Fbin -L out.sym -o out.rom
```

*or with Glass (you can choose)*

```shell
$ java -jar Glass.jar hello_screen2.asm -L out.sym out.rom
```

**Step 4a**: run with openMSX

```shell
$ <path to>/openmsx -machine C-BIOS_MSX1_EU -cart out.rom
```

Now you should see 3 coloured blocks in a diagonal.

## Method 2: using custom copy functions

Using your own written functions gives the benefit of code that you fully understand and enables you to optimize it as far as you can. Usually, the code footprint is larger than using ready made BIOS functions.

**Step 2b**: Type in the code below and save to a file named `hello_screen2.asm`. 

```assembly
; Hello Screen2
; Minimal example for showing an 8x8 bitmap in screen 2,
; using self written 'copy to VRAM' routines.
;
    ; ROM header
    org $4000
    db "AB"
    dw Main
    dw 0, 0, 0, 0, 0, 0

CHGMOD      equ $005f
    ; a for screen mode
VDPData     equ $98
VDPControl  equ $99
RomSize     equ $4000
TilePos     equ 0 * 8

Main:
    ; Change to screen 2
    ld a, 2
    call CHGMOD
	 
    ; Copy Pattern0 to VRAM
    ld hl, $0000 + TilePos
    call SetVDPWriteAddress
    ld hl, Pattern0
    ld b, 8
    ld c, VDPData
    call CopyToVRAM
    
    ; Copy Color0 map to VRAM
    ld hl, $2000 + TilePos
    call SetVDPWriteAddress
    ld hl, Color0
    ld b, 8
    ld c, VDPData
    call CopyToVRAM

    ; Place pattern0 to (x,y)=(2, 1)
    ld hl, $1800 + 1 + 1*32
    call SetVDPWriteAddress
    ld a, $00
    out (VDPData), a
    
    ; Place pattern0 to (x,y)=(4, 2)
    ld hl, $1800 + 2 + 2*32
    call SetVDPWriteAddress
    ld a, $00
    out (VDPData), a
    
    di
    halt


CopyToVRAM:
    ; Don't use otir on MSX1
    ; See http://map.grauw.nl/articles/vdp_tut.php#vramtiming
    outi
    jp nz, CopyToVRAM
    ret

SetVDPWriteAddress:
    ld a, l
    out (VDPControl), a
    ld a, h
    or %01000000
    out (VDPControl), a
    ret
	
Pattern0:
    db %10101010
    db %01010101
    db %10000010
    db %01000001
    db %10000010
    db %01000001
    db %10101010
    db %01010101

Color0:
    db $16
    db $19
    db $1b
    db $13
    db $12
    db $15
    db $14
    db $1d


ProgEnd:
    ds $4000 + RomSize - ProgEnd, 255
```

- Note that instead of **otir**, a subroutine `CopyToVRAM` with outi is used. This is different from the ChibiAkumas tutorials (at the time of writing, June 2022).
- The BIOS functions from the first example are replaced by own implementations. The `CopyToVRAM` subroutine is the fastest possible implementation for copying data to VRAM, according to the info page of  [VDP programming tutorial :: VRAM timings](http://map.grauw.nl/articles/vdp_tut.php#vramtiming).

**Step 3b**: Compile

*with VASM*

```shell
$ vasmz80_oldstyle hello_screen2.asm -chklabels -nocase -Dvasm=1 -Fbin -L out.sym -o out.rom
```

*or with Glass (you can choose)*

```shell
$ java -jar Glass.jar hello_screen2.asm -L out.sym out.rom
```

**Step 4b**: run with openMSX

```shell
$ <path to>/openmsx -machine C-BIOS_MSX1_EU -cart out.rom
```

Now you should see 3 coloured blocks as in the first example.



## References 

-  [VDP programming tutorial :: VRAM timings](http://map.grauw.nl/articles/vdp_tut.php#vramtiming)
- [Overview of MSX 1 BIOS Functions](http://map.tni.nl/resources/msxbios.php#msx1bios)
- [V9918 programmer's guide (MSX1)](ti-vdp-programmers-guide.pdf)

- [V9938 programmer's guide (MSX2)](http://rs.gr8bit.ru/Documentation/V9938-programmers-guide.pdf)

- [More Screen2 for MSX1 example code](https://www.msx.org/forum/development/msx-development/getting-started-msx-coding-assembler)


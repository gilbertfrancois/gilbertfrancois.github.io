# Updating variables when compiling as ROM

_Gilbert François Duivesteijn_

When your program is compiled for cartridge as ROM, you cannot change values of variables in this memory area. For example:

```assembly
    ; ROM header
    org $4000
    db "AB"
    dw Main
    dw 0, 0, 0, 0, 0, 0

RomSize     equ $4000

Main:
		; increase the value of MyVar
		ld hl, MyVar  ; Load the address of MyVar in HL
		ld a, (hl)    ; Load the value of MyVar in a
		inc a         ; Increase a
		ld (hl), a    ; Write the updated value in MyVar

		; finish
		di
		halt
		
MyVar:
		db $00
		
ProgEnd:
    ds $4000 + RomSize - ProgEnd, 255
```

This example won't work. The memory of MyVar lies inside the ROM. Let's look at the debugger and see what is happening. Pay special attention to memory location `$4018`, where `MyVar` is located.

<video autoplay="autoplay" loop="loop" controls="control">
	<source src="05_romvar_debug1.mp4" type="video/mp4"/>  		
	Your Browser does not support the video element
</video>


In the section `Memory layout` of the debugger, we can see that page 0 and 1 are reserved for the ROM:

| Page | Address   | Segment |
| ---- | --------- | ------- |
| 0    | 0000-3fff | R0/1    |
| 1    | 4000-7fff | R0/1    |
| 2    | 8000-bfff | -       |
| 3    | c000-ffff | -       |

Our `ORG` address is `$4000` and MyVar is located at `$4018`. Since this is still in ROM area, the memory content is read-only. The solution is to store the variables or anything that needs to be manipulated in memory, in RAM area. Page 2 and 3 are RAM. There are several ways to do that in code. However, most methods are assembler specific. The standard syntax that works with all assembers is by simply specifying the address as constant, e.g:`MyVarRam equ $c000`. The new listing is:

```assembly
    ; ROM header
    org $4000
    db "AB"
    dw Main
    dw 0, 0, 0, 0, 0, 0

RomSize  equ $4000
MyVarRam equ $c000

Main:
    ; copy initial MyVar value to RAM.
    ; Assuming you have more variables,
    ; usr ldir for fast copy.
    ld hl, MyVar      ; source addr
    ld de, MyVarRam   ; destination addr
    ld bc, 1          ; block size
    ldir              ; copy

    ; increase the value of MyVarRam
    ld hl, MyVarRam
    ld a, (hl)
    inc a
    ld (hl), a

    di
    halt

MyVar:
    db $00

ProgEnd:
    ds $4000 + RomSize - ProgEnd, 255
```

First, we copy the initial values from ROM to RAM (when needed). Then we continue our original program, and can successfully change values in MyVarRam. You can see in the debugger that the increase of the value is successful.

<video autoplay="autoplay" loop="loop" controls="control">
	<source src="05_romvar_debug2.mp4" type="video/mp4"/>  		
	Your Browser does not support the video element
</video>



## Final words....

When you have many variables, lists or other data, you can declare them as follows:

```assembly
MyOneByteVarRam    equ $c000
My10ByteListRam    equ MyOneByteVar + 1
My2ByteVarRam      equ My10ByteList + 10
MyEndRamBlock	     equ My2ByteVarRam + 2

TotalRamSize       equ MyEndRamBlock - MyOneByteVarRam
```


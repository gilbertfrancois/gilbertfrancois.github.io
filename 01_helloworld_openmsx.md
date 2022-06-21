# Hello World, compile, run and debug on openMSX

### 

Step 1: Watch the tutorial from ChibiAkumas to know what the code is doing:

[Lesson H3 - Hello World on the MSX by ChibiAkumas](https://www.chibiakumas.com/z80/helloworld.php#LessonH3)



|                                                              |
| ------------------------------------------------------------ |
| ![](/Users/gilbert/Development/git/gilbertfrancois.github.io/01_helloworld_01.png) |
| Hello world, printed in screen 0.                            |



Step 2: Type in the code below and save to a file named `helloworld.asm`. 

```asm
    ; org statement before the header
    org $4000

    ; ROM header
    db "AB"
    dw Main
    dw 0, 0, 0, 0, 0, 0

RomSize equ $4000
CHPUT   equ $00A2

FileStart:
Main:
    ld hl, helloWorld
    call PrintStr
    call NewLn
    call Finished

PrintStr:
    ld a, (hl)
    cp 0
    ret z
    inc hl
    call CHPUT
    jr PrintStr

NewLn:
    push af
    ld a, 13
    call CHPUT
    ld a, 10
    call CHPUT
    pop af
    ret

Finished:
	di
	halt

helloWorld:
    db "Hello world!", 0

FileEnd:
    ds $4000 + RomSize - FileEnd, 255

```

Step 3: Compile

```shell
$ vasmz80_oldstyle helloworld.asm -chklabels -nocase -Dvasm=1 -Fbin -L out.sym -o out.rom
```

Step 4: run with openMSX

```shell
$ <path to>/openmsx -machine C-BIOS_MSX1_EU -cart out.rom
```

Step 5: connect with debugger

- Open the openMSX debugger
- Press `Connect`
- To see the symbols and have a better experience when stepping through the source file, click on `sym` button and add the file `out.sym`.
- Set breakpoints (if you want) and click in the menu on `System` -> `Reboot Emulator` 


# Hooks and interrupts

_Gilbert Francois Duivesteijn_

[< Back to main page](index.html)

![](assets/images/02_rombincas_header.jpg)



## VDP (vblank) interrupt

If you want your program to run something in sync with the refresh rate of the screen, you can use the vblank interrupt. The way to call your function at the interrupt is to use the hook which is a fixed memory address, where 5 bytes are reserved for every hook. Normally they are filled with RET but you can replace these bytes with e.g. a `JP addr` command that points to your function.

This page [MSX Wiki: System hooks](https://www.msx.org/wiki/System_hooks) gives an overview of all possible hooks.

A recommended way to use hooks is:

- Make a copy of the current hook. For example, on disk systems, you cannot assume that the HTIMI hook is empty.
- Place a `jp addr` instruction at the place of the hook, pointing to your subroutine. End the hook with `ret` instructions.
- Make sure the length of the hook instruction is 5 bytes in size, overwrite all existing values to avoid bugs in case the hook was not empty.
- Inside your subroutine, run your code, followed by a `jp old_hook_add` command.

The example below is a minimal example, that gives a BEEP every second on a 50Hz machine. The HTIMI hook is used, which is triggered at the refresh rate of the screen (VBLANK). 

```assembly
; Simple interrupt test

    ; BIN header
    db $FE
    dw FileStart
    dw FileEnd - 1
    dw Main

    ; org statement after the header
    org $C000

BEEP     equ $00c0
HTIMI    equ $fd9f
MaxCount equ 50

FileStart:
Main:
    ; Install hook, run once
    di
    ; Preserve old hook instructions
    ld de, OldHook
    ld hl, HTIMI
    ld bc, 5
    ldir
    ; Copy new hook instructions
    ld hl, NewHook
    ld de, HTIMI
    ld bc, 5
    ldir

    ei

    ; Return to Basic
    ret

NewHook:
    jp BeepFn
    ret
    nop

OldHook:
    ; Reserve 5 bytes to store the old hook
    db 0, 0, 0, 0, 0

BeepFn:
    ; Run at every interrupt
    ld hl, Counter
    dec (hl)
    ld a, (hl)
    jp nz, OldHook
    ; Reset counter and call BEEP
    ld (hl), MaxCount
    call BEEP

Counter:
    db MaxCount

FileEnd:
```

Compile with:

```shell
$ java -jar Glass.jar beep.asm -L out.sym out.bin
```

On the MSX, load and run with:

```shell
bload"out.bin",r
```



## References and credits

- [MSX Wiki: System hooks](https://www.msx.org/wiki/System_hooks)
- [MSX forum: question about htimi hook](https://msx.org/forum/msx-talk/development/question-about-htimi-hook-fd9fh)
- [MSX forum: way detect vblank time](https://msx.org/forum/msx-talk/hardware/way-detect-vblank-time)
- [MSX Inside 006-INTERRUPCIONES VBLANK](https://www.youtube.com/watch?v=aUkHk_mjtOU)
# Champ: First steps and key bindings

*Gilbert François Duivesteijn*

[< Back to main page](index.html)



On this page, I try to explain how to use Champ for MSX(1) to write, store and deploy programs written in assembly or hybrid basic-assembly. Back in the days, around 1986 and beyond, I liked Champ a lot. It was easy to use, because it was an all-in-one editor, assembler, disassembler, monitor/debugger. On top of it, one could easily escape to basic to test the compiled program, which is still in memory and go back to Champ to do further programming. But the most killer feature was that you can step through code line by line, inspecting registers and memory values. 

*Fun fact: Champ has been created by the legendary [Dennis Ritchie](https://en.wikipedia.org/wiki/Dennis_Ritchie), the creator of the [C programming language](https://en.wikipedia.org/wiki/C_(programming_language)). By using this program, you work with legacy of computer history.*

The aim of this page is to show how you can develop and debug on a real MSX with only a cassette player/recorder as storage device, like it was done in the 80's with an MSX1 computer. If you don't have Champ, you can get it [here](https://download.file-hunter.com/Games/MSX1/CAS/Champ%20(1984)(PSS)%5BBLOAD'CAS-'%2CR%5D.zip). Now insert your cassette in the datarecorder, type `bload"cas:",r` and let's have some fun!

|                      |                        |
| :------------------: | :--------------------: |
| ![](03_champ000.jpg) | ![](03_champtitle.png) |
|   Box and cassette   |      Title screen      |

## Modes

Champ has 4 modes, Insert, Edit, Assemble and Debug. You can switch between the modes with the keys as described in the schema below.


---

 `< EDIT >`   &larr; `[ret]` &rarr;   `< INSERT >`

​          &uarr;

​     `[esc]`

​          &darr;

`< ASSEMBLE >`

                &uarr;

   `[m]`   `[a]`
 ​     &darr; 
   `< DEBUG >`

---
## Commands and keybindings

### `<ASSEMBLE>` mode commands

*[WORK IN PROGRESS]*

### `<INSERT>` mode commands

*[WORK IN PROGRESS]*

### `<EDIT>` mode commands

*[WORK IN PROGRESS]*

### `<DEBUG>` mode commands




| Command | Effect |
| :----- | :----- |
| @*addr* | Memory from the given *addr* onwards displayed, one byte at the time, in hex and ascii equivalent. Hit (RET) to advance to next byte, hit (ESC) to return to command level, or type a hex constant to replace the existing content of the byte. |
| A | Return to `<Assemble>` mode. |
| D *addr* *faddr* | Memory from the given address onwards is displayed in screen pages: hit any key to continue, or (ESC) to return to command level. |
| F *saddr* *faddr* *hx* | Every byte between *saddr* and *faddr* is filled with *hx*. |
| K | Print source and symbol table usage. |
| M *daddr* *saddr* *faddr* | The block of memory between *saddr* and *faddr* is copied to the block starting at *daddr*. |
| Q *addr* | Memory from *addr* onwards is disassembled. Hit (RET) to continue and (ESC) to return to command level. |
| G *addr* | The code starting at *addr* is executed (returnable). |
| C *addr* | Execute from *addr* (non-returnable) |
| B*n*=*addr* | A breakpoint number n (between 1 and 8) is set at *addr*, to cause a break in execution of any program which accesses the contents of *addr* as an instruction. Press (C)(RET) to continue from breakpoint. |
| E*n* | Eliminates breakpoint *n*. |
| T | Display the addresses of all breakpoints. |
| R *regname* | Displays the contents of a CPU register and accepts a new value (similar to the function of @ above). |
| J *addr* | Executes the code from *addr* onwards, one instruction at the time, giving a full register display. Hit (J) to continue, (ESC) to return to the command level. |
| H *expr* | Displays the decimal, hex and binary value of expr. |
| S *bytestr* | Searches the memory from $0000 onwards for every occurance of bytestr. |
| N chstr | As `S` above. |
| W | Load, save and veryfy machine code to tape. |




## Some final notes:

- Champ has 3 columns: label, command, arguments.
- Press space if the line does not start with a label, to go to the next column.
- In edit mode, delete a line with `ctrl` `z`.
- Labels start with a letter, are max 6 characters long and have no `:` at the end.
- Hexadecimal addresses start with a `$`.
- Best ORG address $C000 (according to Champ).





## References

[Champ MSX Users Manual](./assets/doc/champ_msx.pdf)

[Champ ZX Spectrum Users Manual](./assets/doc/champ_zxspectrum.pdf)

[Champ for ZX Spectrum](https://spectrumcomputing.co.uk/entry/8012/ZX-Spectrum/Champ)

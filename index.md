# MSX Assembly page for beginners

_Gilbert François Duivesteijn_

<img src="00_index_00.jpg" alt="00_index_00" style="zoom:10%;" />

## Abstract

I started these pages to document my journey into programming with assembly for the MSX1. It is hard to start, to learn and choose the right tools. Although there are plenty of very good resources on the web, most of them assume that you already have a good working development environment or basic knowledge about assembly for the Z80. The first pages will be about getting your first code compiled and working in an emulator and even on a real MSX. I think what is missing on the internet is knowledge of how to develop directly on a real MSX, especially on an MSX1. These pages will show you how you can do that. It's fun!

The page won't go deep into learning actual assembly. There are excellent resources for that. I can recommend you to look at:

- [Learn Multi platform Z80 Assembly Programming... With Vampires!](https://www.chibiakumas.com/z80/)

-  [MSX2-Technical-Handbook](https://konamiman.github.io/MSX2-Technical-Handbook/)

- [MSX Assembly Page](http://map.grauw.nl)

- [MSX wiki](https://www.msx.org/wiki/Category:Programming#Programming_Software)

- [Practical MSX machine code programming, Steve Webb](https://archive.org/details/practical_msx_machine_code_programming_steve_webb)

#### What you need

For developing on a PC or Mac, you need

- a text editor, it can be [anything](https://neovim.io) you like,

- an assembler, e.g. [VASM](http://www.compilers.de/vasm.html) or [Glass](http://www.grauw.nl/projects/glass/),
- an emulator, e.g. [openMSX](https://openmsx.org), optionally with [openMSX Debugger](https://openmsx.org).

For developing with ASM on a real MSX1, I recommend

- [Champ, by PSS](https://download.file-hunter.com/Games/MSX1/CAS/Champ%20(1984)(PSS)%5BBLOAD'CAS-'%2CR%5D.zip). It has a build in editor, debugger and monitor, all running on a MSX1.  

## Howto's: Cross platform development

- [Hello World: compile, run and debug on openMSX](01_helloworld_openmsx.html)

  This page shows you in the shortest possible way how to develop and debug using an emulator on modern hardware and run the program from a cartridge on a real MSX!

- [How to create a ROM, BIN or CAS file](02_rombincas.html)

  Templates for compiling your program to disk, cassette or cartridge.

## Howto's: Development on a real MSX1

- [Champ: First steps and key bindings](03_champ_1.html)

  This page tries to be a good reference for all the keybindings and work flows.

- [Champ: Assembler -> Basic -> Assembler roundtrip](03_champ_2.html)

​		Hands-on example.

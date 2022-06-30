# Champ: Debug, set breakpoints, monitor registers, step though code

_Gilbert Francois Duivesteijn_

[< Back to main page](index.html)

![header](assets/images/03_champ_debug_header.jpg)

This page shows how to use Champ for debugging, stepping through code, inspect registers and more. It is by far the most interesting page about champ on this website :) Let's start with typing in some code. The program is absolutely useless and does not do much. But it will help to demonstrate all the goodies that Champ has to offer as a complete development tool.



This code example does the following things:

- Initialize an array of 8 bytes in memory, labeled `SMILEY` with initial value %11110000 = $F0. This value has been chosen, so we can easily find it in memory when inspecting and debugging.
- Copies the smiley character from VRAM to RAM at position `SMILEY`,  using the bios function `LDIRMV`. 
- Invert the 8 bytes of `SMILEY`

- Return to Basic

The program does not give an output to screen. We can only observe the changes with the Champ Debugger.

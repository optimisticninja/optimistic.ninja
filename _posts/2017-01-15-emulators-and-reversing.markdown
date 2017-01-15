---
layout: post
title:  "Emulators and Reversing"
date:   2017-01-15 09:18:10 -0700
description: "A quick look into writing your own emulator and the valuable lessons it provides."
tags: [howto, emulation, c, d, disassemble, disassembly, reversing, reverse engineering]
---
Soon after I got my first job as a programmer (nowhere near ready, just eager to learn), I embarked on what I still call the most beneficial project to my career. I was reading about Mozilla's new language at the time, Rust, and desperately wanted to build my own operating system, then stepped down to kernel, then emulatior (as I said - wasn't ready, but eager). Those seemed like the ultimate chicken and egg. I searched and searched for easy architectures to emulate (don't start with the NES... it is a complicated beast) and it came down to three different CPUs: the Intel 8080, the Zilog Z80 and a CHIP-8. I unsuccesfully tried all of them several times.

Frustrated, I started building little pieces at a time. I started with a disassembler. If memory serves, the CHIP-8 only had ~35 instructions so doing this didn't sound too daunting. For those that are not familiar to to disassembling binaries, this is the process of reversing the compiled program back into code. I searched and found a ROM for Pong and was ready to start seeing how this thing worked.

Every processor has a set number of instructions and each of those directly map to a number. This number is called its "opcode" or operation code. They all do some small task such as - move this value into this storage place, add this storage place to this storage place, etc. For whatever system you chose to emulate, make sure you download as many data sheets for the hardware as you can, you will be doing a lot of researching to test that your instructions work as desired. All instructions have a set byte length, usually these bytes are arguments to the instruction - like an address, number or register.

The process to disassemble a compiled binary is this.

{% highlight c %}
while reading binary byte by byte
	lookup opcode name by its numeric byte value
	check opcode length and print arguments
	adjust program counter
{% endhighlight %}

I am leaving this description somewhat vague as a learning experience. Watch out for jumps! There are some instructions you may need to implement logic for in your disassembler. Assembly does nothing complicated, you CAN understand all of the instructions, but I don't recommend you try to memorize them all. Reference manuals and Google like would for that monollith called Java; DON'T TRY AND MEMORIZE EVERYTHING, you'll get frustrated and stop learning. Focus more on how the system's bytes are stored (big or little endian), does it have different addressing modes and what are they (indirect, direct, etc), what are the registers in this processor and what are they used for, what is the address space of this X-bit system... 

The reason I say this is knowing the ins and outs of your systems helps you connect those tiny details in the long run. A lot more so than understanding the language but not the system.

After your disassebler is written so you can properly debug, you will need to start mapping the datatype for your CPU. I will give you one hint, if you chose an architecture with registers that operate as 8/16-bit registers... welcome to unions.

{% highlight c %}
typedef struct {
	uint8_t reg[2];
	uint16_t pair;
} RegPair;
{% endhighlight %}

After that - it is all implementation baby. I have one last recommendation for design, however, and that is making an array of function pointers for executing your instructions.

{% highlight c %}
void (*op_table[0xFF]) = { &instr00, &instr01, ... };
{% endhighlight %}

Happy reversing!


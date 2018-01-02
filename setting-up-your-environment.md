# Programming the 32M1

### Overview

The ATmega32M1 microcontroller we use is a part of the Atmel AVR family. Atmel is the company that produces them \(and is now owned by Microchip\), and AVR refers to the architecture and the instruction set used in these microcontrollers \(MCU for short\). As discussed **elsewhere**, in order to program these devices, we need to translate the C code we write into machine code through a _Compiler_. The compiled machine code is then loaded to the AVR device through a protocol called _In-Circuit Serial Programming_, ISP for short, which is just a fancy name for "program your device with just 6 wires so you don't have to rip it out of your circuit board every time you want to change a line of code". This is a standard feature nowadays, but at some point in the past "burning" a program onto your MCU was a much harder and not-easily-reversible process.

Often, the whole process of writing code, compiling it for the specific microcontroller you are using, and loading that program onto the microcontroller is done through what is called an _Integrated Development Environment_, or an IDE. Atmel has a nice IDE for its own microcontrollers, named _Atmel Studio_, and it integrates and simplifies the process a fair bit. However, for HERON Mk II, we've decided to move to a more bare-bones approach that lets you see a little deeper into the inner workings of the process. We call the set of tools we use to program our microcontrollers our _toolchain_, and I'll give a brief overview of what every step of the process is about.

### The AVR Toolchain

From the point of view of the programmer there are three steps in getting your code to run on the MCU


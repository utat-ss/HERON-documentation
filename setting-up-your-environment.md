# 3. Programming the 32M1

### 3.1 Overview

The ATmega32M1 microcontroller we use is a part of the Atmel AVR family. Atmel is the company that produces them \(and is now owned by Microchip\), and AVR refers to the architecture and the instruction set used in these microcontrollers \(MCU for short\). As discussed earlier, in order to program these devices, we need to translate the C code we write into machine code through a _Compiler_. The compiled machine code is then loaded to the AVR device through a protocol called _In-Circuit Serial Programming_, ISP for short, which is just a fancy name for "program your device with just 6 wires so you don't have to rip it out of your circuit board every time you want to change a line of code". This is a standard feature nowadays, but at some point in the past "burning" a program onto your MCU was a much harder, non-reversible process.

Often, the whole process of writing code, compiling it for the specific microcontroller you are using, and loading that program onto the microcontroller is done through what is called an _Integrated Development Environment_, or an IDE. Atmel has a nice IDE for its own microcontrollers, named _Atmel Studio_, and it integrates and simplifies the process a fair bit. However, for HERON Mk II, we've decided to move to a more bare-bones approach that lets you see a little deeper into the inner workings of the process. We call the set of tools we use to program our microcontrollers our _toolchain_, and I'll give a brief overview of what every step of the process is about.

### 3.2 The AVR Toolchain

From the point of view of the programmer, there are three steps to get some code on your MCU. For the sake of simplicity, let's call them the following:

* **Code**: Use any text editor you want on your computer to create the .c and .h files in your project
* **Make**: Use a compiler to compile your code into machine code
* **Upload**: Use a _programmer_, a small circuit board with wires on either end, to connect your computer to your MCU and to upload the program 

Once you've completed the setup of the environment, this whole process will be as simple as saving your code, and typing the following into your command line:

```
make upload
```

Keep reading this section to get a better understanding of the whole process - 3.3 explains how to actually get it set up and working on your laptop.

#### 3.2.1 Code

The




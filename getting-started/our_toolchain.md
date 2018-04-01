# 3. Our Toolchain

Developing code for a real-time embedded system like HERON Mk II as part of a
large, interdisciplinary team of programmers is no easy task, and we have
collected a set of tools to make the process as effective as possible. To make
the software we develop reliable and sustainable, it is crucial to properly set
up, use, and understand the tools described in this article.

### 3.1 Background

The ATmega32M1 microcontroller we use is a part of the Atmel AVR family. Atmel
is the company that produces them \(and is now owned by Microchip\), and AVR
refers to the architecture and the instruction set used in these
microcontrollers \(MCU for short\). As discussed earlier, in order to program
these devices, we need to translate the C code we write into machine code
through a _Compiler_. The compiled machine code is then loaded to the AVR
device through a protocol called _In-Circuit Serial Programming_, ISP for
short, which is just a fancy name for "program your device with just 6 wires so
you don't have to rip it out of your circuit board every time you want to
change a line of code". This is a standard feature nowadays, but at some point
in the past "burning" a program onto your MCU was a much harder, non-reversible
process.

Often, the process of writing code, compiling it for the specific MCU that you
are using, and loading that program onto the MCU is done through what is called
an _Integrated Development Environment_, or an IDE. Atmel has a nice IDE for
its own microcontrollers, named _Atmel Studio_, and it integrates and
simplifies the process a fair bit. However, for HERON Mk II, we've decided to
move to a more bare-bones approach that lets you see a little deeper into the
inner workings of the process. This approach also allows us to keep better
track of the _libraries_ that we use in our code. Libraries, which are
pre-written, self-contained pieces of useful code that can be reused, make up
most of the code in any embedded system, and we have a few that we have written
that you will definitely end up using if you write any code for HERON.

We call the set of tools we use to develop and program our MCUs our
_toolchain_, and I'll give a brief overview of what every part of it is about.

### 3.2 Our AVR Toolchain

From the point of view of the programmer, there are three steps to get some
code on your MCU. For the sake of simplicity, let's call them the following:

* **Code**: Use any text editor you want on your computer to create the .c and .h files and make use of libraries.
* **Make**: Use a compiler to compile your code into machine code
* **Upload**: Use a _programmer_, a small circuit board with wires on either end, to connect your computer to your MCU and to upload the program

Once you've completed the setup of the environment, this whole process will be
as simple as saving your code, and typing the following into your command line:

```
make upload
```

The rest of this section describes these steps in more detail, and explains how
they apply to our specific project.

#### 3.2.1 Coding with Libraries

Libraries are collections of well-tested, useful, and nicely documented code
that can be _included_ in many projects. They can have many different purposes,
including:

* provide easy access to basic functions, such as "wait 50 ms", "turn pin PB5
  on" or "allocate 8 bits of memory for this integer"
* allow easy use of complex features of your microcontroller, such as the SPI
  or CAN data transfer protocols
* let you use an external device \(a _peripheral_\), such as writing data to an
  SD card or running a motor.

There are three sources of libraries that we use:

1. [**Standard C
   Libraries**](https://en.wikipedia.org/wiki/C_standard_library): a set of
   libraries that can be used in any given C program running on any device.
   Functions from these libraries are used so often that you don't think about
   them much. Math functions, variable types and sizes, floating point numbers,
   strings are all dealt with here.
2. [**avr-libc**](http://www.nongnu.org/avr-libc/user-manual/overview.html): a
   library created specifically for writing C programs for AVR
   microcontrollers. It lets you do things that are specific to the
   microcontroler you are using. For example, it can let you read/write to the
   I/O pins on your microcontroller; it can allow you to count a certain number
   of milliseconds to have accurate delays; or it can provide access to certain
   power-saving functions.
3. [**lib-common**](https://github.com/HeronmkII/lib-common): our
   constantly-evolving, home-made library just for HERON Mk II. Here we have
   functions that are used in all of the different subsystems on the satellite,
   protocols that allow the subsystems to communicate, and drivers for
   peripherals that are used on the satellite.

Since libraries typically make up most of a project, it is not convenient to
store them as ".c" and ".h" files with the rest of your code. If you were to do
that, they would need to be recompiled every time you make a small change in
your main code, which would take ages. Instead, they are pre-compiled and
stored in ".a" files, which you can see in lib-common/lib/ in the link above.
This is why later on, once you have completed setting up the toolchain, you
will not be able to compile one of the subsystem projects until after you have
compiled the contents of lib-common.

Any library can be used in your code as long as it is accessible to the
compiler, however it needs to be included properly. The snippet below shows an
example of how to include various libraries and other project files in your
main code \(taken from obc.h in the [On-Board Computer
code](/github.com/HeronMkII/obc)\). Note how the angle brackets are used for
the libraries, and double quotes are used for the project files - [can you
guess
why](https://stackoverflow.com/questions/3162030/difference-between-angle-bracket-and-double-quotes-while-including-heade)?

```
// standard C libraries
#include      <stdbool.h>
#include      <stdint.h>

// avr-libc includes
#include     <avr/io.h>
#include     <util/delay.h>

// lib-common includes
#include     <spi/spi.h>
#include     <uart/uart.h>
#include     <uart/log.h>
#include     <can/can.h>

// project file includes
#include     "rtc.h"
#include     "mem.h"
```

#### 3.2.2 Making with avr-gcc

#### 3.2.3 Uploading with avrdude

# The AVR Toolchain

The AVR toolchain is a collection of open-source tools which can be used
together to compile and upload programs to AVR-based microcontrollers, such as
the ATMega32M1 microcontroller used on the Heron Mk II subsystem
microcontrollers.

Installing and understanding how to use the AVR toolchain is the first
step to contributing to the Heron Mk II codebase.

## Glossary

Before we dive in, we pause to introduce the following terminology. It will be
used liberally throughout these docs.

* SSM: abbreviation for subsystem microcontroller; this refers to the
  microcontroller which controls a particular subsystem board.

* 32M1: abbreviation for the ATMega32M1.

* MCU: abbreviation for microcontroller.

* Library: a self-contained unit of code, intended to be reused.

* Header: a file describing the interface to a particular unit of code. Header
  files end in `.h`.

* SPI: serial peripheral interface; the mechanism by which the 32M1 communicates
  with peripherals, such as external sensors.

* CAN: controller area network; a robust bus on which all SSMs communicate
  with one another.

## The AVR-GCC Compiler

The AVR-GCC compiler is a tool used to compile and link C code to create an
executable that can run on the 32M1. Generally, compilation refers to the
process of transforming structured data from one form into another. The
AVR-GCC compiler transforms C code into machine instructions that the 32M1
can execute. It also links this code together into a executable; a program
which can be run on the 32M1.

In fact, AVR-GCC accomplishes much, much more, including warning you of
certain kinds of errors in your code (type errors) and performing optimizations
to reduce code size.

Installing AVR-GCC also installs a host of other useful programs such as
`avr-objcopy`, `avr-ar` and `avr-objdump`. You'll learn about these tools later.

The AVR-GCC is based on the well known GCC compiler. To read more about AVR-GCC
and GCC, look [here](https://www.nongnu.org/avr-libc/user-manual/overview.html).

AVR-GCC can be invoked by running `avr-gcc` in the command line. You won't
normally have to invoke the AVR-GCC compiler yourself; as you'll see the process
of compiling your code can be automated to the point where it becomes a single
command.

## AVR libc

AVR libc is a library which provides all of the standard C headers and their
implementations, as well as special code used to access features unique
to AVR-based MCUs, such as accessing pins, handling hardware interrupts, and
communicating with peripheral devices.

Whenever you use standard C libraries, for example, via `#include <stdint.h>`
or `#include <avr/io.h>`, you are implicitly using AVR libc.

To learn more about AVR libc, read the online documentation
[here](https://www.nongnu.org/avr-libc/user-manual/index.html).

## avrdude

Avrdude is a command-line application which, when used in combination with a
programmer, can upload the contents of a specific file to the 32M1's flash
memory. We use avrdude to upload executables onto the 32M1.

Avrdude writes to the 32M1's flash memory using a protocol called In-Circuit
Serial Programming; ISP for short. A programmer is simply a device which
allows your computer to interface with the pins of the 32M1 that are relevant
to ISP. You will learn about which pins are relevant to ISP later, when you
learn more about the 32M1.

The programmer available for general use in the UTAT lab is the Polulu USB
AVR programmer v2. To learn more about the Polulu, read the online documentation
[here](https://www.pololu.com/docs/0J67).

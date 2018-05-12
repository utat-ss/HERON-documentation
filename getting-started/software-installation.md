# Software Installation

There are several software tools that we use to develop the satellite's software.




## AVR Toolchain

The AVR toolchain is a collection of tools to compile and upload programs to the microcontrollers.


### The AVR-GCC Compiler

The AVR-GCC compiler is a tool used to compile and link C code to create an
executable (binary program) that can run on the 32M1.


### AVR libc

AVR libc contains the standard C library and code to access features on
the MCU, such as accessing pins, handling hardware interrupts, and communicating
with peripheral devices.


### AVRdude

AVRdude is an application that uploads executable binary programs to the 32M1.




## Make

Compiling and uploading a program to the 32M1 requires several complex commands. Instead of having to type these commands every time, we use a tool called Make to automate them. By creating a file called a `makefile` containing these commands, we can just run one simple command.




## Git and GitHub

Git is a version control system that allows teams of people to track changes and collaborate on the same codebase. It provides mechanisms for tracking versions of code, so you can see previous iterations and revert back if necessary.

### GitHub

GitHub is a platform build on top of the Git that provides extra features, including a web interface for viewing and managing repositories (projects) and code.

### GitHub Desktop

This is a desktop application that makes it easier to view and modify code locally on your computer.




## Atom

Atom is a text editor that we use to write our code. It does not actually run the code, so you need to save the files and use the command line to compile and upload a program.

Other text editors are available such as Sublime and Notepad++.




## CoolTerm

We use a communication protocol called UART to allow us to view log messages from the 32M1 on a laptop. You need a terminal program that receives and displays these messages.

We recommend using CoolTerm, but other options such as TeraTerm or Xterm are available.




## Pololu USB AVR Programmer v2 (optional)

**You don't need to install this** since we rarely use it.

This utility is for changing the settings of a hardware programmer device. If someone ever tells you to install it, download and install the Pololu USB AVR Programmer v2 for your operating system [here](https://www.pololu.com/product/3170/resources). If you're using the older programmer, this might not work.

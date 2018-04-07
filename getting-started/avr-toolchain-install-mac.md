# Installing the AVR Toolchain on macOS

The following tutorial will help you install the AVR toolchain on macOS.

## Homebrew

To begin, we will install Homebrew. Homebrew is a tool that allows you to easily
install other command-line programs on your Mac. It takes care of many details,
like updating dependencies and managing versions, so you don't have to.

To install Homebrew, run

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is accessible via a new command-line utility, `brew`. To make sure the
installation was successful, run

```
 $ brew help
```

## The AVR-GCC package

We're now ready to install the AVR-GCC package. This include the AVR-GCC compiler,
as well as AVR libc.

To do this, run

```
$ brew tap osx-cross/avr
```

Once this command returns, run

```
$ brew install avr-gcc
```

This will take a while. Be patient.

## avrdude

To install `avrdude`, run

```
$ brew install avrdude --with-usb
```

## Additional Tools

Finally, we need to install the Git and Make utilities, and the CoolTerm
application. These utilities are not strictly part of the AVR toolchain,
nontheless they are very helpful.

Make will help us compile programs consistently by automating the compilation
process. Make expects compilation instructions to be stored in a file called a
`makefile`. You'll learn more about Make later.

Git is a tool which keeps track of changes in a codebase over time. It provides
powerful facilities for different programmers to collaborate on the same
codebase. You'll learn more about Git very soon.

CoolTerm is a macOS application that allows you to interface with peripheral
devices via a serial protocol.

To install Make, run

```
$ brew install make
```

This creates a new command-line utility called `make`. Run `make --help` to
learn more.

To install Git, run

```
$ brew install git
```

This creates a new command-line utility called `git`. Run `git --help` to learn
more.

To install CoolTerm, visit [this site](http://freeware.the-meiers.org/), and
download the macOS package.

## Sanity Check

To check that the installation completed successfully, run the following

```
$ which avr-gcc
```

This should print something like `/usr/local/bin/avr-gcc`; if so, you're all
set.











## Mac (alternate instructions from OBC)

## Instructions to build and run from scratch

The following information is outdated; TODO: revise.

First, install [homebrew](https://brew.sh/). Next, do

```
$ brew tap osx-cross/avr
$ brew install avr-libc
$ brew install avrdude --with-usb
```

This intalls the AVR GCC toolchain along with software to write binary files
to the ATMega's flash memory.

It may also be necessary to patch `avrdude.conf` to support the m32m1. This can
be found online.

To build the program, run

```
$ make
```

To upload the program to the MCU, run

```
$ make upload
```

## Finding the correct USB port

To see all connected USB devices do

```
$ ls /dev/tty.usb*
```

The USB device corresponding to the programmer is almost always the device
with the lowest id. To ease compilation, open the makefile and change the
value of `PORT` to this device's location. For example, if the command
above returns `/dev/tty.usbmodem00100561 /dev/tty.usbmodem00100563`,
set the value of `PORT` to `/dev/tty.usbmodem00100561`.

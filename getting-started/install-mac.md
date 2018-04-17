# Installing Software on macOS

The following tutorial will help you install the AVR toolchain on macOS.


## Homebrew

To begin, we will install Homebrew, a tool that allows you to easily
install other command-line programs on your Mac. It takes care of many details,
like updating dependencies and managing versions so you don't have to.

To install Homebrew, run

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is now accessible via a new command-line utility, `brew`. To make sure the installation was successful, run

```
 $ brew help
```

If Homebrew is successfully installed, it will display help information.


## The AVR-GCC package

We're now ready to install the AVR-GCC package. This includes the AVR-GCC compiler and the AVR libc standard libraries.

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

This is the software that writes binary files to the 32M1's flash memory. To install `avrdude`, run

```
$ brew install avrdude --with-usb
```

**Note**: It may also be necessary to patch `avrdude.conf` to support the m32m1. This can be found online.


## Additional Tools

Finally, we need to install the Git and Make utilities, and the CoolTerm
application. These utilities are not part of the AVR toolchain,
but we use them because they are very helpful.

Make helps us compile programs consistently by automating the compilation
process. Make expects compilation instructions to be stored in a file called a
`makefile`.

Git is a version control system that keeps track of changes in a codebase over time. It provides powerful facilities for different programmers to collaborate on the same codebase.

CoolTerm is an application that allows you to interface with peripheral
devices via the UART serial protocol and view log messages from the board on your laptop.

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

To install CoolTerm, visit [this site](http://freeware.the-meiers.org/) and
download the macOS package.


## Sanity Check

To check that the installation completed successfully, run the following

```
$ which avr-gcc
```

This should print something like `/usr/local/bin/avr-gcc`. If so, you're all
set.


## Build and Run a Program

To build a program, run

```
$ make
```

To upload the program to the 32M1, run

```
$ make upload
```

Note that `make upload` both compiles and uploads the program.


## Finding the Correct USB Port

To see all connected USB devices, run

```
$ ls /dev/tty.usb*
```

The USB device corresponding to the programmer is almost always the device
with the lowest id. Open the makefile and change the value of `PORT` to this device's location. For example, if the `ls` command
above displays `/dev/tty.usbmodem00100561` and `/dev/tty.usbmodem00100563`,
set `PORT = /dev/tty.usbmodem00100561`.

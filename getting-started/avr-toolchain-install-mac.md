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

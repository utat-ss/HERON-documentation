# AVR Programming on macOS INCOMPLETE

The following tutorial will help you get started with AVR programming on Windows. After finishing this tutorial, you'll be ready to compile and upload programs onto AVR microcontrolers.

## WinAVR

First, download and install WinAVR, version 20100110 through the following [link](https://sourceforge.net/projects/winavr/files/WinAVR/). By default, this installs
WinAVR in `C:\WinAVR-20100110`, if this isn't
where you'd like to install  

## Homebrew

To begin, we're going to install Homebrew. Homebrew is a tool that allows you to easily install other programs on your Mac. It takes care of many details, like updating dependencies and managing versions, so you don't have to.

To install Homebrew, run

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is accessible via a new command line utility, `brew`. To make sure the installation was successful, run

```
 $ brew help
```

## The AVR-GCC Toolchain

We're now ready to install the workhorse that is the AVR-GCC toolchain. This is the set of tools which will allow us to compile and upload programs to AVR microcontrollers. To do this, run

```
$ brew tap osx-cross/avr
```

Once this command returns, run

```
$ brew install avr-gcc
```

This will take a while. Be patient. Next, run

```
$ brew install avrdude --with-usb
```

To check that the installation completed successfully, run the following

```
$ which avr-gcc
```

This should print something like `/usr/local/bin/avr-gcc`.

## The Make Utility

Finally, we need to install the `make` utility. This helps compile programs consistently by automating the compilation process. Make expects compilation instructions to be stored in a file called a `makefile`. These details of how `make` works are not relevant at the moment.

To install `make`, run

```
$ brew install make
```

## Conclusion

You should now be ready to contribute to the UTAT AVR codebase. We look forward to reading your code!


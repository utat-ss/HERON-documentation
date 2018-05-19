# Software Installation - macOS

Please ask us for help if you encounter any issues. See the "Software Installation" page for descriptions of what these tools do.




## Homebrew

Homebrew is a tool that allows you to easily install and update other
command-line programs. This will help us install the other programs more easily.

To install Homebrew, run

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is now accessible via the command-line utility `brew`. To make sure the installation was successful, run

```
 $ brew help
```

If Homebrew is successfully installed, it will display help information.




## AVR Toolchain

To install AVR-GCC and AVR libc, run

```
$ brew tap osx-cross/avr
```

Once it finishes, run

```
$ brew install avr-gcc
```

This will take a while. Be patient.


To install `avrdude`, run

```
$ brew install avrdude --with-usb
```

**Note**: It may also be necessary to patch `avrdude.conf` to support the m32m1. This can be found online.


### Sanity Check

To check that the installation completed successfully, run

```
$ which avr-gcc
```

This should print something like `/usr/local/bin/avr-gcc`. If so, you're all
set.




## Make

To install Make, run

```
$ brew install make
```

Run `make --help` to learn more.




## Git and GitHub

To install Git, run

```
$ brew install git
```

Run `git --help` to learn more.

### GitHub

Create an account on https://github.com/. Ask one of the leads to add you to the Heron Mk II organization so you can contribute code.

### GitHub Desktop

Download and install it from https://desktop.github.com.




## Atom

Download and install it from https://atom.io/.




## CoolTerm

Download and install the macOS package from [this site](http://freeware.the-meiers.org/).

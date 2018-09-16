# Software Installation - macOS

Open the Terminal application on your computer to use the command line. Ask one of the leads if you are not familiar with using the command line.

Please ask us for help if you encounter any issues. See the "Software Installation" page for descriptions of what these tools do.


## Xcode Command Line Tools

The Xcode command line tools is a package of common development tools you will need. Note that this is a small package and is not the full Xcode application.

Run the following:

```
$ xcode-select --install
```

If a dialog box pops up, click `Install`.

When it is done, verify it is installed:

```
$ xcode-select -p
```

It should print something like `/Library/Developer/CommandLineTools`.

[Reference information](http://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/)


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

This will take a while (usually about 20-30 minutes). Be patient.

To install `avrdude`, run

```
$ brew install avrdude --with-usb
```

To check that the installation completed successfully, run

```
$ which avr-gcc
```

This should print something like `/usr/local/bin/avr-gcc`. If so, you're all
set.


## GitHub

Create an account [here](https://github.com/). Ask one of the leads to add you to the Heron Mk II organization so you can contribute code.


## GitHub Desktop

Download it from [here](https://desktop.github.com) and move the downloaded application to your Applications folder. Open it and sign in with your GitHub acccount.


## Atom

Download it from [here](https://atom.io/) and move the downloaded application to your Applications folder.


## CoolTerm

Download it for your operating system from [here](http://freeware.the-meiers.org/). Open the downloaded `CoolTermMac.dmg` file, and in the window the opens, move the `CoolTerm` application to the Applications folder on your computer.

You may get the message `"CoolTerm" can't be opened becuase it is from an unidentified developer`. If this happens, open it by right-clicking the application and selecting `Open` instead.


## Miscellaneous Troubleshooting

**Note**: It may be necessary to patch `avrdude.conf` to support the m32m1. This can be found online.

# Software Installation

These are the tools you will need to develop the satellite's software.

Please ask one of the leads if you encounter any issues or are not familiar with using the command line.

**macOS**: Open the Terminal application.

**Windows**: Open the Powershell or Command Prompt application.

**Linux**: Open the Terminal application.


## Xcode Command Line Tools (macOS only)

This is a package of common development tools you will need. Note that this will just install a small package, not the full Xcode application.

Run the following:

```
$ xcode-select --install
```

If it says you already have this installed, continue to the next step.

If a dialog box pops up, click `Install`. When it is done, verify it is installed:

```
$ xcode-select -p
```

It should print something like `/Library/Developer/CommandLineTools`.

[Reference information](http://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/)


## Homebrew (macOS only)

This is a tool that allows us to install and update other command-line programs more easily. Run the command:

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is now accessible via the command-line utility `brew`. To make sure the installation was successful, run

```
$ brew help
```

If Homebrew is successfully installed, it will display help information.


## AVR Toolchain

This is the software that allows us to compile and upload C programs to the microcontrollers.

### macOS

To install AVR-GCC and AVR libc (compiler and C libraries), run

```
$ brew tap osx-cross/avr
```

Once it finishes, run

```
$ brew install avr-gcc
```

This will take a while (usually about 20-30 minutes). Be patient.

To install avrdude (program uploader), run

```
$ brew install avrdude --with-usb
```

To check that the installation completed successfully, run

```
$ which avr-gcc
```

This should print something like `/usr/local/bin/avr-gcc`. If so, you're all
set.

### Windows

1. Download and install the latest version of [WinAVR](https://sourceforge.net/projects/winavr/files/WinAVR/20100110/). Remember where you installed it - we will call the path `<install>` here. By default, it is `C:\WinAVR-20100110`. Leave the default installation options, but uncheck "install programmer's notepad".

2. Open Control Panel and go to `System and Security > System > Advanced system settings` and click on Environment Variables. In the window that pops up, edit the Path variable in the **top** list. Add `<install>\bin` and move it to the top of the list. Add `<install>\utils\bin` and move it to second in the list. Click OK and exit the window.

3. Download [this zip file](https://drive.google.com/file/d/1fWXrAz3peHORZjkVtChWaw8oGPStPxiM/view?usp=sharing), which contains necessary modifications we made to some files so it works. You may need to retry the download if it doesn't work. Unzip this file. Within the extracted folder, take the `WinAVR-20100110` folder and replace the `<install>` folder on your computer with it.

[Reference instructions]( http://fab.cba.mit.edu/classes/863.16/doc/projects/ftsmin/windows_avr.html) (**Do not actually follow these instructions**)

### Linux

Run the following commands:

```
sudo apt-get update
sudo apt-get upgrade all
sudo apt-get install gcc-avr binutils-avr avr-libc
sudo apt-get install avrdude
```


## Make

Compiling and uploading a program requires several complex commands. Instead of having to type these commands every time, we use a tool called Make to automate them using a `makefile` containing these commands.

### macOS

This is already installed with the Xcode command line tools.

### Windows

Download and run the setup program from [here](http://gnuwin32.sourceforge.net/packages/make.htm).

### Linux

This should already be installed.


## Git

Git is a version control system that allows teams to track changes and collaborate on the same codebase. It provides mechanisms for tracking versions of code, so you can see previous iterations and revert back if necessary.

### macOS

This is already installed with the Xcode command line tools.

### Windows

Download and install Git from [here](https://git-scm.com). In the installer, use all of the default settings.

### Linux

This should already be installed.


## GitHub

GitHub is a platform that builds on top of Git to provide extra features, including a web interface for viewing and managing projects and code.

Create an account [here](https://github.com/). If you are contributing code to the Heron Mk II codebase, ask one of the leads to add you to the Heron Mk II organization.


## GitHub Desktop

This is a desktop application that makes it easier to view and modify code locally on your computer.

Download it from [here](https://desktop.github.com).

### macOS

Move the application to your Applications folder.

### Windows

Run the installer.

### Linux

This app is not available for Linux.


## Atom

This is a text editor that we use to write our code. We recommend Atom, but you can use other text editors such as Sublime or Notepad++.

Download it from [here](https://atom.io/).

### macOS

Move the application to your Applications folder.

### Windows

Run the installer.

### Linux

Run the installer.


## CoolTerm

This is a serial terminal program that allows us to view log messages and send commands from a laptop. We recommend using CoolTerm, but other options such as TeraTerm or Xterm are available.

Download it for your operating system from [here](http://freeware.the-meiers.org/).

### macOS

Open the downloaded `CoolTermMac.dmg` file. In the window the opens, move the `CoolTerm` application to the Applications folder on your computer.

You may get the message `"CoolTerm" can't be opened becuase it is from an unidentified developer`. If this happens, open it by right-clicking the application and selecting `Open`.

### Windows

Extract the zip file. CoolTerm does not have an installer; you can move it and run `CoolTerm.exe` from anywhere on your computer as long you move the entire `CoolTermWin` folder containing the necessary libraries. We recommend moving it to your `Documents` folder, but you can move it somewhere else if you want.

### Linux

Run the application.


## Linux Reference Instructions

The Linux instructions were based off of the following links (you can check them for reference):
- [Reference 1](http://maxembedded.com/2015/06/setting-up-avr-gcc-toolchain-on-linux-and-mac-os-x/)
- [Reference 2](http://www.linuxandubuntu.com/home/setting-up-avr-gcc-toolchain-and-avrdude-to-program-an-avr-development-board-in-ubuntu)

If you want to use the Windows Subsystem for Linux, follow the instructions [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10). If you don't know which distro to use, pick Ubuntu.

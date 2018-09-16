# Software Installation

These are the tools you will need to develop the satellite's software.

Please ask one of the leads if you encounter any issues or are not familiar with using the command line.

**macOS**: Open the Terminal application.

**Windows**: Open the Command Prompt application.


## Xcode Command Line Tools (macOS only)

This is a package of common development tools you will need. Note that this is a small package and is not the full Xcode application.

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

1. Download and install [WinAVR](https://sourceforge.net/projects/winavr/files/WinAVR/20100110/). Make sure you get `WinAVR-20100110`. Remember where you installed it - we will call the path `<install>` here. By default, it is `C:\WinAVR-20100110`. Leave all of the default installation options (but uncheck "install programmer's notepad").

2. Add `<install>/bin` and `<install>/utils/bin` to your `PATH` variable. Do this from `Control Panel > System > Advanced System Settings > Environment variables`, by choosing to edit the Path variable on the **top** list in the window that pops up. Move `<install>/bin` to the top of the list, and `<install>/utils/bin` to second place.

3. Download [this zip file](https://drive.google.com/file/d/1fWXrAz3peHORZjkVtChWaw8oGPStPxiM/view?usp=sharing). Unzip this file. Within the extracted folder, take the `WinAVR-20100110` folder and replace the `<install>` folder with it.

[Reference instructions]( http://fab.cba.mit.edu/classes/863.16/doc/projects/ftsmin/windows_avr.html) (don't follow them)




## Make

Compiling and uploading a program requires several complex commands. Instead of having to type these commands every time, we use a tool called Make to automate them using a `makefile` containing these commands.

### macOS

This is already installed with the Xcode command line tools.

### Windows

Download and install the latest version of Make from [here](https://www.gnu.org/software/make/).


## Git

Git is a version control system that allows teams to track changes and collaborate on the same codebase. It provides mechanisms for tracking versions of code, so you can see previous iterations and revert back if necessary.

### macOS

This is already installed with the Xcode command line tools.

### Windows

Download and install Git from [here](https://git-scm.com).


## GitHub

GitHub is a platform that builds on top of Git to provide extra features, including a web interface for viewing and managing projects and code.

Create an account [here](https://github.com/). Ask one of the leads to add you to the Heron Mk II organization so you can contribute code.


## GitHub Desktop

This is a desktop application that makes it easier to view and modify code locally on your computer.

Download it from [here](https://desktop.github.com).

### macOS

Move the application to your Applications folder.

### Windows

Run the installer.


## Atom

This is a text editor that we use to write our code. We recommend Atom, but you can use other text editors such as Sublime and Notepad++.

Download it from [here](https://atom.io/).

### macOS

Move the application to your Applications folder.

### Windows

Run the installer.


## CoolTerm

This is a serial terminal program that allows us to view log messages and send commands from a laptop. We recommend using CoolTerm, but other options such as TeraTerm or Xterm are available.

Download it for your operating system from [here](http://freeware.the-meiers.org/).

### macOS

Open the downloaded `CoolTermMac.dmg` file. In the window the opens, move the `CoolTerm` application to the Applications folder on your computer.

You may get the message `"CoolTerm" can't be opened becuase it is from an unidentified developer`. If this happens, open it by right-clicking the application and selecting `Open` instead.

### Windows

Run the installer.


## Linux Software Installation

If you want to use the Windows Subsystem for Linux, follow the instructions [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10). If you don't know which distro to use, pick Ubuntu.

Follow the installation instructions for macOS to install the same tools, but for command line packages use `sudo apt-get` instead of `brew`. GitHub Desktop is currently not available for Linux, so you will need to use command-line Git.


## Miscellaneous Troubleshooting

**Note**: It may be necessary to patch `avrdude.conf` to support the m32m1. This can be found online.

# Installing Software on Windows

The following tutorial will help you get started with AVR programming on
Windows. After finishing this tutorial, you'll be ready to compile and upload
programs onto AVR microcontrollers.

Note that this process has only been tested on Windows 10. Please ask us for help if you encounter any issues. Also let us know of any suggestions or improvements we can make to the instructions.


## AVR Toolchain

1. Download and install [WinAVR](https://sourceforge.net/projects/winavr/files/WinAVR/20100110/). Make sure you get `WinAVR-20100110`. Remember where you installed it - we will call the path `<install>` in this tutorial. By default, it is `C:\WinAVR-20100110`. Leave all of the default installation options (but probably uncheck "install programmer's notepad").

2. Add `<install>/bin` and `<install>/utils/bin` to your `PATH` variable. Do this from `Control Panel > System > Advanced System Settings > Environment variables`, by choosing to edit the Path variable on the **top** list in the window that pops up. Move `<install>/bin` to the top of the list, and `<install>/utils/bin` to second place.

3. Download [this zip file](https://drive.google.com/file/d/1f61X8gvA3odfeqVYECHdmM-gnlFUbaJG/view?usp=sharing). You must be logged into the Space Systems general member account to access it. Unzip this file, then take the WinAVR folder inside the extracted folder and replace the `<install>` folder with it.

Note: You might need 7-Zip or other unzipping software to extract all packages.

Note: If you get errors about C99, go to the makefile and add `-std=c99` to the CFLAGs.

Any improvements on this process are welcome.


## Pololu AVR Development Bundle

1. Download and install the Pololu AVR Development Bundle for the v2 programmer [here](https://www.pololu.com/product/3170/resources). If you're using the older programmer, this might not work.

2. Now, we will try to compile and upload a program to the 32M1. Go to the `makefile` in your project folder, and change the port to match the port the Pololu programmer is connected to. You can find this in `Device Manager > Ports`. You want the programmer port, not the serial port. Your makefile should have a line similar to ```PORT = COM7```.

3. Navigate to project folder using ```cd``` in the command prompt. Type ```make``` to compile and ```make upload``` to compile and program the 32M1.

4. To change the settings of the programmer, use the Pololu USB AVR Programmer Configuration Utility that you downloaded in (11).


## Additional Tools

Finally, we need to install the Git and Make utilities, and the CoolTerm
application. These utilities are not part of the AVR toolchain,
but we use them because they are very helpful.


### Make

Make helps us compile programs easily and consistently by automating the compilation
process. Make expects compilation instructions to be stored in a file called a
`makefile`.

Download the latest version of Make from [this link](https://www.gnu.org/software/make/) and install it.

This creates a new command-line utility called `make`. Run `make --help` to
learn more.


### Git

Git is a version control system that keeps track of changes in a codebase over time. It provides powerful facilities for different programmers to collaborate on the same codebase.

Download Git from [this link](https://git-scm.com) and install it.

This creates a new command-line utility called `git`. Run `git --help` to learn
more.


### GitHub Desktop

GitHub is a website built on top of the Git version control system that provides additional features, such as a website to manage projects, project management features, and the GitHub Desktop application to make it easier to use Git on your local computer.

You are not required to install GitHub Desktop, but it is recommended to make using Git easier.

You can download and install it from https://desktop.github.com.


### CoolTerm

CoolTerm is an application that allows you to interface with peripheral
devices via the UART serial protocol and view log messages from the board on your laptop.

Download the Windows package from [this site](http://freeware.the-meiers.org/) and install it.

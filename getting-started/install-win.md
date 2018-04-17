# Installing Software on Windows

The following tutorial will help you get started with AVR programming on
Windows. After finishing this tutorial, you'll be ready to compile and upload
programs onto AVR microcontrollers.

Note that this process has only been tested on Windows 10. Please ask us for help if you encounter any issues. Also let us know of any suggestions or improvements we can make to the instructions.

1. Download and install [WinAVR](https://sourceforge.net/projects/winavr/files/WinAVR/20100110/). Make sure you get `WinAVR-20100110`. Remember where you installed it - we will call the path `<install>` in this tutorial. By default, it is `C:\WinAVR-20100110`. Leave all of the default installation options (but probably uncheck "install programmer's notepad").

2. Add `<install>/bin` and `<install>/utils/bin` to your `PATH` variable. Do this from `Control Panel > System > Advanced System Settings > Environment variables`, by choosing to edit the Path variable on the **top** list in the window that pops up. Move `<install>/bin` to the top of the list, and `<install>/utils/bin` to second place.

3. In `<install>/utils/bin`, replace the file `msys-1.0.dll` with the one found [here](http://www.madwizard.org/electronics/articles/winavrvista). It's in the download called `msys-1.0-vista64.zip`.

4. Download the [AVR 8-bit Toolchain](http://www.microchip.com/avr-support/avr-and-arm-toolchains-(c-compilers).

5. Back up your WinAVR install folder (the whole folder, it's probably `C:\WinAVR-20100110`).

6. Delete all **subfolders** from the original WinAVR folder except `pn` and `utils`.

7. Copy all the subfolders in the Atmel AVR Toolchain from (4) into `<install>` (the folder you cleaned up in (6)).

8. Download `avrdude-6.3-mingw32.zip` from [this link](http://download.savannah.gnu.org/releases/avrdude/).

9. Extract the two files from `avrdude-6.3-mingw32.zip` and copy them to `<install>/bin`.

10. Replace the `avrdude.conf` file with [this](https://pastebin.com/BvcVC8G9).

11. Download and install the Pololu AVR Development Bundle for the v2 programmer [here](https://www.pololu.com/product/3170/resources). If you're using the older programmer, this might not work.

12. Now, we will try to compile and upload a program to the 32M1. Go to the `makefile` in your project folder, and change the port to match the port the Pololu programmer is connected to. You can find this in `Device Manager > Ports`. You want the programmer port, not the serial port. Your makefile should have a line similar to ```PORT = COM7```.

13. Navigate to project folder using ```cd``` in the command prompt. Type ```make``` to compile and ```make upload``` to compile and program the 32M1.

14. To change the settings of the programmer, use the Pololu USB AVR Programmer Configuration Utility that you downloaded in (11).


Note: If you get a missing `libusb0.dll` file when you `make`, it means you deleted all the files in the WinAVR folder in step 6. Only delete the folders. You should leave `<install>\libusb0.dll`.

Note: You might need 7-Zip or other unzipping software to extract all packages.

Note: If you get errors about C99, go to the makefile and add `-std=c99` to the CFLAGs.

Note: Steps 4-10 are from [here](https://www.insidegadgets.com/2014/05/27/update-winavr-to-the-latest-avr-gcc-avrdude/). We need them to update the WinAVR bundle to the latest version of `avrdude`. Any improvements on this process are welcome.

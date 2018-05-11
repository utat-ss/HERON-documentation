## Compiling and Running Programs

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



Note: If you get errors about C99, go to the makefile and add `-std=c99` to the CFLAGs.





2. Now, we will try to compile and upload a program to the 32M1. Go to the `makefile` in your project folder, and change the port to match the port the Pololu programmer is connected to. You can find this in `Device Manager > Ports`. You want the programmer port, not the serial port. Your makefile should have a line similar to ```PORT = COM7```.

3. Navigate to project folder using ```cd``` in the command prompt. Type ```make``` to compile and ```make upload``` to compile and program the 32M1.

4. To change the settings of the programmer, use the Pololu USB AVR Programmer Configuration Utility that you downloaded in (11).

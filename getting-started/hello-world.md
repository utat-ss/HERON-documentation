# Hello World ATMega32M1

Now that you've installed the AVR toolchain, let's compile and upload our first
program.

Later on, this compilation and uploading process will be made much less painful
using tools such as Make; for most subsystems, this entire process can be
accomplished using a single command.

## Step 1

Create a new directory called `hello_32m1` as follows:

```
$ mkdir hello_32m1
```

This will be the root directory of the project. Now enter this directory by running `cd hello_32m1` and run the following command:

```
$ git init
```

This will initialize Git for this root directory and all sub-directories. You'll
learn more about what this command is doing soon.

## Step 2

Create a new file called `hello_world.c`, and copy the following contents into
the new file:

```
#include <uart/log.h>

int main() {
    init_uart();
    print("Hello ATMega32M1!\n");
    return 0;
}
```

The first line, `init_uart();`, initializes the UART circuitry on the 32M1. UART
stands for Universal Asynchronous Receiver-Transmitter. This allows the 32M1 to
communicate with external peripherals using a serial protocol.

The second line, `print("Hello ATMega32M1!\n");`, send the string "Hello
ATMega32M1" to the UART system to be written to serial output.

The third line returns from the `main` function.

Run `$ git status` and read the output carefully. Notice that Git has noticed
the new file you've created.

## Step 3

Download the `lib-common` library by running the following command in the root
directory:

```
$ git submodule add https://github.com/HeronMkII/lib-common
```

Run `ls` in the root directory. Notice a new directory has been added, called
`lib-common`. Explore the contents of this directory by running `cd lib-common`
\(do *not* modify anything\).

This is a library which contains many code components common to all subsystems,
including SPI, UART and CAN functionality.

Before the library is used, it must be built. To do this, make sure you are in
the `lib-common` directory and run

```
$ make
```

Inspect the `lib` folder in the `lib-common` directory. You should notice many
files ending in `.a`. These are the library files we just generated.

## Step 4

We're now ready to compile our program. Make sure you're in the root directory,
and run

```
$ avr-gcc -std=gnu99 -Wall -mmcu=atmega32m1 -c hello_world.c -I ./lib-common/include
```

This will create a new `hello_world.o` object file. We must now link this
object file to create an executable. To do this, run

```
$ avr-gcc -std=gnu99 -Wall -mmcu=atmega32m1 -o hello_world.elf hello_world.o -L ./lib-common/lib -l uart
```

This will create a new file called `hello_world.elf`; this is a complete
executable file. To upload it onto the 32M1, however, we must format it
in the Intel Hex format. To do this, run

```
$ avr-objcopy -j .text -j .data -O ihex hello_world.elf hello_world.hex
```

This creates the final executable which we will upload to the 32M1, called
`hello_world.hex`.

## Step 5

We're now ready to upload our hello world executable onto a board.

To do this, you need to connect a 32M1 on a board to your computer using a
programmer. Find a Polulu programmer in the lab, and connect it to an open USB
port on your computer.

We will use `avrdude` to upload our `hello_world.hex` onto the 32M1. To do this,
we need to tell `avrdude` which USB port the programmer is connected to. To find
this out, run

```
$ ls /dev/tty.usb*
```

This lists all USB devices connected to your computer.

The USB device corresponding to the programmer is almost always the device with
the lowest id. For example, if the command above returns
`/dev/tty.usbmodem00100561 /dev/tty.usbmodem00100563`, the USB port
corresponding to the programmer is `/dev/tty.usbmodem00100561`. Make sure to
remember this port.

Connect power to the VCC pin on the board, and ground the GND pin. Set the
voltage to 3.3 volts. Connect the programmer to the programmer port on the
board. Check that the light on the programmer is green; this means the board is
being powered correctly. If the board has insufficient power, the programmer
will not upload to the board, because this has the potential to corrupt the
MCU's memory.

Now run the following command, where `<port>` is replaced with the USB port you
found above.

```
$ avrdude -p 32m1 -c stk500 -P <port> -U flash:w:./hello_world.hex
```

This should upload your program onto the 32M1! If this command runs
successfully, congratulations! You've just compiled and uploaded your first
program onto the 32M1!

## Step 6

To verify that the program is running correctly, first connect the female RX pin on the programmer
via a male to male wire to the MOSI pin on the board. This is wire along which
the serial communication between the 32M1 and your computer will occur.

Now, open CoolTerm. Click the Options icon on the top of the new window. The
default options do not need to be changed. Click the Port dropdown menu and
select the port you identified above. Click OK on the bottom right. Click the
Connect icon. Now, any UART output from the 32M1 should appear on your screen.

Reset the board by pressing the reset button. If all goes well, you should see
the string "Hello world!" appear on your screen!

This process was very time consuming and error prone. We'll see how we can
simplify this process in the future using tools such as Make.

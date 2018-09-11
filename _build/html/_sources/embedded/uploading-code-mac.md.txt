# Uploading Code (Mac)

**These instructions may be a duplicate of previous instructions. These instructions have not been thoroughly checked yet.**

Here is how to upload your code to the AVR ATmega32M1 microcontroller.

1. Follow the instructions at https://github.com/HeronMkII/coms-board to download and install the AVR software.

2. Download the CoolTerm application from http://freeware.the-meiers.org. This will be used to view transmissions from UART, a communication protocol to transmit log messages (from the print() function) from the board to the computer.

3. Get the programmer hardware device. Connect the 6-pin connector to the "Programming" header on the PCB, which is used to upload code. Connect the "RX" pin on the programmer to the "MOSI_A" pin on the PCB. RX refers to the "receive" pin, while MOSI_A refers to the "alternative" MOSI line, used for UART which is separate from SPI.

4. Turn on the power supply, set the output to 3.3V, and connect the power and ground lines to the "3V3" and "GND" header pins on the PCB.

5. See the instructions at https://github.com/HeronMkII/coms-board for finding the correct USB port and modifying the makefile (it might already be the correct one).

6. Open CoolTerm and modify the options to set the correct port (see https://github.com/HeronMkII/coms-board; the port for UART is the **opposite** of the port for uploading code). Click Connect.

7. Navigate to the folder for the local copy of the Git repository on your computer. Run ```make upload``` to compile the program and upload it to the board. Fix any compile-time errors if they occur.

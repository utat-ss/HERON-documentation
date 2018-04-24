# The ATMega32M1 Microcontroller

The ATmega32M1 microcontroller used in Heron Mk II SSMs is a part of a family of
MCUs called the Atmel AVR family, produced by Atmel corporation \(now owned by
Microchip Inc.\). All the MCUs in the AVR family have a similar architecture and
instruction set.

The 32M1 has an 8-bit architecture. This means most registers are 8 bits wide,
and the data bus is 8 bits wide. Because some registers are 16 bits wide, they
must be accessed using special atomic instructions.

In practice, you won't often need to worry about the underlying details of the
instruction set or architecture; you can simply write code in C and be confident
`avr-gcc` will generate the correct instructions for you.


## Pin Layout

The 32M1 has 32 pins, labelled from 1 to 32. Most pins have some specific
functionality; for example, pin 4 is VCC, the supply voltage. Instead of
referring to pins by their numeric label, you can also specify a pin using its
mnemonic label; pin 8 is also labelled pin PB0, and read "port B zero".

There is no simple rule matching a pin's number to its mnemonic label; see the
32M1 [datasheet](https://www.microchip.com/wwwproducts/en/ATmega32M1) for more
details.

If a pin is associated to a port, it can be read and written to using software
instructions. We will examine this more closely later.

Every pin associated to a port has two key properties: its data direction, and
its value. A pin's data direction is either input (0) or output (1). A pin's
value is either low (0) or high (1).

We describe two families of pins which will be referenced many times throughout
these docs. Knowing where these pins are and how to measure the voltage across
them can also be useful for debugging purposes.

In what follows we use mnemonic labels because this is how pins are specified in
C code.


## SPI Pins

See the Communication Protocols section for how the SPI protocol works.

- **PB7 - SCK/SCLK** - The 32M1 outputs the clock signal to this pin to synchronize all devices.
- **PB0 - MISO** - Peripherals (other devices) write to this pin to send data to the 32M1.
- **PB1 - MOSI** - The 32M1 writes to this pin to send data to a peripheral device.
- **PD3 - CS/SS** - This pin can be used by another peripheral to select the 32M1 as a slave.

In the current setup, the CS/SS pin is not used because each SSM is the master in the
SPI master/slave model. It is included for completeness, and because it can be
the source of many difficult-to-debug bugs. For example, accidentally setting
this pin as an input pin and driving it high can cause the 32M1 to believe it is
now a SPI slave, thus changing the MOSI and clock SPI pins to input pins.


## CAN Pins

See the Communication Protocols section for how the CAN protocol works.

These pins connect the 32M1 to the CAN transceiver, which is the device that actually transmits and receives messages on the CAN bus.

- **PC2 - TX** - Transmit
- **PC3 - RX** - Receive

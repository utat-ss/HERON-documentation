Communication Protocols
=======================

There are various communication protocols used in the satellite to communicate data between devices. Different protocols have different hardware (communication lines) and transfer data in different formats. Different protocols prioritize different things, such as error checking, minimal hardware wires, or message priorities.

The satellite uses three communication protocols:
- CAN - Used to communicate between the 32M1 microcontrollers in different subsystems.
- SPI - Used by each 32M1 microcontroller to communicate with sensors and other devices within its subsystem.
- UART - Used to communicate between a 32M1 microcontroller and a laptop. The 32M1 can send log messages to the laptop (useful for debugging) or receive keyboard input from the laptop (not used much, but sometimes for controlled testing).

.. toctree::
	:maxdepth: 2

	can
	spi
	i2c
	uart

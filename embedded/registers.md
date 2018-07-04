# Registers

A **register** is a small piece of memory in a digital device. Each register is
generally used to control settings and behaviours of the device. Most registers
are 8 bits, but some can be 16 bits, 24 bits, or more.

For the 32M1, you write to its registers directly using built-in AVR constants.
For SPI devices, you can read and write register values by sending specific SPI
messages as described in the component's datasheet. Descriptions of registers
are generally one of the most important things to look for in a component's
datasheet.

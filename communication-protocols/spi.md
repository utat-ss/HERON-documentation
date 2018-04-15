# SPI (Serial Peripheral Interface)

SPI is a data communication protocol that we use for controlling devices within a subsystem, primarily sensors.

In this section, we will describe the SPI protocol and how to use our library with the ATmega32m1.


## What is SPI?

SPI is a communication protocol used to communicate between microcontrollers and peripheral devices, such as sensors. Put simply, it’s a system that allows us to send a byte to some device and receive a byte in return.

SPI uses what’s called a Master-Slave architecture. In this system there is one master device that communicates to multiple slave devices. In our system the master device is our microcontroller and the slave devices are mostly sensors. The master can only communicate with one slave device at a time and the slaves cannot communicate with each other.

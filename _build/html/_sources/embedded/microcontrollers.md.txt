# Microcontrollers

A microcontroller is a processing unit, like the brain of a circuit. It is slightly different from the microprocessor in your computer because it also has other components like RAM and ROM integrated on the same chip. A microprocessor just has the processing unit.

C is the most common language for programming microcontrollers. This is because of its wide availability and compatibility between platforms, low performance and memory overhead, and high level of control for the programmer. This high level of control for the programmer also creates more potential for mistakes, so be careful.


## ATmega32M1 (AVR)

![](../figures/avr.png)

Our subsystem's components will be controlled by the ATmega32M1 microcontroller on the satellite, part of the AVR family of microcontrollers. It is programmed in the C language, and you will need to install the AVR software to compile and upload code to it (which we will help you to get set up).


## Arduino

![](../figures/arduino.jpg)

Arduino is an open-source platform of microcontrollers, which we occasionally use to test components and prototype software quickly. Since Arduino has more built-in code libraries, it is faster to write and upload code to test individual components than using the AVR microcontroller. Arduino is programmed using a slightly modified version of the C++ language.

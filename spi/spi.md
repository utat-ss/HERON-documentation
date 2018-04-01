# SPI

## [init\_spi](#init_spi)

## [send\_spi](#send_spi)

## [init\_cs](#init_cs)

## [set\_cs\_low](#set_cs_low)

## [set\_cs\_high](#set_cs_high)








# SPI (Serial Peripheral Interface)

In this section, we will describe the SPI protocol and how to use our library with the ATmega32m1, the main microcontroller used on the Heron Mk. II cube-satellite.

## What is SPI?
SPI is a communication protocol used to communicate between microcontrollers and peripheral devices, such as sensors. Put simply, it’s a system that allows us to send a byte to some device and receive a byte in return.

SPI uses what’s called a Master-Slave architecture. In this system there is one master device that communicates to multiple slave devices. In our system the master device is our microcontroller and the slave devices are mostly sensors. The master can only communicate with one slave device at a time and the slaves cannot communicate with each other.

## SPI Bus
SPI uses four lines, which make up what we call the SPI bus. Three of these lines (SCK, MOSI, MISO) are shared amongst the slave devices. Whereas the final line (CS) must be unique to each slave device.  SPI is a synchronous communication protocol, meaning data is sent and received at the same time. This means that two of the four lines (MOSI, MISO) are for data, and one of the lines (SCK) is for timing. One of the data lines is for master to slave and the other is for slave to master. All of this will make more sense shortly. The four lines are shown in the figure below. They'll be explained in more detail shortly.

![Diagram of a general SPI bus](../figures/spi_bus.png)


### SCK (Source Clock)
The clock keeps both the data lines in sync. The clock is an oscillating signal produced by the master device that tells the receiving device when to read the data. Depending on the device properties, data is either sent/received on the rising/falling edge of SCK. I'll touch on this in more detail shortly. This line is shared by all slave devices.

### MOSI (Master Out Slave In)
The name pretty much explains this line. This is the line where data is sent from the master device to the slave device. This line is shared by all slave devices.

### MISO (Master In Slave Out)
On this line the data is being sent out of the slave device received by master. This line is shared by all slave devices.

### SS/CS (Slave Select / Chip Select)
This line is referred to as SS and CS interchangeably. CS is active low, which means the slave device is active when CS is written low. Only one CS can be low at a time or there will be conflicts on the SPI bus resulting in garbage data. We use a pull-up resistor on the CS pin to set a default value.

A pull-up resistor is a large resistor (typically 10K) which bridges between VCC (3V3 in our case) and another pin. When no load is applied to the pin, no current flows through the resistor. This allows us to hold CS at a known (3V3) state when the CS pin isn't being driven by other circuitry. Then, we can drive another pin on the CS line low (GND) to select the device. Current will flow through the resistor and drop 3V3 across it.

Hopefully the diagram above now makes sense.
This is what a SPI transfer should look like:

![SPI signals on an oscilloscope](../figures/spi_scope.png)

I took this image from online but SPI transfers look very similar on our equipment.

I’ll pose the following question:

> *Which lines are which?*

|

You can’t really distinguish between MISO and MOSI in this picture but just pick one to be MOSI and the other to be MISO. What is being sent and received? The answer is given below.

> **Answer:**
>
> 1. The **yellow line is CS**. This is because it is being lowered before and raised after the SPI transfer is complete.
>
> 2. The **green line is SCK**. It oscillates 8 times for each byte sent.
>
> 3. The **pink and blue are MOSI and MISO**. The blue line has ```0x00``` and ```0x00``` and the pink line has ```0b10010101``` and ```0b01010101```.

## Using our SPI Library
Here are the basic software steps to send SPI messages:

1. Initialize CS as an output pin
2. Set CS high
3. Initialize SPI
4. Send SPI message
5. Set CS low
6. Send message
7. Set CS high



### Initializing CS as an output pin and writing CS high or low
On our microcontroller each IO pin has three registers that control it. We will only need to use two of them. There is the data direction register that controls if the pin is input our output and there is the port register that lets you write high or low on the pin.

Once you figure out what pin you are using for CS you can check the microcontroller’s datasheet to get the name of the pin. Figure 1 shows the pin configuration for the ATmega32m1 microcontroller.

![ATmega32m1 pinout](../figures/32m1.png)


There are four banks of ports (B, C, D and E) with eight pins on each. There are 8-bit data direction and port registers for each of the four ports. Each bit in the register is for a separate pin. The data direction register is called DDRx and the port register is just called PORTx where x is the port. So if you wanted to initialized PB6 as output the code would be the following.

![](../figures/ddrb.jpg)


The macro ```_BV(PB6)``` expands to ```1 << PB6``` and PB6 is a macro that expands to 6. Here is the code to write high or low on PB6.


![](../figures/portb.jpg)

In our SPI library we have functions that will do this for you. This is how you use them.

![](../figures/spi_fn.jpg)


### Initialize SPI
We have a function, ```init_spi()``` that does this. It initializes SCK and MOSI as output and sets the SCK frequency to 8 MHz / 64. 8 MHz is the frequency of the 32m1’s internal clock.

### Sending a SPI message
SPI sends 8 bit messages. If you want to send more than a byte you can send consecutive SPI messages. This is how you do it.

![](../figures/spi_msg.jpg)


### Example SPI Code
This a full SPI program that I used to make sure SPI was running correctly on the microcontroller.

![](../figures/spi_example_code.jpg)


This repeatedly sends ```10101010```.

### More Clock Settings
As I touched on earlier in this document, there are more clock settings.

![](../figures/spi_modes.jpg)

The two clock settings introduced here are Clock Polarity and Clock Phase. Clock Phase determines whether data is shifted in and out on the rising or falling edge of the data clock cycle. Clock Polarity determines determines whether the clock is idle when high or low.

This is the end of this SPI tutorial. Hopefully you you are now a SPI expert.

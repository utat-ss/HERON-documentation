Transceiver Interface and Debugging
===================================

The current tools we have to communicate with the transceiver, debug it, and safely handle it.

Safety
------

The transceiver is one of the most expensive pieces of equipment we have, and it must be treated with the appropriate care. The biggest risks in handling the transceiver, and mitigation strategies for each as follows:
* **Electrostatic Discharge**: electrostatic buildup in your body or tools is common, and its sudden discharge (usually referred to as `_ESD_, Electrostatic Discharge <https://en.wikipedia.org/wiki/Electrostatic_discharge>`_) can negatively affect any electronic component.
      - **Always handle the transceiver on top of ESD-proof mats**. These are the green rubber-ish mats that we have placed on all of our electronics workspaces. The transceiver, once out of its box, should go directly here.
      - **Discharge yourself**: Before handling the transceiver, touch a large, grounded metal surface to discharge any electrostatic buildup on your body
      - **Wear ESD wriststraps**. We should have these in the lab - they prevent electrostatic buildup on your body while you are working
* **Transmission Power**: the transceiver outputs a lot of power when trying to transmit.
    - **Do not send commands to the transceiver to transmit data unless there is an antennas connected to the transceiver's coax output**, or else you will damage the transceiver.

We have a protoboard from Endurosat that breaks out the PC104 pins from the transceiver header. **Carefully** connect the transceiver to the protoboard using the PC104 header (do it evenly from both sides so you don't damage the pins).

Turn on 5V on the power supply. Preferably use the dedicated 5V port so you can't accidentally use a different voltage. Connect 5V on the power supply to a breadboard, then to the +5V and GND header pins on the transceiver board. When you connect 5V, the transceiver will **briefly** flash its lights. The lights will then go off but it is still powered.

Connect a programmer using a USB cable to your computer. Connect the TX on the programmer to RxD on the transceiver board and the RX on the programmer to the TxD on the transceiver board (using male-male or male-female jumper wires). **Make sure to connect the GND pin on the programmer to the GND header on the transceiver board.**

We can't connect the transceiver coax output to the oscilloscope because the impedance isn't matched. We might be able to connect it to the spectrum analyzer (probably 50 ohm impedance matched) but we haven't tested it yet so **don't try this yet**.

Endurosat made a terminal app with predefined macros, available in the "Endurosat USB Contents" folder on the Google Drive. It should be easier to use this, but you can use CoolTerm or another terminal application if you want.

2018-12-08 testing results (sent followed by received):

ES+R2200
OK+0022B90303
Different from datasheet, assumptions are:
00 - probably RSSI
22- device addr
B9 - counter of number of reset cycles (kept increasing by 1 every time we turned it off and on)
0303 - contents of status register

ES+R2201
OK+0076620F41
frequency (435 MHz)

ES+W220700000001
OK
Checking frequency

ES+R2207
OK+0000000001

...
Set dest and src call signs (worked)

Transceiver's number of packets transmitted kept going up by 1 every second (in beacon mode, text beacon).
Received some data in GNU Radio but it didn't make sense, maybe not demodulating correctly or baud/sampling rate is wrong?

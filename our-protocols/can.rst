CAN Protocol
============

This protocol defines how CAN messages are structured for data requests between
OBC, PAY, and EPS. This is how OBC sends a message to either PAY or EPS to
request a particular piece of data, and how either PAY or EPS responds with the
appropriate data.

Subsystems/Microcontrollers
---------------------------

OBC - On-Board Computer

PAY - Payload

EPS - Electrical Power Systems

MOb Allocation
--------------

The limited number of CAN Message Objects that can be created on the ATmega32M1/64M1 (only 6) requires us to strictly allocate the purpose of each MOb. The allocation is done under the following assumptions:

* No MOb shall be reinitialized or renamed
* Only three subsystem microcontrollers are used, referred to as OBC, PAY, and EPS
* A message cannot have multiple recipients, but multiple senders can send messages to the same receiving MOb
* No two MObs on the bus can have the same ID

The first three mobs (0-2) are used for heartbeat, noting the following:

* Each subsystem receives on one MOB and sends on the other two mobs, i.e. two TX mobs send to one RX mob

The next three mobs (3-5) are used for commands, noting the following:

* MOB names are defined with respect to OBC
* PAY and EPS do not communicate with each other

The command MOb names are with respect to the OBC (e.g. PAY_CMD_MOB is from OBC to PAY, CMD_RESP_MOB is from PAY to OBC).

The MOBs are allocated as follows:

.. list-table::
    :header-rows: 1
    :stub-columns: 1

    * - Number
      - Name
      - OBC
      - PAY
      - EPS
    * - 0
      - OBC_HB_MOB
      - RX
      - TX
      - TX
    * - 1
      - PAY_HB_MOB
      - TX
      - RX
      - TX
    * - 2
      - EPS_HB_MOB
      - TX
      - TX
      - RX
    * - 3
      - PAY_CMD_MOB
      - TX
      - RX
      - N/A
    * - 4
      - EPS_CMD_MOB
      - TX
      - N/A
      - RX
    * - 5
      - CMD_RESP_MOB
      - RX
      - TX
      - TX

.. list-table:: The Message ID of every message is structured as the following:
    :header-rows: 1
    :stub-columns: 1

    * - Bits
      - 10-9
      - 8-3
      - 2-1
      - 0
    * -
      - Receiver ID
      - ``_BV(MOB #)``
      - Sender ID
      - RX(1) or TX(0)

.. list-table:: The Receiver and Sender IDs are:
    :header-rows: 1
    :stub-columns: 1

    * - Device
      - ID
    * - OBC
      - ``0b00``
    * - PAY
      - ``0b01``
    * - EPS
      - ``0b10``

Bits 8-3 are used to encode the universal MOb number, 0-5, in one-hot encoding. The ``_BV(MOb #)`` macro simply means ``000000001 << MOb #``. So, if ``MOb # = 5``, the value of bits 8-3 would be ``100000``, and for ``MOb # = 2`` it would be ``000100``.

Bit 0 is 0 if it is a Tx mailbox, or 1 if it is an Rx mailbox.

This allows a single universal mask to be valid for all mailboxes:
``CAN_RX_MASK_ID = 111 1111 1000``.
This allows selectivity in Receiver ID and MOb #, while remaining sender-agnostic.


Message Format
--------------

Each CAN message can have up to 8 bytes of data. Always transmit
all 8 bytes for simplicity and ignore the unused bytes.

Bytes within a CAN message are numbered so that the first byte sent is Byte 0
and the last byte sent is Byte 7.
Bits within a byte are numbered so that the rightmost (least significant) bit is Bit 0
and the leftmost (most significant) bit is Bit 7.

Every CAN message is 8 bytes, structured as follows:

Heartbeat
_________

TODO - update code

* Byte 0 - Unused
* Byte 1 - Sender
* Byte 2 - Receiver
* Byte 3 - Opcode (1 = ping request, 2 = ping response, 3 = restart count request, 4 = restart count response)
* Bytes 4-7 - Data (if responding to a restart count request)

Commands
________

* Byte 0 - Unused
* Byte 1 - Unused
* Byte 2 - Opcode
    * Broad category of type of message/command
* Byte 3 - Field Number
    * Specific ID/index of a sensor or command
    * always numbered sequentially starting at 0
    * e.g. which sensor to poll
* Bytes 4-7 - Data (if applicable)
    * When OBC makes a request for data, this is unused
    * When PAY or EPS responds with data, it goes here
    * If OBC sends an action command for setting something in EPS/PAY, there is data in the request but not the response
    * If data is not applicable, sending a response message with the appropriate Bytes 0-1 acts as a confirmation that the action has been performed
    * If the data is smaller than 32 bits, it is right-aligned to the least significant bits with padding zeros added on the left, e.g. a 12-bit value from an ADC (xxxx xxxxxxxx) is sent as a 32-bit value (00000000 00000000 0000xxxx xxxxxxxx)

Bytes 0-3 are identical both when the request is sent from OBC to PAY/EPS and when the response is sent from PAY/EPS to OBC. This is so OBC can match the request message with the response message and verify it is receiving the correct message.

Message Types
~~~~~~~~~~~~~

The data collection message types start at field 0 to match numbering and organization in memory.

Onboard Computer (OBC) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: N/A

Note that this does not use CAN; it is just included in this document for consistency in describing field numbering.

General state of the main OBC.

Field numbers:

.. list-table::
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Uptime
      - 0
      - In seconds
    * - Restart count
      - 1
      - Count
    * - Restart reason
      - 2
      - See data conversion protocol
    * - Restart date
      - 3
      - 0x00YYMMDD
    * - Restart time
      - 4
      - 0x00HHMMSS

TODO - make unknown restart reason = 0

Electrical Power Systems (EPS) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x01``

General data about the state of the power systems (voltage, current, temperature).

Field numbers:

.. list-table::
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Battery pack voltage
      - 0
      - ADC - EPS Voltage
    * - Battery pack current
      - 1
      - ADC - EPS Current
    * - X+ solar panel current
      - 2
      - ADC - EPS Current
    * - X- solar panel current
      - 3
      - ADC - EPS Current
    * - Y+ solar panel current
      - 4
      - ADC - EPS Current
    * - Y- solar panel current
      - 5
      - ADC - EPS Current
    * - Buck-boost (3.3V) converter voltage
      - 6
      - ADC - EPS Voltage
    * - Buck-boost (3.3V) converter current
      - 7
      - ADC - EPS Current
    * - Boost (5V) converter voltage
      - 8
      - ADC - EPS Voltage
    * - Boost (5V) converter current
      - 9
      - ADC - EPS Current
    * - Payload current
      - 10
      - ADC - EPS Current
    * - Battery temperature 1
      - 11
      - ADC - Temperature
    * - Battery temperature 2
      - 12
      - ADC - Temperature
    * - Buck-boost (3.3V) converter temperature
      - 13
      - ADC - Temperature
    * - Boost (5V) converter temperature
      - 14
      - ADC - Temperature
    * - Payload connector temperature
      - 15
      - ADC - Temperature
    * - Solar panel charging shunt states
      - 16
      - 4 bits - 0 = not shunting (charging on), 1 = shunting (charging off) - bit 3 = X+, bit 2 = X-, bit 1 = Y+, bit 0 = Y-
    * - Battery heater 1 - currently used setpoint
      - 17
      - DAC
    * - Battery heater 2 - currently used setpoint
      - 18
      - DAC
    * - IMU Gyroscope (Uncalibrated) - X-Axis
      - 19
      - IMU - Gyroscope
    * - IMU Gyroscope (Uncalibrated) - Y-Axis
      - 20
      - IMU - Gyroscope
    * - IMU Gyroscope (Uncalibrated) - Z-Axis
      - 21
      - IMU - Gyroscope
    * - IMU Gyroscope (Calibrated) - X-Axis
      - 22
      - IMU - Gyroscope
    * - IMU Gyroscope (Calibrated) - Y-Axis
      - 23
      - IMU - Gyroscope
    * - IMU Gyroscope (Calibrated) - Z-Axis
      - 24
      - IMU - Gyroscope
    * - Uptime
      - 25
      - In seconds
    * - Restart count
      - 26
      - Count
    * - Restart reason
      - 27
      - See ``uptime.h`` for constants (TODO put in document)


Electrical Power Systems (EPS) Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x02``

Control actions for the power system (e.g. temperature setpoints, battery charging).

Field numbers:

.. list-table::
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Ping
      - 0
      - N/A
    * - Get battery heater 1 - shadow setpoint
      - 1
      - DAC
    * - Set battery heater 1 - shadow setpoint
      - 2
      - DAC
    * - Get battery heater 2 - shadow setpoint
      - 3
      - DAC
    * - Set battery heater 2 - shadow setpoint
      - 4
      - DAC
    * - Get battery heater 1 - sun setpoint
      - 5
      - DAC
    * - Set battery heater 1 - sun setpoint
      - 6
      - DAC
    * - Get battery heater 2 - sun setpoint
      - 7
      - DAC
    * - Set battery heater 2 - sun setpoint
      - 8
      - DAC
    * - Get battery heater mode - lower current threshold
      - 9
      - ADC - EPS Current
    * - Set battery heater mode - lower current threshold
      - 10
      - ADC - EPS Current
    * - Get battery heater mode - upper current threshold
      - 11
      - ADC - EPS Current
    * - Set battery heater mode - upper current threshold
      - 12
      - ADC - EPS Current
    * - Reset
      - 13
      - N/A
    * - Read EEPROM
      - 14
      - EEPROM Address (OBC to EPS) or EEPROM data (EPS to OBC)
    * - Erase EEPROM
      - 15
      - EEPROM Address (OBC to EPS)
    * - Read RAM Byte
      - 16
      - RAM Address (OBC to EPS) or RAM data (EPS to OBC)
    * - Start temporary low-power mode (60 seconds)
      - 17
      - N/A
    * - Enable indefinite low-power mode
      - 18
      - N/A
    * - Disable indefinite low-power mode
      - 19
      - N/A

TODO - set limits for setpoints

Ping - Respond to a CAN message from OBC

Set EPS Heater DAC Setpoints - The satellite changes the DAC setpoints that control the EPS heaters for the batteries.

Set EPS Heater Mode Current Threshold - Sets the threshold of total (summed) solar panel current for which to switch the mode of shadow/sun for heater setpoints.

Payload (PAY) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x03``

General data about the state of the payload (temperature, pressure, humidity).

Field numbers:

.. list-table::
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Humidity sensor measurement
      - 0
      - Humidity
    * - Pressure sensor measurement
      - 1
      - Pressure
    * - Temperature sensor measurement (ambient)
      - 2
      - ADC - Thermistor
    * - Temperature sensor measurement (motor driver 1)
      - 3
      - ADC - Thermistor
    * - Temperature sensor measurement (motor driver 2)
      - 4
      - ADC - Thermistor
    * - Temperature sensor measurement (boost 10V inductor)
      - 5
      - ADC - Thermistor
    * - Temperature sensor measurement (boost 6V inductor)
      - 6
      - ADC - Thermistor
    * - MF chip thermistor temperatures (1 to 12)
      - 7 to 18
      - ADC - Thermistor
    * - Heater enabled states
      - 19
      - 5 bits - 1 bit for each heater state (0 = off, 1 = on) - bit 4 = heater 5, bit 0 = heater 1
    * - Limit switch measurements
      - 20
      - 4 bits - 1 bit for each switch state (0 = not pressed, 1 = pressed) - bit 3 = top 1, bit 2 = top 2, bit 1 = bot 1, bit 0 = bot 2
    * - Uptime
      - 21
      - In seconds
    * - Restart count
      - 22
      - Count
    * - Restart reason
      - 23
      - See ``uptime.h`` for constants


Payload (PAY) Optical
^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x04``

Optical sensor data from the experiment (wells with cells).

TODO - get mapping of field numbers

Field numbers:

.. list-table::
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - 32 wells
      - 0 to 31
      - Optical Sensor


Payload (PAY) Control
^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x05``

Control of payload functions and the experiment (e.g. temperature setpoints, deployment with motors popping blister packs and the proximity sensors to the actuation plate).

Field numbers:

.. list-table::
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Ping
      - 0
      - N/A
    * - Force heater 1 off
      - 1
      - N/A
    * - Force heater 1 on
      - 2
      - N/A
    * - Force heater 2 off
      - 3
      - N/A
    * - Force heater 2 on
      - 4
      - N/A
    * - Force heater 3 off
      - 5
      - N/A
    * - Force heater 3 on
      - 6
      - N/A
    * - Force heater 4 off
      - 7
      - N/A
    * - Force heater 4 on
      - 8
      - N/A
    * - Force heater 5 off
      - 9
      - N/A
    * - Force heater 5 on
      - 10
      - N/A
    * - Disable 6V boost converter
      - 11
      - N/A
    * - Enable 6V boost converter
      - 12
      - N/A
    * - Disable 10V boost converter
      - 13
      - N/A
    * - Enable 10V boost converter
      - 14
      - N/A
    * - Move actuation plate up
      - 15
      - N/A
    * - Move actuation plate down
      - 16
      - N/A
    * - Run blister pack deployment sequence
      - 17
      - N/A
    * - Reset
      - 18
      - N/A
    * - Read EEPROM
      - 19
      - EEPROM Address (OBC to PAY) or EEPROM data (PAY to OBC)
    * - Erase EEPROM
      - 20
      - EEPROM Address (OBC to PAY)
    * - Read RAM Byte
      - 21
      - RAM Address (OBC to PAY) or RAM data (PAY to OBC)
    * - Start temporary low-power mode (60 seconds)
      - 22
      - N/A
    * - Enable indefinite low-power mode
      - 23
      - N/A
    * - Disable indefinite low-power mode
      - 24
      - N/A


Ping - Respond to a CAN message from OBC

Force Heater x On/Off - Override the on-board heater control algorithm to command a certain heater to turn on or off

Disable/Enable Boost Converter - Turn off/on the boost converter to disable 10V or 6V power load

Move Actuation Plate Up/Down - Command the motors to turn and move actuation plate up or down

Run blister pack deployment sequence - Command the motors to turn in a pre-defined sequence to pop the blister packs and deploy the biological experiment

Reset - Reset the microcontroller ATMega64m1 on pay-ssm

Read EEPROM - Read 4-bytes of data in EEPROM

Erase EEPROM - Erase 4-bytes of data in EEPROM

Read RAM byte (in progress) - Read an arbitrary byte specified in RAM, used to check register status

Start Temporary Low-power Mode (60 seconds) - Turn off the 6V boost converter (heaters system) temperarily to run motors only

Enable/Disable Indefinite Low-poer Mode - Turn on/off the 10V boost converter (motors system)

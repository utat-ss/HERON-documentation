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

TODO - see code

Commands
________

* Byte 0 - Unused
* Byte 1 - Unused
* Byte 2 - Message Type
    * Broad category of type of message
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

TODO - implement 4-byte data in code

Message Types
~~~~~~~~~~~~~

The data collection message types start at field 0 to match numbering and organization in memory. The control message types start at field 1 to prevent commands from being accidentally being executed if sent with field 0 (except for ping which is field 0).

Electrical Power Systems (EPS) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x00``

General data about the state of the power systems (voltage, current, temperature).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Buck-boost converter output voltage
      - 0
      - ADC - EPS Voltage
    * - Buck-boost converter output current
      - 1
      - ADC - EPS Current
    * - -Y solar cell output current
      - 2
      - ADC - EPS Current
    * - +X solar cell output current
      - 3
      - ADC - EPS Current
    * - +Y solar cell output current
      - 4
      - ADC - EPS Current
    * - -X solar cell output current
      - 5
      - ADC - EPS Current
    * - Battery thermistor 1 temperature
      - 6
      - ADC - Thermistor
    * - Battery thermistor 2 temperature
      - 7
      - ADC - Thermistor
    * - Battery pack output voltage
      - 8
      - ADC - EPS Voltage
    * - Battery pack output current
      - 9
      - ADC - EPS Current
    * - Boost converter output current
      - 10
      - ADC - EPS Current
    * - Boost converter output voltage
      - 11
      - ADC - EPS Voltage
    * - Battery heater - setpoint 1
      - 12
      - DAC
    * - Battery heater - setpoint 2
      - 13
      - DAC
    * - IMU Gyroscope (Uncalibrated) - X-Axis
      - 14
      - IMU - Gyroscope
    * - IMU Gyroscope (Uncalibrated) - Y-Axis
      - 15
      - IMU - Gyroscope
    * - IMU Gyroscope (Uncalibrated) - Z-Axis
      - 16
      - IMU - Gyroscope
    * - IMU Gyroscope (Calibrated) - X-Axis
      - 17
      - IMU - Gyroscope
    * - IMU Gyroscope (Calibrated) - Y-Axis
      - 18
      - IMU - Gyroscope
    * - IMU Gyroscope (Calibrated) - Z-Axis
      - 19
      - IMU - Gyroscope
    * - Battery heater - shadow setpoint 1
      - 20
      - DAC
    * - Battery heater - shadow setpoint 2
      - 21
      - DAC
    * - Battery heater - sun setpoint 1
      - 22
      - DAC
    * - Battery heater - sun setpoint 2
      - 23
      - DAC


Electrical Power Systems (EPS) Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x01``

Control actions for the power system (e.g. temperature setpoints, battery charging).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Ping
      - 0
      - N/A
    * - Set battery heater - shadow setpoint 1
      - 1
      - DAC
    * - Set battery heater - shadow setpoint 2
      - 2
      - DAC
    * - Set battery heater - sun setpoint 1
      - 3
      - DAC
    * - Set battery heater - sun setpoint 2
      - 4
      - DAC
    * - Set battery heater mode - lower current threshold
      - 5
      - ADC - EPS Current
    * - Set battery heater mode - upper current threshold
      - 6
      - ADC - EPS Current
    * - Reset
      - 7
      - N/A
    * - Read EEPROM
      - 8
      - EEPROM Address (OBC to EPS) or EEPROM data (EPS to OBC)
    * - Erase EEPROM
      - 9
      - EEPROM Address (OBC to EPS)
    * - Get restart count
      - 10
      - Count
    * - Get restart reason
      - 11
      - See ``uptime.h`` for constants
    * - Get uptime
      - 12
      - In seconds
    * - Start temporary low-power mode (60 seconds)
      - 13
      - N/A


Payload (PAY) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x02``

General data about the state of the payload (temperature, pressure, humidity).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Temperature sensor measurement
      - 0
      - Temperature
    * - Humidity sensor measurement
      - 1
      - Humidity
    * - Pressure sensor measurement
      - 2
      - Pressure
    * - MF chip thermistor temperatures
      - 3 to 12
      - ADC - Thermistor
    * - MF chip heater - setpoint 1
      - 13
      - DAC
    * - MF chip heater - setpoint 2
      - 14
      - DAC
    * - Left motor proximity sensor measurement
      - 15
      - ADC - Actuation Distance
    * - Right motor proximity sensor measurement
      - 16
      - ADC - Actuation Distance


Payload (PAY) Optical
^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x03``

Optical sensor data from the experiment (wells with cells).

TODO - get mapping of field numbers

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - 32 wells
      - 0 to 31
      - Optical ADC


Payload (PAY) Control
^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x04``

Control of payload functions and the experiment (e.g. temperature setpoints, deployment with motors popping blister packs and the proximity sensors to the actuation plate).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Ping
      - 0
      - N/A
    * - Set MF chip heater - setpoint 1
      - 1
      - DAC
    * - Set MF chip heater - setpoint 2
      - 2
      - DAC
    * - Move actuation plate up
      - 3
      - N/A
    * - Move actuation plate down
      - 4
      - N/A
    * - Reset
      - 5
      - N/A
    * - Read EEPROM
      - 6
      - EEPROM Address (OBC to PAY) or EEPROM data (PAY to OBC)
    * - Erase EEPROM
      - 7
      - EEPROM Address (OBC to PAY)
    * - Get restart count
      - 8
      - Count
    * - Get restart reason
      - 9
      - See ``uptime.h`` for constants
    * - Get uptime
      - 10
      - In seconds
    * - Start temporary low-power mode (60 seconds)
      - 11
      - N/A


General Descriptions for CAN Commands (TODO)
--------------------------------------------

Ping - EPS/PAY respond to CAN messages from OBC

Set EPS Heater DAC Setpoints - The satellite changes the DAC setpoints that control the EPS heaters for the batteries.

Set PAY Heater DAC Setpoints - The satellite changes the DAC setpoints that control the PAY heaters for the cells.

Set EPS Heater Mode Current Threshold - Sets the threshold of total (summed) solar panel current for which to switch the mode of shadow/sun for heater setpoints.

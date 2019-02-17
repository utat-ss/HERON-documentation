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

The limited number of CAN Message Objects that can be created on the ATmega32M1 (only 6) requires us to strictly allocate the purpose of each MOb. The allocation is done under the following assumptions:

* No MOb shall be reinitialized or renamed
* Only three subsystem microcontrollers are used, referred to as OBC, EPS and PAY
* EPS and PAY do not communicate with each other
* A message cannot have multiple recipients, but multiple senders can send to the same MOb
* No two MObs on the bus can have the same ID

The MOb names are with respect to the OBC (e.g. STATUS_TX is transmitted from the OBC, STATUS_RX is received on the OBC).

.. list-table:: The MOBs are allocated as follows:
    :header-rows: 1
    :stub-columns: 1

    * - Number
      - Name
      - OBC
      - PAY
      - EPS
    * - 0
      - STATUS_TX_MOB
      - TX
      - RX
      - RX
    * - 1
      - STATUS_RX_MOB
      - RX
      - TX
      - TX
    * - 2
      - CMD_RX_MOB
      - RX
      - TX
      - TX
    * - 3
      - PAY_CMD_TX_MOB
      - TX
      - RX
      - N/A
    * - 4
      - EPS_CMD_TX_MOB
      - TX
      - N/A
      - RX
    * - 5
      - DATA_RX_MOB
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
all 8 bytes for simplicity and ignore the unused bytes. We should revisit this
later and consider using variable length messages.

Bytes within a CAN message are numbered so that the first byte sent is Byte 0
and the last byte sent is Byte 7.
Bits within a byte are numbered so that the rightmost (least significant) bit is Bit 0
and the leftmost (most significant) bit is Bit 7.

Every CAN message is 8 bytes, structured as follows:

* Byte 0 - Sender and Receiver
    * Which boards are sending and receiving the message
    * Might be necessary depending on MOB allocations if one board is sending a message to multiple other boards
* Byte 1 - Message Type
    * Broad category of type of message
* Byte 2 - Field Number
    * Specific ID/index of a sensor or command
    * always numbered sequentially starting at 0
    * e.g. which sensor to poll
* Bytes 3-5 - Data (if applicable)
    * When OBC makes a request for data, this is unused
    * When PAY or EPS responds with data, it goes here
    * If OBC sends an action command for setting something in EPS/PAY, there is data in the request but not the response
    * If data is not applicable, sending a response message with the appropriate Bytes 0-2 acts as a confirmation that the action has been performed
    * If the data is smaller than 24 bits, it is right-aligned to the least significant bits with padding zeros added on the left, e.g. a 12-bit value from an ADC (xxxx xxxxxxxx) is sent as a 24-bit value (00000000 0000xxxx xxxxxxxx)
* Bytes 6-7 - Unused

Byte 0 format (for IDs, see MOB allocations above):

.. list-table::
    :header-rows: 1

    * - Bits
      - 7-4
      - 3-2
      - 1-0
    * -
      - Unused
      - Sender ID
      - Receiver ID

Bytes 1-2 are identical both when the request is sent from OBC to PAY/EPS and when the response is sent from PAY/EPS to OBC. This is so OBC can match the request message with the response message and verify it is receiving the correct message.

Message Types
~~~~~~~~~~~~~


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
    * - Battery temperature - DAC setpoint 1
      - 12
      - DAC
    * - Battery temperature - DAC setpoint 2
      - 13
      - DAC
    * - IMU Acceleration - X-Axis
      - 14
      - IMU - Acceleration
    * - IMU Acceleration - Y-Axis
      - 15
      - IMU - Acceleration
    * - IMU Acceleration - Z-Axis
      - 16
      - IMU - Acceleration
    * - IMU Gyroscope - X-Axis
      - 17
      - IMU - Gyroscope
    * - IMU Gyroscope - Y-Axis
      - 18
      - IMU - Gyroscope
    * - IMU Gyroscope - Z-Axis
      - 19
      - IMU - Gyroscope
    * - IMU Magnetometer - X-Axis
      - 20
      - IMU - Magnetometer
    * - IMU Magnetometer - Y-Axis
      - 21
      - IMU - Magnetometer
    * - IMU Magnetometer - Z-Axis
      - 22
      - IMU - Magnetometer


Electrical Power Systems (EPS) Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x01``

Control actions for the power system (e.g. temperature setpoints, battery charging).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Set battery temperature - DAC setpoint 1
      - 0
      - DAC
    * - Set battery temperature - DAC setpoint 2
      - 1
      - DAC


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
    * - MF chip thermistors - DAC setpoint 1
      - 13
      - DAC
    * - MF chip thermistors - DAC setpoint 2
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

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - 36 wells
      - 0 to 35
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
    * - Set MF chip thermistors - DAC setpoint 1
      - 0
      - DAC
    * - Set MF chip thermistors - DAC setpoint 2
      - 1
      - DAC
    * - Level actuation plate
      - 2
      - N/A
    * - Pop blister packs
      - 3
      - N/A

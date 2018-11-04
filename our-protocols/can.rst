CAN Protocol
==============

MOb Allocation
--------------

The limited number of CAN Message Objects that can be created on the ATmega32M1 \(only 6\) requires us to strictly allocate the purpose of each MOb. The allocation is done under the following assumptions:

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

Every CAN message is 8 bytes, structured as follows:

* Byte 0 - Sender and Receiver
    * Which boards are sending and receiving the message
    * Might be necessary depending on MOB allocations if one board is sending a message to multiple other boards
* Byte 1 - Message Type
    * Broad category of type of message
* Byte 2 - Field Number
    * Specific ID of a sensor or command
* Bytes 3-5 - Data (optional)
    * When OBC makes a request for data, this is unused
    * When PAY or EPS responds with data, it goes here
* Bytes 6-7 - Unused

.. list-table:: Byte 0 format:
    :header-rows: 1

    * - Bits
      - 7-4
      - 3-2
      - 1-0
    * -
      - Unused
      - Sender ID (defined in MOB allocations above)
      - Receiver ID (defined in MOB allocations above)


Message Types
~~~~~~~~~~~~~


Electrical Power Systems (EPS) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x00``

General data about the state of the power systems (voltage, current, temperature).

**TODO** - sort out voltage/current/thermistor measurements

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Buck-boost converter output voltage
      - ``0x00``
      - ADS7952
    * - Buck-boost converter 1 output current
      - ``0x01``
      - ADS7952
    * - Buck-boost converter 2 output current
      - ``0x02``
      - ADS7952
    * - Boost converter output voltage
      - ``0x03``
      - ADS7952
    * - Boost converter 1 output voltage
      - ``0x04``
      - ADS7952
    * - Battery pack negative voltage
      - ``0x05``
      - ADS7952
    * - +X solar cell output current
      - ``0x06``
      - ADS7952
    * - -X solar cell output current
      - ``0x07``
      - ADS7952
    * - +Y solar cell output current
      - ``0x08``
      - ADS7952
    * - -Y solar cell output current
      - ``0x09``
      - ADS7952
    * - Battery pack positive voltage
      - ``0x0A``
      - ADS7952
    * - Battery output current
      - ``0x0B``
      - ADS7952
    * - Battery thermistor 1 voltage
      - ``0x0C``
      - ADS7952
    * - Battery thermistor 2 voltage
      - ``0x0D``
      - ADS7952


Payload (PAY) Housekeeping
^^^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x01``

General data about the state of the payload (temperature, pressure, humidity).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Temperature sensor measurement
      - ``0x00``
    * - Humidity sensor measurement
      - ``0x01``
    * - Pressure sensor measurement
      - ``0x02``


Payload (PAY) Optical
^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x02``

Optical sensor data from the experiment (wells with cells).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - 36 wells
      - ``0x00`` to ``0x23``
      - AD7194


Payload (PAY) Experiment
^^^^^^^^^^^^^^^^^^^^^^^^

Message Type: ``0x03``

Control of the experiment (deployment with motors popping blister packs and the proximity sensors to the actuation plate).

.. list-table:: Field numbers:
    :header-rows: 1

    * - Data
      - Field Number
      - Data Format
    * - Left motor proximity sensor measurement
      - ``0x00``
      - ADS7952
    * - Right motor proximity sensor measurement
      - ``0x01``
      - ADS7952
    * - Level actuation plate
      - ``0x02``
      - Confirmation
    * - Pop blister packs
      - ``0x03``
      - Confirmation


Data Formats
~~~~~~~~~~~~

Since we can't transmit floating-point numbers, we can only transmit integer representations of data (referred to as "raw data"). The conversion function must be performed by the receiver/ground station to get the meaningful values.

All data is right-aligned to the least significant bits (zero-padded on the left, most significant bits).

Conversion functions are defined in ``lib-common/src/conversions/conversions.c``.

.. list-table::
    :header-rows: 1

    * - Data Format
      - Data
    * - ADS7952 (ADC)
      - 12-bit raw data
    * - AD7194 (Optical ADC)
      - 24-bit raw data
    * - Confirmation (that an action has been performed)
      - 1 (success) or 0 (failure)

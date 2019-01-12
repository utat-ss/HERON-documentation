Ground Station to Satellite Protocol
====================================

The current protocol between the ground station and satellite, to be created one step at a time.

This integrates heavily with the memory protocol (in a separate document).

`AX25 Protocol <https://www.tapr.org/pub_ax25.html>`_

The ground station and transceiver both use the AX25 protocol to send messages.

All multi-byte data is big-endian.

TODO - need to figure out if we need to supply any header/status information for the AX25 protocol itself, but this document currently only covers the content of the messages itself.

TODO - get satellite to send ACK immediately after receiving a message

TODO - add checksum

Each message contains the following:

- Byte 0 - 0x00 (special character to indicate start of message)
- Byte 1 - number of characters in message (to follow after this byte, not including the first 2 bytes) - 0x00 is invalid
- Byte 2 - Command type


Command Summary
---------------

.. list-table::
    :header-rows: 1

    * - Command type
      - Name
    * - 0x00
      - Status/ping
    * - 0x01
      - Reset
    * - 0x02
      - Low-power mode
    * - 0x03
      - EPS Housekeeping - Block
    * - 0x04
      - EPS Housekeeping - Field
    * - 0x05
      - PAY Housekeeping - Block
    * - 0x06
      - PAY Housekeeping - Field
    * - 0x07
      - PAY Optical - Block
    * - 0x08
      - PAY Optical - Field
    * - 0x09
      - PAY Experiment - Actuation Distances
    * - 0x0A
      - PAY Experiment - Actuate
    * - 0x0B
      - Experiment - Automatic Data Collection
    * - 0x0C
      - Read Memory
    * - 0x0D
      - Write Memory
    * - 0x0E
      - Set EPS Heater DAC Setpoints
    * - 0x0F
      - Set PAY Heater DAC Setpoints



Command Details
---------------

Status/ping
^^^^^^^^^^^

If the satellite receives this command, it responds with data "UTAT" (no termination).

- Bytes 3-6 (response only) - "UTAT"


Reset
^^^^^

Resets everything in the satellite.


Low-power mode
^^^^^^^^^^^^^^

Puts the entire satellite in low-power mode.


EPS Housekeeping - Block
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of EPS housekeeping data stored in flash memory.

- Byte 3 - block number
- Bytes 4-39 (response only) - data (12 fields, 3 bytes each)


EPS Housekeeping - Field
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified field of EPS housekeeping data.

- Byte 3 - field number (0 to 11)
- Bytes 4-6 (response only) - data (1 field, 3 bytes)


PAY Housekeeping - Block
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of PAY housekeeping data stored in flash memory.

- Byte 3 - block number
- Bytes 4-12 (response only) - data (3 fields, 3 bytes each)


PAY Housekeeping - Field
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified field of PAY housekeeping data.

- Byte 3 - field number (0 to 2)
- Bytes 4-6 (response only) - data (1 field, 3 bytes)


PAY Optical - Block
^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of PAY optical data stored in flash memory.

- Byte 3 - block number
- Bytes 4-111 (response only) - data (36 fields, 3 bytes each)


PAY Optical - Field
^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified field of PAY optical data.

- Byte 3 - field number (0 to 35)
- Bytes 4-6 (response only) - data (1 field, 3 bytes)


PAY Experiment - Actuation Distances
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the PAY actuation plate distance data.

- Bytes 3-8 (response only) - data (2 fields, 3 bytes each)


PAY Experiment - Actuate
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite starts the experiment. Actuates the motors to pop the blister packs. Starts a timer which will go off every 30 minutes, collecting all data in the satellite and storing it in memory.

- Byte 3 - 0x00 (align plate only) or 0x01 (pop blister packs)


Experiment - Automatic Data Collection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Turns off or on automatic data collection manually.

- Byte 3 - 0x00 (disable automatic data collection) or 0x01 (enable automatic data collection)


Read Memory
^^^^^^^^^^^

The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes.

- Bytes 3-6 - 32-bit address
- Byte 7 - 8-bit count (number of bytes)
- Bytes 8-(`count`+8-1) (response only) - data (`count` bytes)


Write Memory
^^^^^^^^^^^^

The satellite writes the specified data to flash memory starting at the specified address and with the specified number of bytes.

- Bytes 3-6 - 32-bit address
- Byte 7 - 8-bit count (number of bytes)
- Bytes 8-(`count`+8-1) (request only) - data (`count` bytes)


Set EPS Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the EPS heaters for the batteries

- Bytes 3-5 - Setpoint 1 (12 bits)
- Bytes 6-8 - Setpoint 2 (12 bits)


Set PAY Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the PAY heaters for the cells.

- Bytes 3-5 - Setpoint 1 (12 bits)
- Bytes 6-8 - Setpoint 2 (12 bits)

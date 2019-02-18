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

Tentatively (until we figure out AX25), each message (raw bytes) contains the following:

- Byte 0 - 0x00 (special character to indicate start of message)
- Byte 1 - number of characters in message (to follow after this byte, not including the first 2 bytes) - 0x00 is invalid
- Byte 2-... - Message contents

The message contents (the data we actually care about, independent of any encoding protocol, checksum, etc.):

- Byte 0 - Message type
- Bytes 1-4 (32-bit int) - Argument 1 (may be ignored but is still sent)
- Bytes 5-8 (32-bit int) - Argument 2 (may be ignored but is still sent)

Only for messages from the satellite to the ground station:

- Bytes 9-... - Data (length depends on message type)


Commands
--------

Status/ping
^^^^^^^^^^^

If the satellite receives this command, it responds with data "UTAT" (no string termination).

- Message type - 0x00
- Data - 4 bytes - "UTAT"

EPS Housekeeping - Collect Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Triggers EPS HK data collection of a block and writes it to flash memory.

- Message type - 0x01
- Data - 4 bytes - block number

EPS Housekeeping - Read Local Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Reads the block of EPS HK data stored locally in the microcontroller's program memory.

- Message type - 0x02
- Data - 77 bytes - header (8 bytes), 23 fields (3 bytes each)

EPS Housekeeping - Read Memory Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of EPS HK data stored in flash memory.

- Message type - 0x03
- Argument 1 - block number
- Data - 77 bytes - header (8 bytes), 23 fields (3 bytes each)

PAY Housekeeping - Collect Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Triggers PAY HK data collection of a block and writes it to flash memory.

- Message type - 0x04
- Data - 4 bytes - block number

PAY Housekeeping - Read Local Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Reads the block of PAY HK data stored locally in the microcontroller's program memory.

- Message type - 0x05
- Data - 59 bytes - header (8 bytes), 17 fields (3 bytes each)

PAY Housekeeping - Read Memory Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of PAY HK data stored in flash memory.

- Message type - 0x06
- Argument 1 - block number
- Data - 59 bytes - header (8 bytes), 17 fields (3 bytes each)

PAY Optical - Collect Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Triggers PAY OPT data collection of a block and writes it to flash memory.

- Message type - 0x07
- Data - 4 bytes - block number

PAY Optical - Read Local Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Reads the block of PAY OPT data stored locally in the microcontroller's program memory.

- Message type - 0x08
- Data - 116 bytes - header (8 bytes), 36 fields (3 bytes each)

PAY Optical - Read Memory Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of PAY OPT data stored in flash memory.

- Message type - 0x09
- Argument 1 - block number
- Data - 116 bytes - header (8 bytes), 36 fields (3 bytes each)

PAY Control - Actuate Motors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Actuates the motors in the payload.

- Message type - 0x0A
- Argument 1 - 1 (move plate up) or 2 (move plate down)

Experiment - Automatic Data Collection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Turns off or on automatic data collection for each type of data.

- Message type - 0x0B
- Argument 1 (0 to disable, 1 to enable) - bit 2 (EPS HK), bit 1 (PAY HK), bit 0 (PAY OPT)

Read Memory
^^^^^^^^^^^

The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes.

- Message type - 0x0C
- Argument 1 - starting address (in bytes)
- Argument 2 - count (number of bytes)
- Data - `count` bytes - read data

Erase Memory
^^^^^^^^^^^^

The satellite erases the flash memory (sets every byte to 0xFF, i.e. all 1's) starting at the specified address and for the specified number of bytes.

- Message type - 0x0D
- Argument 1 - starting address (in bytes)
- Argument 2 - count (number of bytes)

Set EPS Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the EPS heaters for the batteries.

- Message type - 0x0E
- Argument 1 - Setpoint 1 (12 bits)
- Argument 2 - Setpoint 2 (12 bits)


Set PAY Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the PAY heaters for the cells.

- Message type - 0x0F
- Argument 1 - Setpoint 1 (12 bits)
- Argument 2 - Setpoint 2 (12 bits)


Ideas for Future Commands
-------------------------

Reset
^^^^^

Resets everything in the satellite.


Low-power mode
^^^^^^^^^^^^^^

Puts the entire satellite in low-power mode.

EPS Housekeeping - Field
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified field of EPS housekeeping data.

- Byte 3 - field number (0 to 11)
- Bytes 4-6 (response only) - data (1 field, 3 bytes)

PAY Housekeeping - Field
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified field of PAY housekeeping data.

- Byte 3 - field number (0 to 2)
- Bytes 4-6 (response only) - data (1 field, 3 bytes)

PAY Optical - Field
^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified field of PAY optical data.

- Byte 3 - field number (0 to 35)
- Bytes 4-6 (response only) - data (1 field, 3 bytes)

Write Memory
^^^^^^^^^^^^

The satellite writes the specified data to flash memory starting at the specified address and with the specified number of bytes.

- Bytes 3-6 - 32-bit address
- Byte 7 - 8-bit count (number of bytes)
- Bytes 8-(`count`+8-1) (request only) - data (`count` bytes)

PAY Experiment - Actuate
^^^^^^^^^^^^^^^^^^^^^^^^

The satellite starts the experiment. Actuates the motors to pop the blister packs. Starts a timer which will go off every 30 minutes, collecting all data in the satellite and storing it in memory.

- Byte 3 - 0x00 (align plate only) or 0x01 (pop blister packs)

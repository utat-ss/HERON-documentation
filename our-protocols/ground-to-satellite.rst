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

Constants
---------

Subsystem
^^^^^^^^^

One of the three subsystems of the satellite. (TODO - PAY SSM/Optical?)

- 0 - OBC
- 1 - EPS
- 2 - PAY

Block Type
^^^^^^^^^^

This is used as an argument in some commands to identify a type of data.

- 0 - EPS HK
- 1 - PAY HK
- 2 - PAY OPT

Commands
--------

Status/ping
^^^^^^^^^^^

If the satellite receives this command, it responds with data "UTAT" (no string termination).

- Message type - 0x00
- Data - 4 bytes - "UTAT"

Get Restart Count, Restart Date/Time, Uptime
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the restart count (number of times OBC has restarted its program), restart date/time (RTC date/time of most recent restart), and uptime (time since most recent restart)

- Message type - 0x01
- Data - 14 bytes - restart count (4 bytes), restart date (3 bytes), restart time (3 bytes), uptime (4 bytes)

Get RTC Date/Time
^^^^^^^^^^^^^^^^^

- Message type - 0x02
- Data - 6 bytes - date YY, date MM, date DD, time HH, time MM, time SS

Set RTC Date/Time
^^^^^^^^^^^^^^^^^

- Message type - 0x03
- Argument 1 - date (8 bits YY, 8 bits MM, 8 bits DD)
- Argument 2 - time (8 bits HH, 8 bits MM, 8 bits SS)

Read Memory
^^^^^^^^^^^

The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes.

- Message type - 0x04
- Argument 1 - starting address (in bytes)
- Argument 2 - count (number of bytes)
- Data - `count` bytes - read data

Erase Memory
^^^^^^^^^^^^

The satellite erases the flash memory (sets every byte to 0xFF, i.e. all 1's) starting at the specified address and for the specified number of bytes.

- Message type - 0x05
- Argument 1 - starting address (in bytes)
- Argument 2 - count (number of bytes)

Collect Block
^^^^^^^^^^^^^

Triggers data collection of a block and writes it to flash memory.

- Message type - 0x06
- Argument 1 - block type
- Data - 4 bytes - block number

Read Local Block
^^^^^^^^^^^^^^^^

Reads the block of data stored locally in the microcontroller's program memory.

- Message type - 0x07
- Argument 1 - block type
- Data - 10 bytes (header) + (3 bytes * number of fields) - 79 bytes (EPS HK) or 61 bytes (PAY HK) or 118 bytes (PAY OPT)

Read Memory Block
^^^^^^^^^^^^^^^^^

The satellite sends back the specified block of data stored in flash memory.

- Message type - 0x08
- Argument 1 - block type
- Argument 2 - block number
- Data - 10 bytes (header) + (3 bytes * number of fields) - 79 bytes (EPS HK) or 61 bytes (PAY HK) or 118 bytes (PAY OPT)

Automatic Data Collection - Enable/Disable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Turns off or on automatic data collection for one type of data.

- Message type - 0x09
- Argument 1 - block type
- Argument 2 - 0 (disable) or 1 (enable)

Automatic Data Collection - Period
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the automatic data collection period for one type of data.

- Message type - 0x0A
- Argument 1 - block type
- Argument 2 - period (in seconds)

Automatic Data Collection - Resync
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Resynchronizes timers for data collection for all types of data so they start counting at the same time.

- Message type - 0x0B

Set EPS Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the EPS heaters for the batteries.

- Message type - 0x0C
- Argument 1 - 0 or 1
- Argument 2 - Setpoint (12 bits)

Set PAY Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the PAY heaters for the cells.

- Message type - 0x0D
- Argument 1 - 0 or 1
- Argument 2 - Setpoint (12 bits)

PAY Control - Actuate Motors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Actuates the motors in the payload.

- Message type - 0x0E
- Argument 1 - 1 (move plate up) or 2 (move plate down)

Reset
^^^^^

Resets the microcontroller for the specified subsytem (makes it restart its program)

- Message type - 0x0F
- Argument 1 - subsystem
- No response message back to ground station

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

PAY Experiment - Actuate
^^^^^^^^^^^^^^^^^^^^^^^^

Actuates the motors to pop the blister packs.

- Byte 3 - 0x00 (align plate only) or 0x01 (pop blister packs)

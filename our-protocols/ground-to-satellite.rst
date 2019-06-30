Ground Station to Satellite Protocol
====================================

The current protocol for communication of packets between the ground station and satellite.

This integrates heavily with the memory protocol (in a separate document).

`AX25 Protocol <https://www.tapr.org/pub_ax25.html>`_

All multi-byte data is big-endian.

OBC only deals with the message component of any packet, i.e. for RX it only receives the message and for TX is only sends the message. The transceiver handles all of the packetization, including header/status information and checksum.

Beacon mode: Wraps the message using the AX25 protocol.

PIPE mode: Does not wrap the message using the AX25 protocol.

TODO - get satellite to send ACK immediately after receiving a message

The transceiver terminates a message when the ``\r`` (i.e. ``<CR>``, decimal 13, hex 0x0D) character is sent. Therefore, we can't sent raw messages because they will often contain the byte 0x0D. Therefore, represent each raw byte (number 0-255) as 2 bytes in an ASCII hex representation, similar to how the transceiver's ES+ commands work. e.g. the one byte 0xA3 is represented as "A3", i.e. two bytes 0x41 0x33. Always use the capital letter rather than the lowercase letter representations.

The **decoded message** is the message information that we actually care about:

- Byte 0 - Message type
- Bytes 1-4 (32-bit int) - Argument 1 (may be ignored but is still sent)
- Bytes 5-8 (32-bit int) - Argument 2 (may be ignored but is still sent)
- Bytes 9-... - Data (only for satellite to ground, length depends on message type)

The **encoded message** is the full message (called "message" in the packetization protocol):

- Byte 0 - 0x00 (special character to indicate start of message)
- Byte 1 - number of characters in message (to follow after this byte, not including this byte or the previous byte) - 0x00 is invalid - this can't be 0x0D because it must be at least 18 (0x12)
- Byte 2-... - Decoded message (in ASCII hex representation, twice the number of bytes as the decoded message)

If the decoded message has ``n`` bytes, the encoded message always has ``2n + 2`` bytes.

Even though the transceiver only accepts packets with a valid checksum, we add the start and count bytes in case characters are dropped over UART between the OBC and transceiver.




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

Ping one of the subsystems to see if it responds. Should be used to check OBC responds to transceiver messages and EPS/PAY respond to CAN messages from OBC.

- Message type - 0x00
- Argument 1 - Subsystem

Get Restart Count, Restart Date/Time, Uptime
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the restart count (number of times OBC has restarted its program), restart date/time (RTC date/time of most recent restart), and uptime (time since most recent restart) for the specified subsystem.

- Message type - 0x01
- Argument 1 - Subsystem
- Data
    - If OBC - 14 bytes - restart count (4 bytes), restart date (3 bytes), restart time (3 bytes), restart reason (1 byte), uptime (4 bytes)
    - If EPS/PAY - 8 bytes - restart count (4 bytes), restart reason (1 byte), uptime (4 bytes)

TODO - make unknown restart reason = 0

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

The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes. The maximum number of bytes that can be read in one command is 106 bytes (to match the biggest block type of PAY_OPT, 10 byte header + 32 fields * 3 bytes, don't want to make the message buffers on OBC any longer).

- Message type - 0x04
- Argument 1 - starting address (in bytes)
- Argument 2 - count (number of bytes)
- Data - `count` bytes - read data

Erase Memory Sector
^^^^^^^^^^^^^^^^^^^

The satellite erases one sector (4 kB) of the flash memory (sets every byte to 0xFF, i.e. all 1's). This will happen for the 4 kB sector that includes the specified address, aligned to a 4 kB boundary.

- Message type - 0x05
- Argument 1 - address (in bytes) - ideally this should be specified as aligned to a 4 kB boundary, but will work nonetheless

Collect Block
^^^^^^^^^^^^^

Triggers data collection of a block and writes it to flash memory on OBC.

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

Sets the automatic data collection period for one type of data. Must have `period >= 30` or else the state of OBC will not change. This is to prevent data collection from triggering too frequently and constantly filling up the command/CAN queues.

- Message type - 0x0A
- Argument 1 - block type
- Argument 2 - period (in seconds)

Automatic Data Collection - Resync
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Resynchronizes timers for data collection for all types of data so they start counting at the same time (reset all to 0, counting up).

- Message type - 0x0B

Set EPS Heater DAC Setpoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite changes the DAC setpoints that control the EPS heaters for the batteries.

- Message type - 0x0C
- Argument 1 - 0 (heater 1 shadow), 1 (heater 2 shadow), 2 (heater 1 sun), 3 (heater 2 sun)
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

Resets the microcontroller for the specified subsytem (intentionally runs out the watchdog timer to make it restart its program).

- Message type - 0x0F
- Argument 1 - subsystem
- If resetting OBC, no response message back to ground station

It is recommended that the ground station team sends a follow-up message to check the uptime/restart time of the subsystem that should have been reset.

Send CAN Message - EPS
^^^^^^^^^^^^^^^^^^^^^^

OBC sends a CAN message (8 bytes) to EPS and gets a response (8 bytes) back.

- Message type - 0x10
- Argument 1 - first 4 bytes of message to send
- Argument 2 - last 4 bytes of message to send
- Data (8 bytes) - response from EPS

Ideas for use cases:

- Request a single field of EPS_HK data (in case the block collection of all measurements at once fails).

Send CAN Message - PAY
^^^^^^^^^^^^^^^^^^^^^^

OBC sends a CAN message (8 bytes) to PAY and gets a response (8 bytes) back.

- Message type - 0x11
- Argument 1 - first 4 bytes of message to send
- Argument 2 - last 4 bytes of message to send
- Data (8 bytes) - response from PAY

Read EEPROM
^^^^^^^^^^^

Reads 4 bytes (a `dword` i.e. double word) from EEPROM memory of the specified subsystem.

- Message type - 0x12
- Argument 1 - subsystem
- Argument 2 - 32-bit address
- Data (4 bytes) - read data

Get Current Block Number
^^^^^^^^^^^^^^^^^^^^^^^^

Gets the current block number for the specified block type. The block number represents the index of the block that will be written to memory the next time collection is triggered for that section, i.e. if the current block number is x, blocks 0 to (n-1) have already been collected and written to memory but block x has not.

- Message type - 0x13
- Argument 1 - block type
- Data (4 bytes) - block number

Set Current Block Number
^^^^^^^^^^^^^^^^^^^^^^^^

Sets the current block number for the specified block type. The block number represents the index of the block that will be written to memory the next time collection is triggered for that section, i.e. if the current block number is x, blocks 0 to (n-1) have already been collected and written to memory but block x has not. This could be used to skip sections of flash memory that are found to be malfunctioning, to reset the block number to 0 when a section reaches the end of its memory and all existing data has already been safely downlinked, or ran when the start address of a section has been changed.

- Message type - 0x14
- Argument 1 - block type
- Argument 2 - block number

Set Memory Section Start Address
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the starting address of a section in OBC flash memory. This could be used if one of the memory chips is found to be malfunctioning in orbit, allowing us to remap the memory sections from ground. Note that changing this will blindly overwrite any data previously in that part of memory.

NOTE: This should be run consecutively with the "Set Memory Section End Address" command.

- Message type - 0x15
- Argument 1 - block type
- Argument 2 - start address

Set Memory Section End Address
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the end address of a section in OBC flash memory. See above for motivation.

NOTE: This should be run consecutively with the "Set Memory Section Start Address" command.

- Message type - 0x16
- Argument 1 - block type
- Argument 2 - end address

Erase EEPROM
^^^^^^^^^^^^

Erases 4 bytes (a `dword` i.e. double word) in EEPROM memory of the specified subsystem (sets to all 1's, i.e. 0xFFFFFFFF).

- Message type - 0x17
- Argument 1 - subsystem
- Argument 2 - 32-bit address (in bytes)

NOTE: Be careful using this, because for example it could force OBC to re-run its initial 30-minute comms delay and try to deploy the antenna again.

Set EPS Heater Mode Current Threshold
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the threshold of total (summed) solar panel current for which to switch the mode of shadow/sun for heater setpoints.

- Message type - 0x18
- Argument 1 - 0 (lower), 1 (upper)
- Argument 2 - Current (12 bits, in ADC format)

Erase All Memory
^^^^^^^^^^^^^^^^

The satellite erases all flash memory on all 3 chips (sets every byte to 0xFF, i.e. all 1's). This would generally be used when changing the satellite's current block number, allowing it to rewrite to addresses that were previously written to.

BE VERY CAREFUL WITH THIS!!

- Message type - 0x19

Ideas for Future Commands
-------------------------

Low-power mode
^^^^^^^^^^^^^^

Puts the entire satellite in low-power mode.

Ground Station to Satellite Protocol
====================================

The current protocol for communication of packets between the ground station and satellite.

This integrates heavily with the memory protocol (in a separate document).

`AX25 Protocol <https://www.tapr.org/pub_ax25.html>`_

All multi-byte data is big-endian.

OBC only deals with the message component of any packet, i.e. for RX it only receives the message and for TX is only sends the message. The transceiver handles all of the packetization, including header/status information and checksum.

Beacon mode: Wraps the message using the AX25 protocol.

PIPE mode: Does not wrap the message using the AX25 protocol.

The transceiver terminates a message when the ``\r`` (i.e. ``<CR>``, decimal 13, hex 0x0D) character is sent. Therefore, we can't sent raw messages because they will often contain the byte 0x0D. Therefore, represent each raw byte (number 0-255) as 2 bytes in an ASCII hex representation, similar to how the transceiver's ES+ commands work. e.g. the one byte 0xA3 is represented as "A3", i.e. two bytes 0x41 0x33. Always use the capital letter rather than the lowercase letter representations.

Decoded Message Formats
-----------------------

The **decoded message** is the message information that we actually care about.

The process for the ground station initiating communication and receiving response it as follows.

Ground to satellite request:
- Byte 0 - Message type
- Bytes 1-4 (32-bit int) - Argument 1 (may be ignored but is still sent)
- Bytes 5-8 (32-bit int) - Argument 2 (may be ignored but is still sent)
- Bytes 9-12 (32-bit int) - Password (may be ignored but is still sent)

The password is only checked for sensitive commands.

Satellite to ground acknowledgement:
- Byte 0 - Message type
- Bytes 1-4 (32-bit int) - Argument 1
- Bytes 5-8 (32-bit int) - Argument 2
- Bytes 9 - Status - 0 = OK (either correct password or don't care about password for that command), 1 = Invalid Password, 2 = Invalid Command

The acknowledgement should be sent almost immediately after the satellite receives the request, even if it is currently in the process of executing another command. There is a delay between the acknowledgement and response to execute the command (depends on command type, e.g. collect block takes the longest by far).

Satellite to ground response:
- Byte 0 - Message type
- Bytes 1-4 (32-bit int) - Argument 1
- Bytes 5-8 (32-bit int) - Argument 2
- Bytes 9-... - Data (length depends on message type)

Message Encoding
----------------

The **encoded message** is the full message (called "message" in the packetization protocol), i.e. what is actually sent through the transceiver and over the air.

- Byte 0 - 0x00 (special character to indicate start of message)
- Byte 1 - number of bytes in message (starting at byte 3 but not including the last byte, i.e. subtract 4 from total number of characters) - 0x00 is invalid
- Byte 2 - 0x00 (special character to separate length from message contents)
- Byte 3-... - Decoded message with base-254 algorithm applied (see below)
- Byte (n-1) - 0x00 (special character to indicate end of message)

Even though the transceiver only accepts packets with a valid checksum, we add the start and count bytes in case characters are dropped over UART between the OBC and transceiver.

TODO - will the length ever be 13? Maybe add an extra byte for padding and just have minimum rather than exact length.

Base-254 Algorithm
^^^^^^^^^^^^^^^^^^

This algorithm takes a sequence of bytes in base-256 and represents them in base-254.

Motivation
++++++++++

The very large majority of packets/bytes we send in either direction will be downlinking collected data from the sat to ground. They are by far the largest packets and will probably be most of the commands we execute. To try to reduce the number of bytes we need to send, we can do things like omitting command arguments if they don’t apply, but this encoding scheme has a much bigger impact since it applies to all bytes in all messages.

Terminology
+++++++++++

We use “decoded message”/“decoded bytes” to refer to the data (raw bytes, each between 0-255) that we actually care about sending. We use “encoded message”/“encoded bytes” to refer to the bytes we actually send in the packet, which is then wrapped in the EnduroSat packet format.

Remember/learn the meaning of the term "base" (also called "radix"), i.e. the number of possible values that one digit in a number can be. Binary is base-2, decimal is base-10, hexadecimal is base-16, etc.

Constraints
+++++++++++

An encoded message can never contain a byte with the value 13 (0x0D) because EnduroSat uses that as the character to indicate the end of a message.

We choose to not allow an encoded message to contain the byte 0 (0x00) because we use that as a special character/delimiter for messages. so 0x00 clearly denotes the beginning of message rather than possibly being a part of the message.

Algorithm
+++++++++

Consider we have a sequence of an arbitrary number of decoded bytes (which we will call base-256, each can take on a value between 0 and 255) that we want to send. We can’t send it in this format since the value 13 is not allowed. Now consider we split this sequence into 7-byte sections, and have a final section for the leftover bytes (i.e. `n % 7` bytes).

We will start with how to encode each 7-byte section and discuss the remainder section later. We encode each 7-byte base-256 section as an 8-byte base-254 section, where each byte can take one of 254 possible values, i.e. decoded 0-11 are represented as encoded 1-12, but decoded 12-253 are represented as encoded 14-255. Then we have a sequence of 8 base-254 digits which are treated as a single unsigned integer in big-endian format. Using standard rules for numbers with different bases, you can take 8 bytes in base-254 (say we have an array uint8_t seq[8]) and convert it to an integer as follows: ``seq[0] * (254^7) + seq[1] * (254^6) + ... + seq[7]``.

Note that the most significant byte in base-254 will usually be 0, and sometimes 1.

Why choose base-254? This is because we have two values (0 and 13) out of 256 that we want to avoid.

Why choose to group bytes such that decoded 7-byte base-256 -> encoded 8-byte base-254? This is because the biggest integer we can represent in software and do math on is 64 bits, i.e. ```uint64_t`` in C.

Why not use 8-byte -> 9-byte? We could do this, but it would assume that the ground station always sends the most significant byte in base-254 as either 0 or 1. Just in case it accidentally sends a higher number, we don't want to overflow a `uint64_t` in the satellite's software.

For the remaining bytes after creating 7-byte groups (e.g. say n = num_bytes % 7), we apply the same algorithm but encode the decoded `n` bytes -> encoded `n+1` bytes to ensure we can always represent all possible values of the bytes.

We should have a ratio of encoded bytes to decoded bytes of around 8:7.

If the decoded message has ``N`` bytes, the encoded message length is as follows:
- If num_bytes % 7 == 0 -> (N / 7) * 8
- If num_bytes % 7 != 0 -> (floor(N / 7) * 8) + ((N % 7) + 1)

Example
+++++++

Say we have a 9-byte decoded message, in hex ``f3:ff:34:9e:1e:28:9a:6e:b7``. We split up the message into a single 7-byte chunk and a leftover 2-byte chunk, and apply this procedure.

Note that ``^`` represents a power operation, not XOR.

Take the 7-byte chunk: ``f3:ff:34:9e:1e:28:9a``. We treat this as an unsigned 64-bit integer in big-endian format. Now calculate that 64-bit integer: 0xF3*256^6 + 0xFF*256^5 + 0x34*256^4 +0x9E*256^3 + 0x1E*256^2 + 0x28*256^1 + 0x9A*256^0 = 68679020796848282.

Now let's take that integer and represent as 8 values in base-254:
(note mod(a ; b) = a % b)
mod(floor(68679020796848282 / 254^7) ; 254) = 1
mod(floor(68679020796848282 / 254^6) ; 254) = 1
mod(floor(68679020796848282 / 254^5) ; 254) = 191
mod(floor(68679020796848282 / 254^4) ; 254) = 106
mod(floor(68679020796848282 / 254^3) ; 254) = 189
mod(floor(68679020796848282 / 254^2) ; 254) = 199
mod(floor(68679020796848282 / 254^1) ; 254) = 13
mod(floor(68679020796848282 / 254^0) ; 254) = 0

Now to escape (not send through the transceiver) the bytes 0 and 13, we apply the mapping of 0-11 -> 1-12 and 12-253 -> 14-255. Now we get:
1 -> 2 = 0x02
1 -> 2 = 0x02
191 -> 193 = 0xC1
106 -> 108 = 0x6C
189 -> 191 = 0xBF
199 -> 201 = 0xC9
13 -> 15 = 0x0F
0 -> 1 = 0x01

Now we have the actual 8 bytes we send over the air (in hex): ``02:02:c1:6c:bf:c9:0f:01``

Apply the same procedure to the remaining 2 byte chunk, ``6e:b7``, changing 2-byte base-256 to 3-byte base-254.

0x6E*256^1 + 0xB7*256^0 = 28343

mod(floor(28343 / 254^2) ; 254) = 0
mod(floor(28343 / 254^1) ; 254) = 111
mod(floor(28343 / 254^0) ; 254) = 149

0 -> 1 = 0x01
111 -> 113 = 0x71
149 -> 151 = 0x97

Send over the air: ``01:71:97``

Now we concatenate these together and get our final message to send over the air: ``02:02:c1:6c:bf:c9:0f:01:01:71:97``.


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

Ping (OBC)
^^^^^^^^^^

Ping OBC to see if it responds. Should be used to check OBC responds to transceiver messages.

- Message type - 0x00

Get Subsystem Status (OBC)
^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the restart count (number of times OBC has restarted its program), restart date/time (RTC date/time of most recent restart), and uptime (time since most recent restart).

- Message type - 0x01
- Argument 1 - Subsystem
- Data - 15 bytes - restart count (4 bytes), restart date (3 bytes), restart time (3 bytes), restart reason (1 byte), uptime (4 bytes)

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

Read Memory Bytes
^^^^^^^^^^^^^^^^^

The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes. The maximum number of bytes that can be read in one command is 106 bytes (to match the biggest block type of PAY_OPT, 10 byte header + 32 fields * 3 bytes, don't want to make the message buffers on OBC any longer).

- Message type - 0x04
- Argument 1 - starting address (in bytes)
- Argument 2 - count (number of bytes)
- Data - `count` bytes - read data

Erase Memory Physical Sector
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

Sets the automatic data collection period for one type of data. Must have ``period >= 60`` or else the state of OBC will not change. This is to prevent data collection from triggering too frequently and constantly filling up the command/CAN queues.

- Message type - 0x0A
- Argument 1 - block type
- Argument 2 - period (in seconds)

Automatic Data Collection - Resync
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Resynchronizes timers for data collection for all types of data so they start counting at the same time (reset all to 0, counting up).

- Message type - 0x0B

PAY Control - Actuate Motors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Actuates the motors in the payload.

This gets its own command (instead of the generic CAN commands) so it can first send them CAN messages to activate temporary low-power mode.

- Message type - 0x0E
- Argument 1 - 1 (move plate up) or 2 (move plate down)

Reset Subsystem
^^^^^^^^^^^^^^^

Resets the microcontroller for the specified subsytem (intentionally runs out the watchdog timer to make it restart its program).

This gets its own command (instead of the generic CAN commands) because EPS and PAY will not respond so it doesn't wait for them.

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

Read EEPROM (OBC)
^^^^^^^^^^^^^^^^^

Reads 4 bytes (a `dword` i.e. double word) from EEPROM memory.

- Message type - 0x12
- Argument 1 - 32-bit address
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

Erase EEPROM (OBC)
^^^^^^^^^^^^^^^^^^

Erases 4 bytes (a `dword` i.e. double word) in EEPROM memory (sets to all 1's, i.e. 0xFFFFFFFF).

- Message type - 0x17
- Argument 1 - 32-bit address (in bytes)

NOTE: Be careful using this, because for example it could force OBC to re-run its initial 30-minute comms delay and try to deploy the antenna again.

Erase All Memory
^^^^^^^^^^^^^^^^

The satellite erases all flash memory on all 3 chips (sets every byte to 0xFF, i.e. all 1's). This would generally be used when changing the satellite's current block number, allowing it to rewrite to addresses that were previously written to.

BE VERY CAREFUL WITH THIS!!

- Message type - 0x19

Erase Memory Physical Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^

NOTE: The use of the term "block" here is different from all other uses in general.

Deletes the block in memory containing the specified address. The block size can range from 8kb to 64kb - see pg. 5 of data sheet for memory map and pg. 25 for more details on block erase

- Message type - 0x1A
- Argument 1 - address (in bytes)


General Descriptions for CAN Commands
-------------------------------------

Ping - EPS/PAY respond to CAN messages from OBC

Set EPS Heater DAC Setpoints - The satellite changes the DAC setpoints that control the EPS heaters for the batteries.

Set PAY Heater DAC Setpoints - The satellite changes the DAC setpoints that control the PAY heaters for the cells.

Set EPS Heater Mode Current Threshold - Sets the threshold of total (summed) solar panel current for which to switch the mode of shadow/sun for heater setpoints.


Ideas for Future Commands
-------------------------

CAN messages
^^^^^^^^^^^^

Maybe have distinct commands for generic CAN message (less data bytes, just send message type/field number or data) and raw CAN message (full 8 bytes)

Low-power mode
^^^^^^^^^^^^^^

Puts the entire satellite in low-power mode.

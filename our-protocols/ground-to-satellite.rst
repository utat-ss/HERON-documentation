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

- Byte 0 - Opcode
- Bytes 1-4 (32-bit int) - Argument 1
- Bytes 5-8 (32-bit int) - Argument 2
- Bytes 9-12 (32-bit int) - Password

The argument 1, argument 2, and password are sometimes not used, but are always sent in packets. The password is only checked for sensitive commands.

TODO - revisit variable-length or omission of arguments

Satellite to ground acknowledgement (ACK or NACK):

- Byte 0 - Opcode
- Bytes 1-4 (32-bit int) - Argument 1
- Bytes 5-8 (32-bit int) - Argument 2
- Bytes 9 - Status - 0 = OK (valid command and (valid password or don't care)), 1 = Invalid Length, 2 = Invalid Decoded Format, 3 = Invalid Opcode, 4 = Invalid Password

The acknowledgement should be sent almost immediately after the satellite receives the request, even if it is currently in the process of executing another command. There is a delay between the acknowledgement and response to execute the command (depends on command type, e.g. collect block takes the longest by far).

Satellite to ground response:

- Byte 0 - Opcode
- Bytes 1-4 (32-bit int) - Argument 1
- Bytes 5-8 (32-bit int) - Argument 2
- Bytes 9-... - Data (length depends on opcode)

Message Encoding
----------------

The **encoded message** is the full message (called "message" in the packetization protocol), i.e. what is actually sent through the transceiver and over the air.

- Byte 0 - 0x00 (special character to indicate start of message)
- Byte 1 - number of bytes in decoded message with base-254 algorithm applied + 16 (i.e. +0x10, to avoid ever sending the byte 0x0D, should never overflow 255) (starting at byte 3 but not including byte n-1, i.e. subtract 4 from total number of characters) - number<=16 is invalid
- Byte 2 - 0x00 (special character to separate length from message contents)
- Byte 3-... - Decoded message with base-254 algorithm applied (see below)
- Byte (n-1) - 0x00 (special character to indicate end of message)

Even though the transceiver only accepts packets with a valid checksum, we add the start and count bytes in case characters are dropped over UART between the OBC and transceiver.

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

Consider we have a sequence of an arbitrary number of decoded bytes (which we will call base-256, each can take on a value between 0 and 255) that we want to send. We can’t send it in this format since the value 13 is not allowed. Now consider we split this sequence into 7-byte sections, and have a final section for the leftover bytes (i.e. ``n % 7`` bytes).

We will start with how to encode each 7-byte section and discuss the remainder section later. We encode each 7-byte base-256 section as an 8-byte base-254 section, where each byte can take one of 254 possible values, i.e. decoded 0-11 are represented as encoded 1-12, but decoded 12-253 are represented as encoded 14-255. Then we have a sequence of 8 base-254 digits which are treated as a single unsigned integer in big-endian format. Using standard rules for numbers with different bases, you can take 8 bytes in base-254 (say we have an array ``uint8_t seq[8]``) and convert it to an integer as follows: ``seq[0] * (254^7) + seq[1] * (254^6) + ... + seq[7]``.

Note that the most significant byte in base-254 will usually be 0, and sometimes 1.

Why choose base-254? This is because we have two values (0 and 13) out of 256 that we want to avoid.

Why choose to group bytes such that decoded 7-byte base-256 -> encoded 8-byte base-254? This is because the biggest integer we can represent in software and do math on is 64 bits, i.e. ``uint64_t`` in C.

Why not use 8-byte -> 9-byte? We could do this, but it would assume that the ground station always sends the most significant byte in base-254 as either 0 or 1. Just in case it accidentally sends a higher number, we don't want to overflow a ``uint64_t`` in the satellite's software.

For the remaining bytes after creating 7-byte groups (e.g. say ``n = num_bytes % 7``), we apply the same algorithm but encode the decoded ``n`` bytes -> encoded ``n+1`` bytes to ensure we can always represent all possible values of the bytes.

We should have a ratio of encoded bytes to decoded bytes of around 8:7.

If the decoded message has ``N`` bytes, the encoded message length is as follows:

- If ``num_bytes % 7 == 0`` -> ``(N / 7) * 8``
- If ``num_bytes % 7 != 0`` -> ``(floor(N / 7) * 8) + ((N % 7) + 1)``

Example
+++++++

Say we have a 9-byte decoded message, in hex ``f3:ff:34:9e:1e:28:9a:6e:b7``. We split up the message into a single 7-byte chunk and a leftover 2-byte chunk, and apply this procedure.

Note that ``^`` represents a power operation, not XOR.

Take the 7-byte chunk: ``f3:ff:34:9e:1e:28:9a``. We treat this as an unsigned 64-bit integer in big-endian format. Now calculate that 64-bit integer: ``0xF3*256^6 + 0xFF*256^5 + 0x34*256^4 +0x9E*256^3 + 0x1E*256^2 + 0x28*256^1 + 0x9A*256^0 = 68679020796848282``.

Now let's take that integer and represent as 8 values in base-254:

(note ``mod(a ; b) = a % b``)

``mod(floor(68679020796848282 / 254^7) ; 254) = 1``
``mod(floor(68679020796848282 / 254^6) ; 254) = 1``
``mod(floor(68679020796848282 / 254^5) ; 254) = 191``
``mod(floor(68679020796848282 / 254^4) ; 254) = 106``
``mod(floor(68679020796848282 / 254^3) ; 254) = 189``
``mod(floor(68679020796848282 / 254^2) ; 254) = 199``
``mod(floor(68679020796848282 / 254^1) ; 254) = 13``
``mod(floor(68679020796848282 / 254^0) ; 254) = 0``

Now to escape (not send through the transceiver) the bytes 0 and 13, we apply the mapping of 0-11 -> 1-12 and 12-253 -> 14-255. Now we get:

``1 -> 2 = 0x02``

``1 -> 2 = 0x02``

``191 -> 193 = 0xC1``

``106 -> 108 = 0x6C``

``189 -> 191 = 0xBF``

``199 -> 201 = 0xC9``

``13 -> 15 = 0x0F``

``0 -> 1 = 0x01``

Now we have the actual 8 bytes we send over the air (in hex): ``02:02:c1:6c:bf:c9:0f:01``

Apply the same procedure to the remaining 2 byte chunk, ``6e:b7``, changing 2-byte base-256 to 3-byte base-254.

``0x6E*256^1 + 0xB7*256^0 = 28343``

``mod(floor(28343 / 254^2) ; 254) = 0``

``mod(floor(28343 / 254^1) ; 254) = 111``

``mod(floor(28343 / 254^0) ; 254) = 149``

``0 -> 1 = 0x01``

``111 -> 113 = 0x71``

``149 -> 151 = 0x97``

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

TODO - update

This is used as an argument in some commands to identify a type of data.

- 0 - EPS HK
- 1 - PAY HK
- 2 - PAY OPT
- 3 - OBC HK (TODO make first)

Block Size
^^^^^^^^^^

TODO - update

The number of bytes to store a block of a particular type of data, including both the header and data.

size = 10 bytes (header) + (3 bytes * number of fields)

- EPS HK - 91 bytes
- PAY HK - 70 bytes
- PAY OPT - 106 bytes
- OBC HK - 25 bytes

Commands - Summary
------------------

.. list-table::
    :header-rows: 1
    :stub-columns: 1

    * - Name
      - Password Protected
      - Opcode
      - Argument 1
      - Argument 2
      - Data
    * - Ping (OBC)
      - No
      - 0x00
      - N/A
      - N/A
      - N/A
    * - Get RTC Date/Time
      - No
      - 0x02
      - N/A
      - N/A
      - 6 bytes - date YY, date MM, date DD, time HH, time MM, time SS
    * - Set RTC Date/Time
      - Yes
      - 0x03
      - date (8 bits YY, 8 bits MM, 8 bits DD)
      - time (8 bits HH, 8 bits MM, 8 bits SS)
      - N/A
    * - Read Memory Bytes
      - Yes
      - 0x04
      - Starting address (in bytes)
      - Count (number of bytes)
      - ``count`` bytes - read data
    * - Erase Memory Physical Sector
      - Yes
      - 0x05
      - Address (in bytes)
      - N/A
      - N/A
    * - Collect Block
      - No
      - 0x06
      - block type
      - automatic (1 for auto)
      - 3 bytes - block number (only sends a downlink packet if auto)
    * - Read Recent Local Block
      - No
      - 0x07
      - block type
      - N/A
      - Block size for argument 1
    * - Read Data Block
      - No
      - 0x08
      - block type
      - block number
      - Block size for argument 1
    * - Automatic Data Collection - Enable/Disable
      - Yes
      - 0x09
      - block type
      - 0 (disable) or 1 (enable)
      - N/A
    * - Automatic Data Collection - Period
      - Yes
      - 0x0A
      - block type
      - period (in seconds)
      - N/A
    * - Automatic Data Collection - Resync
      - Yes
      - 0x0B
      - N/A
      - N/A
      - N/A
    * - PAY Control - Actuate Motors
      - Yes
      - 0x0E
      - 1 (move plate up) or 2 (move plate down)
      - N/A
      - N/A
    * - Reset Subsystem
      - Yes
      - 0x0F
      - subsystem
      - N/A
      - N/A
    * - Send CAN Message - EPS
      - Yes
      - 0x10
      - first 4 bytes of message to send
      - last 4 bytes of message to send
      - (8 bytes) - response from EPS
    * - Send CAN Message - PAY
      - Yes
      - 0x11
      - first 4 bytes of message to send
      - last 4 bytes of message to send
      - (8 bytes) - response from PAY
    * - Read EEPROM (OBC)
      - Yes
      - 0x12
      - 32-bit address
      - N/A
      - (4 bytes) - read data
    * - Get Current Block Number
      - No
      - 0x13
      - block type
      - N/A
      - (4 bytes) - block number
    * - Set Current Block Number
      - Yes
      - 0x14
      - block type
      - block number
      - N/A
    * - Set Memory Section Start Address
      - Yes
      - 0x15
      - block type
      - start address
      - N/A
    * - Set Memory Section End Address
      - Yes
      - 0x16
      - block type
      - end address
      - N/A
    * - Erase EEPROM (OBC)
      - Yes
      - 0x17
      - 32-bit address (in bytes)
      - N/A
      - N/A
    * - Erase All Memory
      - Yes
      - 0x19
      - N/A
      - N/A
      - N/A
    * - Erase Memory Physical Block
      - Yes
      - 0x1A
      - address (in bytes)
      - N/A
      - N/A
    * - Enable/Disable Indefinite Low-Power Mode
      - Yes
      - 0x1B
      - 0 to disable, 1 to enable
      - N/A
      - N/A
    * - Get Most Recent Status Info
      - No
      - 0x1C
      - N/A
      - N/A
      - 27 bytes - OBC uptime (3 bytes), OBC restart count (3 bytes), OBC restart reason (1 byte), OBC restart date (3 bytes), OBC restart time (3 bytes), EPS uptime (3 bytes), EPS restart count (3 bytes), EPS restart reason (1 byte), PAY uptime (3 bytes), PAY restart count (3 bytes), PAY restart reason (1 byte)
    * - Read Primary Command Blocks
      - No
      - 0x1D
      - starting block number
      - number of blocks (count, must be <= 5 or else nothing will be read and 0 bytes of data will be given back)
      - (19 * `count`) bytes - block size for `count` number of command blocks (19 bytes each)
    * - Read Secondary Command Blocks
      - No
      - 0x1E
      - starting block number
      - number of blocks (count, must be <= 5 or else nothing will be read and 0 bytes of data will be given back)
      - (19 * `count`) bytes - block size for `count` number of command blocks (19 bytes each)
    * - Read RAM Byte (OBC)
      - Yes
      - 0x1F
      - Address (in bytes)
      - N/A
      - 1 byte - value


Commands - Descriptions
-----------------------

Ping (OBC)
^^^^^^^^^^

Ping OBC to see if it responds. Should be used to check OBC responds to transceiver messages.

Get RTC Date/Time
^^^^^^^^^^^^^^^^^

TODO

Set RTC Date/Time
^^^^^^^^^^^^^^^^^

TODO

Read Memory Bytes
^^^^^^^^^^^^^^^^^

The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes. The maximum number of bytes that can be read in one command is 106 bytes (to match the biggest block type of PAY_OPT, 10 byte header + 32 fields * 3 bytes, don't want to make the message buffers on OBC any longer).

Erase Memory Physical Sector
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ideally argument 1 (address in bytes) should be specified as aligned to a 4 kB boundary, but it will work nonetheless.

The satellite erases one sector (4 kB) of the flash memory (sets every byte to 0xFF, i.e. all 1's). This will happen for the 4 kB sector that includes the specified address, aligned to a 4 kB boundary.

Collect Block
^^^^^^^^^^^^^

Note the "automatic" argument indicates whether the command was sent manually from the ground station (0) or was scheduled by automatic data collection (1). The ground station should only send this as 0. A value of 1 will cause OBC to not send a transceiver packet response through the downlink. This is to save power for a frequent operation and to prevent OBC from spamming the ground station with packets when the ground station did not request anything.

Triggers data collection of a block and writes it to flash memory on OBC. Note that this does not send any data back to ground - see "read block" command.

Read Recent Local Block
^^^^^^^^^^^^^^^^^^^^^^^

Reads the block of data stored locally in the microcontroller's program memory. This should be the most recent block it has collected, if OBC has not restarted since it collected it.

Generally, this should not be used. It can be used for debugging and very infrequent data collection in case flash memory storage fails.

Read Data Block
^^^^^^^^^^^^^^^

The satellite sends back the specified block of data stored in flash memory.

Automatic Data Collection - Enable/Disable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Turns off or on automatic data collection for one type of data.

Automatic Data Collection - Period
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the automatic data collection period for one type of data. Must have ``period >= 60`` or else the state of OBC will not change. This is to prevent data collection from triggering too frequently and constantly filling up the command/CAN queues.

Automatic Data Collection - Resync
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Resynchronizes timers for data collection for all types of data so they start counting at the same time (reset all to 0, counting up).

PAY Control - Actuate Motors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Actuates the motors in the payload.

This gets its own command (instead of the generic CAN commands) so it can first send them CAN messages to activate temporary low-power mode.

Reset Subsystem
^^^^^^^^^^^^^^^

Resets the microcontroller for the specified subsytem (intentionally runs out the watchdog timer to make it restart its program).

This gets its own command (instead of the generic CAN commands) because EPS and PAY will not respond so it doesn't wait for them.

If resetting OBC, no response message back to ground station.

It is recommended that the ground station team sends a follow-up message to check the uptime/restart time of the subsystem that should have been reset.

Send CAN Message - EPS
^^^^^^^^^^^^^^^^^^^^^^

OBC sends a CAN message (8 bytes) to EPS and gets a response (8 bytes) back.

Ideas for use cases:

- Request a single field of EPS_HK data (in case the block collection of all measurements at once fails).

Send CAN Message - PAY
^^^^^^^^^^^^^^^^^^^^^^

OBC sends a CAN message (8 bytes) to PAY and gets a response (8 bytes) back.

Read EEPROM (OBC)
^^^^^^^^^^^^^^^^^

Reads 4 bytes (a `dword` i.e. double word) from EEPROM memory.

Get Current Block Number
^^^^^^^^^^^^^^^^^^^^^^^^

Gets the current block number for the specified block type. The block number represents the index of the block that will be written to memory the next time collection is triggered for that section, i.e. if the current block number is x, blocks 0 to (n-1) have already been collected and written to memory but block x has not.

Set Current Block Number
^^^^^^^^^^^^^^^^^^^^^^^^

Sets the current block number for the specified block type. The block number represents the index of the block that will be written to memory the next time collection is triggered for that section, i.e. if the current block number is x, blocks 0 to (n-1) have already been collected and written to memory but block x has not. This could be used to skip sections of flash memory that are found to be malfunctioning, to reset the block number to 0 when a section reaches the end of its memory and all existing data has already been safely downlinked, or ran when the start address of a section has been changed.

Set Memory Section Start Address
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the starting address of a section in OBC flash memory. This could be used if one of the memory chips is found to be malfunctioning in orbit, allowing us to remap the memory sections from ground. Note that changing this will blindly overwrite any data previously in that part of memory.

NOTE: This should be run consecutively with the "Set Memory Section End Address" command.

Set Memory Section End Address
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the end address of a section in OBC flash memory. See above for motivation.

NOTE: This should be run consecutively with the "Set Memory Section Start Address" command.

Erase EEPROM (OBC)
^^^^^^^^^^^^^^^^^^

Erases 4 bytes (a `dword` i.e. double word) in EEPROM memory (sets to all 1's, i.e. 0xFFFFFFFF).

NOTE: Be careful using this, because for example it could force OBC to re-run its initial 30-minute comms delay and try to deploy the antenna again.

Erase All Memory
^^^^^^^^^^^^^^^^

The satellite erases all flash memory on all 3 chips (sets every byte to 0xFF, i.e. all 1's). This would generally be used when changing the satellite's current block number, allowing it to rewrite to addresses that were previously written to.

BE VERY CAREFUL WITH THIS!!

Erase Memory Physical Block
^^^^^^^^^^^^^^^^^^^^^^^^^^^

NOTE: The use of the term "block" here is different from all other uses in general.

Deletes the block in memory containing the specified address. The block size can range from 8kb to 64kb - see pg. 5 of data sheet for memory map and pg. 25 for more details on block erase

Enable/Disable Indefinite Low-Power Mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

TODO - figure out what this should do

Get Most Recent Status Info
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the most recently saved (in flash memory) status information for each subsystem. This is done by subtracting one from each section's current block number and reading that block in memory (does not actually modify the current block number for any sections).

This is intended to be used at the beginning of the pass to detect any restarts or critical status information that should all be obtained in one command.

Read Primary Command Blocks
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block(s) of primary command data stored in flash memory.

Read Secondary Command Blocks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The satellite sends back the specified block(s) of secondary command data stored in flash memory.

Read RAM Byte (OBC)
^^^^^^^^^^^^^^^^^^^

Reads a byte from the "data memory" (i.e. RAM) in the OBC microcontroller (see http://download.mikroe.com/documents/compilers/mikroc/avr/help/avr_memory_organization.htm). This is intended to read register values in the MCU for debugging purposes.

TODO - could this be dangerous if reading from an unintended location?

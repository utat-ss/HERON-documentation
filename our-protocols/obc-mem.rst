OBC Flash Memory Protocol
=========================

This protocol describes how data is stored and organized on the 3 flash memory chips connected to the OBC microcontroller.

The 3 chips are treated as a single device with a continuous address space (3x the size of one chip's
addresses). Each flash chip is 16Mbit (2MB or 2 megabytes) in size, so in total we have 6MB of flash memory (0x600000 bytes). Each virtual address is 23 bits (2 bits to select the chip, 21 bits for the address on that chip)

The memory is divided into **sections**, where one **section** stores one category of data (EPS housekeeping, PAY
housekeeping, or PAY optical). The fourth section stores a command log of all commands executed on the satellite.

Each section contains some number of **blocks**. Each **block** contains a header followed by a set of **measurements** taken around the same time. The **block number** can be up to 24 bits.

Each **block** starts with a 10-byte **header** to identify and timestamp the block with data from the RTC (real-time clock). The RTC date and time are for when those measurements were taken (there is some delay between measurements but usually on the order of a few seconds).

.. list-table::
    :header-rows: 1

    * - Byte(s)
      - Description
    * - 0-2
      - Block number (big-endian)
    * - 3
      - RTC date (YY)
    * - 4
      - RTC date (MM)
    * - 5
      - RTC date (DD)
    * - 6
      - RTC time (HH)
    * - 7
      - RTC time (MM)
    * - 8
      - RTC time (SS)
    * - 9
      - Success Count (0xFF = fail, otherwise success)

The success count byte will not be written when a command is started, so its default value of 0xFF means a fail. After a command it is executed and succeesed, the byte will be written to 1.

The collect block command uses the success count to count the number of fields successfully received back over CAN from the intended subsystem. If it is equal to the number of fields of that data type, the command was successful.

The **header** is followed by some number of **fields**. For the 3 data sections, each field is 3 bytes (24 bits) for one measurement.
The number of fields varies between sections but is constant within a section.

For the command section, the header is followed by a special set of data (10 bytes), with values as defined in the ground station to satellite protocol:

.. list-table::
    :header-rows: 1

    * - Byte(s)
      - Description
    * - 0
      - Command number
    * - 1-4
      - Argument 1
    * - 5-8
      - Argument 2

**EEPROM** (Electronically Erasable Programmable Read Only Memory) is a non-volatile memory in the microcontroller,
meaning the data persists even if the microcontroller is turned off or reset. We use it to keep track of
the current block number being written to for each section of memory. In case of the microcontroller resetting, it can recover which blocks it should write to in memory.

The **sections** are defined as follows:

.. list-table::
    :header-rows: 1

    * - Name
      - Start Address
      - End Address
      - Number of fields per block
      - Number of bytes per block (3 * fields/block + 10)
      - Description
    * - OBC Housekeeping
      - 0x000000
      - 0x0FFFFF
      - 36
      - 116
      - Payload experiment data - optical measurements
    * - EPS Housekeeping
      - 0x100000
      - 0x1FFFFF
      - 12
      - 44
      - State of power system - battery, solar panels, battery temperature
    * - PAY Housekeeping
      - 0x200000
      - 0x2FFFFF
      - 3
      - 17
      - State of payload environment - temperature, pressure, humidity
    * - PAY Optical
      - 0x300000
      - 0x3FFFFF
      - 36
      - 116
      - Payload experiment data - optical measurements
    * - Primary Command Log
      - 0x400000
      - 0x4FFFFF
      - N/A
      - 19
      - Log of all commands executed by OBC, except for Read Data Block, Read Primary Command Blocks, and Read Secondary Command Blocks
    * - Second Command Log
      - 0x500000
      - 0x5FFFFF
      - N/A
      - 19
      - Log of all commands executed by OBC, only for Read Data Block, Read Primary Command Blocks, and Read Secondary Command Blocks

If auto data collection is enabled and the current block number of OBC reaches the end of the allocated memory for that section, the current block number will automatically be set back to 0 by the OBC on its own by enqueuing and executing a "Set Block Number" command.

TODO - different number of bytes/measurement for each section?

TODO - separate sections for uptime/restart count/restart reason?

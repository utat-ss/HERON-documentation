EEPROM Map
==========

This document maps out the memory locations of EEPROM values across all subsystems. Use the same mapping scheme across all subsystems for simplicity, even though not all subsystems use all values/types of data.

Try to start each section/block of related values on an address that is a multiple of 16 (0x10), to make it easier to see the sections of related values when dumping EEPROM contents to a file.

Use 4-byte values (dwords, i.e. double words) for all values - for simplicity and we have plenty of EEPROM space. Note all values are stored as little-endian in EEPROM and in the hexdump.

There is 2kB of EEPROM in each 64M1 MCU.

Applicable to all subsystems:

.. list-table::
    :header-rows: 1

    * - Address (bytes)
      - Section
      - Value
    * - 0x00
      - Restart Info
      - Restart count
      - All
    * - 0x04
      - Restart Info
      - Restart reason
      - All

OBC only:

.. list-table::
    :header-rows: 1

    * - Address (bytes)
      - Section
      - Value
    * - 0x100
      - Restart Info
      - Restart date
    * - 0x104
      - Restart Info
      - Restart time
    * - 0x120
      - Comms
      - Comms delay done (30 minutes)
    * - 0x140
      - Memory Blocks
      - OBC_HK current block number
    * - 0x144
      - Memory Blocks
      - OBC_HK auto data collection enable (0 = disabled, 1 = enabled)
    * - 0x148
      - Memory Blocks
      - OBC_HK auto data collection period (in seconds)
    * - 0x150
      - Memory Blocks
      - EPS_HK current block number
    * - 0x154
      - Memory Blocks
      - EPS_HK auto data collection enable (0 = disabled, 1 = enabled)
    * - 0x158
      - Memory Blocks
      - EPS_HK auto data collection period (in seconds)
    * - 0x160
      - Memory Blocks
      - PAY_HK current block number
    * - 0x164
      - Memory Blocks
      - PAY_HK auto data collection enable (0 = disabled, 1 = enabled)
    * - 0x168
      - Memory Blocks
      - PAY_HK auto data collection period (in seconds)
    * - 0x170
      - Memory Blocks
      - PAY_OPT current block number
    * - 0x174
      - Memory Blocks
      - PAY_OPT auto data collection enable (0 = disabled, 1 = enabled)
    * - 0x178
      - Memory Blocks
      - PAY_OPT auto data collection period (in seconds)
    * - 0x180
      - Memory Blocks
      - PRIM_CMD_LOG current block number
    * - 0x190
      - Memory Blocks
      - SEC_CMD_LOG current block number

EPS only:

.. list-table::
    :header-rows: 1

    * - Address (bytes)
      - Section
      - Value
    * - 0x200
      - Heaters
      - Heater 1 shadow setpoint
    * - 0x204
      - Heaters
      - Heater 2 shadow setpoint
    * - 0x208
      - Heaters
      - Heater 1 sun setpoint
    * - 0x20C
      - Heaters
      - Heater 2 sun setpoint
    * - 0x210
      - Heaters
      - Shadow current threshold
    * - 0x214
      - Heaters
      - Sun current threshold

PAY only:

TODO

EEPROM Map
==========

This document maps out the memory locations of EEPROM values across all subsystems. Use the same mapping scheme across all subsystems for simplicity, even though not all subsystems use all values/types of data.

Try to start each section/block of related values on an address that is a multiple of 16 (0x10), to make it easier to see the sections of related values when dumping EEPROM contents to a file.

Use 4-byte values (dwords, i.e. double words) for all values - for simplicity and we have plenty of EEPROM space. Note all values are stored as little-endian in EEPROM and in the hexdump.


.. list-table::
    :header-rows: 1

    * - Address (bytes)
      - Section
      - Value
      - Subsystem(s)
    * - 0x00
      - Uptime
      - Restart count
      - All
    * - 0x04
      - Uptime
      - Restart reason
      - All
    * - 0x08
      - Uptime
      - Restart date
      - OBC
    * - 0x0C
      - Uptime
      - Restart time
      - OBC
    * - 0x20
      - Comms
      - Comms delay done
      - OBC
    * - 0x40
      - Data Collection
      - EPS_HK current block number
      - OBC
    * - 0x44
      - Data Collection
      - EPS_HK auto data collection enable (0 = disabled, 1 = enabled)
      - OBC
    * - 0x48
      - Data Collection
      - EPS_HK auto data collection period (in seconds)
      - OBC
    * - 0x50
      - Data Collection
      - PAY_HK current block number
      - OBC
    * - 0x54
      - Data Collection
      - PAY_HK auto data collection enable (0 = disabled, 1 = enabled)
      - OBC
    * - 0x58
      - Data Collection
      - PAY_HK auto data collection period (in seconds)
      - OBC
    * - 0x60
      - Data Collection
      - PAY_OPT current block number
      - OBC
    * - 0x64
      - Data Collection
      - PAY_OPT auto data collection enable (0 = disabled, 1 = enabled)
      - OBC
    * - 0x68
      - Data Collection
      - PAY_OPT auto data collection period (in seconds)
      - OBC
    * - 0x70
      - Data Collection
      - OBC_HK current block number
      - OBC
    * - 0x74
      - Data Collection
      - OBC_HK auto data collection enable (0 = disabled, 1 = enabled)
      - OBC
    * - 0x78
      - Data Collection
      - OBC_HK auto data collection period (in seconds)
      - OBC
    * - 0x80
      - Memory
      - Command log current block number
      - OBC
    * - 0x100
      - EPS Heaters
      - Heater 1 shadow setpoint
      - EPS
    * - 0x104
      - EPS Heaters
      - Heater 2 shadow setpoint
      - EPS
    * - 0x108
      - EPS Heaters
      - Heater 1 sun setpoint
      - EPS
    * - 0x10C
      - EPS Heaters
      - Heater 2 sun setpoint
      - EPS
    * - 0x200
      - PAY Heaters
      - Heaters 1-4 setpoint
      - PAY
    * - 0x204
      - PAY Heaters
      - Heater 5 setpoint
      - PAY

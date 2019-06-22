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
    * - 0x04
      - Uptime
      - Restart date
      - OBC
    * - 0x08
      - Uptime
      - Restart time
      - OBC
    * - 0x0C
      - Uptime
      - Restart reason
    * - 0x20
      - Comms
      - Comms delay done
    * - 0x40
      - Memory
      - EPS_HK current block number
    * - 0x44
      - Memory
      - PAY_HK current block number
    * - 0x48
      - Memory
      - PAY_OPT current block number
    * - 0x4C
      - Memory
      - Command log current block number
    * - 0x60
      - EPS Heaters
      - Heater 1 shadow setpoint
    * - 0x64
      - EPS Heaters
      - Heater 2 shadow setpoint
    * - 0x68
      - EPS Heaters
      - Heater 1 sun setpoint
    * - 0x6C
      - EPS Heaters
      - Heater 2 sun setpoint
    * - 0x80
      - PAY Heaters
      - Heaters 1-4 setpoint
    * - 0x84
      - PAY Heaters
      - Heater 5 setpoint

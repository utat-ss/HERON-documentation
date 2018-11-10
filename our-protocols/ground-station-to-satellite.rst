Ground Station to Satellite
===========================

The current protocol between the ground station and satellite, to be created one step at a time.

This integrates heavily with the memory protocol (in a separate document).

The ground station and transceiver both use the AX25 protocol to send messages.

TODO - need to figure out if we need to supply any header/status information for the AX25 protocol itself, but this document currently only covers the content of the messages itself.

Each message contains the following:

- Byte 0 - Message type
- Byte 1 - Block number (if applicable)
    - If this is for a data downlink, the ground station specified which block number within the section of memory to fetch.
- Bytes 2-end - Data (if applicable)
    - The data block contains some number of **fields**, where each field is 3 bytes (24 bits of data).

.. list-table:: Message types and data
    :header-rows: 1

    * - Message type
      - Number of fields
      - Name
      - Description
    * - 0x00
      - 4
      - Status/ping
      - If the satellite receives this command, it responds with data "UTAT"
    * - 0x01
      - 12
      - EPS Housekeeping
      - The satellite sends back the specified block of EPS housekeeping data
    * - 0x02
      - 3
      - PAY Housekeeping
      - The satellite sends back the specified block of PAY housekeeping data
    * - 0x03
      - 3
      - PAY Optical
      - The satellite sends back the specified block of PAY optical data
    * - 0x04
      - 2
      - PAY Actuation Distances
      - The satellite sends back the specified block of PAY actuation plate distance data
    * - 0x05
      - 1
      - PAY Experiment
      - The satellite starts the experiment:
        - Actuates the motors to pop the blister packs
        - Starts a timer which will go off every 30 minutes, collecting all data in the satellite and storing it in memory
    * - 0x06
      - unknown
      - Read Memory
      - The satellite reads and sends back the contents of the flash memory starting at the specified address and reading the specified number of bytes
    * - 0x07
      - unknown
      - Write Memory
      - The satellite writes the specified data to flash memory starting at the specified address and with the specified number of bytes

Optical-SPI
===========

Payload (PAY) SPI Protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^
Optical SPI - This microcontroller (PAY-OPTICAL) functions as a SPI slave. The PAY microcontroller functions as the SPI master.

When PAY sends a request to PAY-OPTICAL to request reading of a sensor, PAY will send a byte over SPI. It will start with two 1's ("0B11xxxxx") to signalify to PAY-OPTICAL that it is requesting an instrumentation reading: optical density or fluorescence.

+-----+-----+-----------+-----+-----+-----+-----+
| Byte                                          |
+=====+=====+===========+=====+=====+=====+=====+
|  7  |  6  |     5     |  4  |  3  |  2  |  1  |
+-----+-----+-----------+-----+-----+-----+-----+
|  1  |  1  | OD/FLUOR  | bank      | channel   |
+-----+-----+-----------+-----+-----+-----+-----+

* OD (optical density) = 1
* FLUOR (Fluorescence) = 0
* bank = 0,1,2,3
* channel = 0,1,2,3,4,5,6,7

This is followed by 3 transactions for the 3 bytes of data. There is a total of 4 SPI communications that happen in an exchange:

  1. SSM requests data from a specific channel on optical
  2. OPTICAL transfers back the first byte of data
  3. OPTICAL transfers back the second byte of data
  4. OPTICAL transfers back the third byte of data

The interrupt on OPTICAL gets triggered on each of these. OPTICAL sets the DATA_RDY (data ready) pin high between transfers 1 and 2 to tell SSM it has the data ready (synchronization between OPTICAL and SSM).
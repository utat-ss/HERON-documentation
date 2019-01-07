Data Conversion Protocol
========================

This protocol describes all the data used in the satellite, including all the different types. It describes their raw data formats and how to convert them to the actual real-world values.

When sending data around the satellite or between the satellite and ground station, we can't send floating-point representations. Therefore, we send the "raw data" as an integer value, which can be converted to the real-world value by the receiver if desired.

These data conversions are implemented in code in the ``lib-common/conversions`` library.

ADC (Analog to Digital Converter, ADS7952)
------------------------------------------

Datasheet: http://www.ti.com/lit/ds/slas605c/slas605c.pdf

- Raw data is 12 bits
- Voltage reference (VREF) is 2.5 V.
- Raw voltage is the voltage on the input pin to the ADC
- Formula based on p. 38, 12-bit

raw voltage [V] = (raw data / 0xFFF) * 2.5

EPS Current
^^^^^^^^^^^

INA214 datasheet: http://www.ti.com/lit/ds/sbos437j/sbos437j.pdf

TODO - diagram

- Raw voltage is on ADC pin
- Current is what we want to measure in the circuit
- Shunt resistor is 10 mohm (0.010 ohm)
- 100x gain on INA214
- voltage across resistor: V = IR

voltage across shunt resistor [V] = raw voltage [V] / 100

current [A] = voltage across shunt resistor [V] / 0.01

cancelling out: current [A] = raw voltage [V]

EPS Voltage
^^^^^^^^^^^

TODO - diagram

- Raw voltage is on ADC pin
- Voltage (actual voltage) is what we want to measure in the circuit
- Voltage divider has equal resistors, so ratio of 0.5

voltage = raw voltage * 0.5

Thermistors
^^^^^^^^^^^

Datasheet: https://www.murata.com/~/media/webrenewal/support/library/catalog/products/thermistor/r03e.ashx?la=en-us

TODO - add voltage divider diagram

- Raw voltage is measured in the middle of the voltage divider, on the input pin to the ADC
- Reference resistor is 10k
- Reference voltage is 2.5k
- Resistance to temperature conversion from p. 13, Part # NCU__XH103, 2nd column from left

Raw voltage to resistance: resistance = 10k * ((2.5 / raw voltage) - 1)

Resistance to temperature: based on datasheet table, knowing resistance, find the two resistances it is between, straight line approximation of slope between them, estimate temperature

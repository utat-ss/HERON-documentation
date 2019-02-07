# Patching avrdude for 64M1

To work with the 64M1 microcontroller, you need to modify the avrdude configuration manually because avrdude does not support it by default. This involves modifying the configuration file to add the settings it needs to use the 64M1.

Download the patched `avrdude.conf` file from [here](https://github.com/thomasonw/ATmegaxxM1-C1/blob/master/avr/avrdude.conf), which has the configuration settings for the 64M1.

Open the main `avrdude.conf` file on your computer. On macOS, this is located at `/usr/local/Cellar/avrdude/6.3_1/etc/avrdude.conf`. Open the patched `avrdude.conf` file, copy the ATmega64M1 section (at the end of the file, lines 1704-1887), and add it to the end of main `avrdude.conf` file on your computer, then save it. Now when you run avrdude with the 64M1, it should recognize it and be able to program it.

# AVR Fuses

The microcontroller contains fuses (fuse bits) that control settings such as the clock speed.

**We use the same fuse bits for all microcontrollers and you should never change them** (except when setting up the MCU on a new PCB for the first time).

You can get help with calculating fuse bit configurations and what they mean at http://www.engbedded.com/fusecalc/.


## Our Fuse Bit settings

The default fuse bits on the 64M1 from the factory are `62 6F FF` (in hex).

We always use the fuse bits `F9 D7 FF` (in hex).

Key Settings:
- BODLEVEL2 = 0, BODLEVEL1 = 0, BODLEVEL0 = 0 - brownout detection at 2.6 V - see datasheet p. 37, 62, 67-69, 366, 377-378, 399, 411 - want this for flash/EEPROM protection, although it consumes some power for the BOD circuit


## Setting Fuse Bits

If you need to set the fuse bits, `cd` to the `lib-common` directory and run:

`$ avrdude -U efuse:w:0xf9:m -U hfuse:w:0xd7:m -U lfuse:w:0xff:m -c stk500 -C avrdude.conf -p m64m1 -P <port>`

**You must replace** `<port>`  with the port the programmer is connected to (what you normally use to upload programs), such as `/dev/tty.usbmodem00208212` (on Mac, remember the programming port number normally ends in 2) or `COM3` (on Windows).

**NOTE: You must download the Pololu USB AVR Programmer v2 Configuration Utility application on your laptop. You must check the frequency of your programmer before running the fuse configuration command. We normally have ISP Frequency set to 1714kHz, the highest possible value that our programmer supports, so that it uploads programs faster. However, this will not work for a new microcontroller because it uses a much slower clock by default. Make sure to change the ISP Frequency to 114kHz (and click Apply Settings) before running the fuse command. After the fuses are configured, change the ISP Frequency back to 1714kHz (and click Apply Settings).**

The reason we use `-C avrdude.conf` is because the default `avrdude.conf` file in the avrdude installation does not support the 64M1, so we created a modified `avrdude.conf` file, put it in the `lib-common` repository, and tell avrdude to use that configuration file instead.

**If you are using the 32M1**, replace `m64m1` with `m32m1`.

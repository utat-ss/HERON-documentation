# AVR Fuses

The microcontroller contains fuses (fuse bits) that control settings such as the clock speed.

**We use the same fuse bits for all microcontrollers and you should never change them** (except when setting up the 32M1 on a new PCB for the first time).

You can get help with calculating fuse bit configurations and what they mean at http://www.engbedded.com/fusecalc/.


## Our Fuse Bit settings

The default fuse bits on the 64M1 from the factory are `FF 6F 62` (in hex).

We always use the fuse bits `FF D7 F8` (in hex).

Key Settings:
- BODLEVEL2 = 0, BODLEVEL1 = 0, BODLEVE0 = 0 - brownout detection at 2.6 V - see datasheet p. 399 - want this for flash/EEPROM protection, although it consumes some power for the BOD circuit


## Setting Fuse Bits

If you need to set the fuse bits, run:

`$ avrdude -U lfuse:w:0xff:m -U hfuse:w:0xd7:m -U efuse:w:0xff:m -c stk500 -p m32m1 -P <port>`

**You must replace** `<port>`  with the port the programmer is connected to (what you normally use to upload programs), such as `/dev/tty.usbmodem00208212` (Mac) or `COM3` (Windows).

**If you are using the 64M1**, replace `m32m1` with `m64m1`. If this command doesn't work (says AVR device not supported), see the instructions for patching `avrdude.conf` with the 64M1.

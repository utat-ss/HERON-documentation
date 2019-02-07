# AVR Fuses

The microcontroller contains fuses (fuse bits) that control settings such as the clock speed.

**We use the same fuse bits for all microcontrollers and you should never change them** (except when setting up the 32M1 on a new PCB for the first time).


## Setting Fuse Bits

The default fuse bits (on the 64M1) are `FF 6F 62` (in hex).

We always use the fuse bits `FF D7 FF` (in hex).

If you need to set the fuse bits, run:

`$ avrdude -U lfuse:w:0xff:m -U hfuse:w:0xd7:m -U efuse:w:0xff:m -c stk500 -p m32m1 -P <port>`

**You must replace** `<port>`  with the port the programmer is connected to (what you normally use to upload programs), such as `/dev/tty.usbmodem00208212` (Mac) or `COM3` (Windows).

**If you are using the 64M1**, replace `m32m1` with `m64m1`. If this command doesn't work (says AVR device not supported), see the instructions for patching `avrdude.conf` with the 64M1.

You can get help with calculating fuse bit configurations and what they mean at http://www.engbedded.com/fusecalc/.

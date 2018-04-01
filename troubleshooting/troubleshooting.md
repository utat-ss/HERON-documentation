# Troubleshooting

This document is to be used as a guide when programming to ensure all critical points for error are considered.

Below is a quick checklist for points to consider when programming.

- [ ] Lib-common, if included, is compiled.
- [ ] Added files compile.
- [ ] 'PORT' in makefile is set to the correct port. See below for details.
- [ ] All necessary lib-common modules are initialized.
- [ ] Build folder is included.
- [ ] CoolTerm / Xterm baud rate is set to 9600. Appropriate port is set. See below for details.

If the quick checklist has not cornered your error, let us first troubleshoot the software.

Software
- Read compiler warnings (bold this)
- Add a print statement after init_uart() to see if your program executes on the board

Hardware
- Change pcb
- Change wires
- Change programmer
- Change programmer micro-usb
- Check power supply, use different channel

# Real-Time Embedded Systems

An embedded system refers to a software system which is integrated into a larger
project, often alongside other mechanical or electrical systems. The
microcontroller which controls your coffee machine is an example of a embedded
system.

A real-time system is a software system that has real-time constraints; the
system must make certain guarantees regarding how quickly it responds to input.
An electric braking system is an example of a real-time system.

The Heron Mk II is a real-time embedded system: the software onboard must
integrate and interact with solar cells, external memory, fluid actuation
mechanisms, radio antenna, all within certain (small) time frames.

This introduces unique challenges. If something goes wrong, there is no one to
reset the system, and once the satellite is launched, there is no way to
re-program its various components; therefore the system must be robust and
handle errors gracefully. On board memory (RAM) and non-volatile memory (flash
storage) are limited. Communicating with the satellite requires a radio link,
which is only possible when the satellite is above the ground station.

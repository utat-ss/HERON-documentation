# Footprints

Every electrical component has a **footprint** which describes its physical size and shape.

Surface mount device naming conventions: http://www.pcb-3d.com/tutorials/ipc-7351b-naming-convention-for-surface-mount-device-3d-models-and-footprints/

Through hole device naming conventions: http://www.pcb-3d.com/tutorials/ipc-7251-naming-convention-for-through-hole-3d-models-and-footprints/


## Surface Mount and Through Hole

**Surface mount (SMT/SMD)** components lay on the surface of a PCB face (only on one side). Most of our components are surface mount because they are smaller and take up less space.

**Through hole (TH)** components go through a hole in the PCB from one side to the other. These are less common because they are bigger and take up less space, but are generally used for header pins and other connectors.


## Resistors and Capacitors

Resistors and capacitors generally use either the 0603 or 0402 footprints. As described [here](http://www.resistorguide.com/resistor-sizes-and-packages/), this is an imperial naming convention for width and height in thousands of an inch (sorry, but it's the general standard).

Generally we try to use 0603 resistors and capacitors since they're easier to solder. Sometimes, we have to use 0402s because of space constraints, which are more difficult to solder. Occasionally we have used 1210 resistors if they need to dissipate a lot of power.

http://blog.mbedded.ninja/electronics/components/resistors
http://blog.mbedded.ninja/electronics/components/capacitors


## Selecting Footprints

Generally, selecting which footprints to use is a tradeoff between size and ease of soldering. Bigger components are easier and more reliable to solder, but smaller components are often required due to space constraints.

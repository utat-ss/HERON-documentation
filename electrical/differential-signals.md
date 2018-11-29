# Differential Signals

[Differential Signalling Article](https://www.allaboutcircuits.com/technical-articles/the-why-and-how-of-differential-signaling/)

Generally we use **signal-ended signalling**, which means we have a single point/wire for the ground (voltage reference) and one wire for each signal we send. This means adding a signal only means adding one wire. The voltage of a signal is measured as the voltage of the wire relative to the **common ground**. For most applications, this is the best method.

Some applications use **differential signalling**, which means we have two wires (high and low) for each signal we send. Instead of measuring the voltage of the single wire relative to the common ground, we measure the signal as the voltage difference between the two wires. This is useful for protocols such as CAN where the data bus is differential (CANH and CANL signals). This is sometimes used because it produces less interference and is less affected by surrounding interference, so signal integrity is improved. This is because the two wires are routed right beside each other, so any interference received is received by both wires and cancels out.

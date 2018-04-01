
# Lab Equipment (WIP)

Here is some electrical/hardware equipment you will see and use in the lab (MP 099). We will demonstrate how to use the equipment in-person.

## Circuit Platforms
These are some of the different base platforms for building circuits.

### Breadboard
* Used to quickly build and change circuits for fast prototyping
* Has holes to insert through-hole components and wires
* Components and wires can be moved easily, but can also be knocked loose easily
* Uses through hole (TH) components
* Connections:
  * Particular sets of holes are connected under the board
  * Each long rail of holes is connected together (there are two rails on each side). It is general convention to use the red rails for power and the blue rails for ground.
  * Each row of 5 holes in the main part of the board is connected together. These are used to connect components together.
  * If you forget which holes are connected, follow the lines and notice the breaks in the lines
(add diagram/photo with lines indicating the connected sets of holes)

![](./figures/breadboard.jpg)

### Printed Circuit Board (PCB)
* Used to create final circuits or major prototype versions of a circuit
* Getting a functional PCB requires several steps:
  * Design it using CAD software (we use one called KiCad) - create a schematic of the component connections, then create a PCB layout of those components
  * Send the design to a manufacturer, who prints the board with just the traces (connections/wires)
  * Order all of the components
  * After receiving the board from the manufacturer, solder the components onto it
  * The ordering and soldering process alone usually takes at least a week, which is why PCBs are not used for fast prototyping
* A PCB’s connections can’t be changed after it is ordered and printed (with some exceptions, you can ask for some good stories)
* Generally uses surface mount (SMD/SMT) components, which are much smaller than through hole

![](./figures/pcb_front.jpg)

![](./figures/pcb_back.jpg)

### Protoboard
* Somewhat of a hybrid between a breadboard and PCB
* Has connected tracks like a breadboard, but components need to be soldered and are more likely to stay on the board
* Uses through hole components

![](./figures/protoboard.jpg)


## Tools
These are some of the tools you will use to build, test, and debug circuits.

### Multimeter
Used to measure voltage, current, resistance, and connected points in parts of a circuit.

* **Voltage** - must be **measured across a component** because voltage is the relative energy between two points
* **Current** - must be **measured through a wire**; you have to break (disconnect) the circuit at the point you want to measure, then insert the multimeter as a component in series
* **Resistance** - must be **measured across a component** because resistance is measured between two points
* **Short circuit mode** - used to determine if there is a short circuit **between two points** in the circuit (a direct connection through wires with no components in between)
  * After assembling a circuit, can check that you have made the intended connections
  * Can check that you have not made accidental connections that change your circuit. An unintentional short circuit can change the circuit's behaviour and/or produce a high current that can damage components.

![](./figures/multimeter.jpg)

### Oscilloscope
* Used to measure waveforms (signals) over time in a circuit
* This is useful for viewing the raw signal data in a wire, such as a sensor’s output or communication lines

![](./figures/oscilloscope.jpg)

### Function Generator
* Used to generate an AC (alternating current) signal with a specific voltage and waveform

![](./figures/function_generator.jpg)

### Power Supply
* Used to generate DC (direct current) power with a specific voltage or current

![](./figures/power_supply.jpg)

### Wire Cutters/Wire Strippers
* **Wire cutter** - to cut specific lengths of wire to use on breadboards
* **Wire stripper** - to remove some of the insulation on the end of a wire so it can be connected to a breadboard

![](./figures/wire_tools.jpg)

### Soldering Iron
* Used to form strong electrical connections between components on PCBs or protoboards

![](./figures/soldering_iron.jpg)

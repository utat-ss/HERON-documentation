# PCB Checklist

A good checklist before finalizing and ordering PCBs, courtesy of Jaden.

0) PCB design rule file, pad and via libraries have been imported to prototype PCB project
1) Schematic components are connected according to datasheet specifications (eg. pull-up-or-down resistors, etc.) and schematic compiles without error
2) Schematic components have legal paths to the correct footprints for the design
3) Footprints reflect the pad layout of the component itself (as per datasheet). Effort has been made to add 3D models to components
4) Components are laid out on the board with appropriately sized traces/pours in high current applications, and follow any configuration recommendations found in the datasheet (eg. bypass capacitors being in proximity to their associated pin and with their own connection to GND)
5) Silk screen (top overlay, the yellow one) labels are 1mm high and are placed near their respective components. Pin 1 symbols for components are indicated
6) PCB is as small as can be made without compromising board functionality. Boards are usually 4 layers, sometimes 2 layers
7) Important components in need of being spec'd have part numbers from DigiKey associated in Altium for BOM
8) You have exported a schematic PDF of your PCB for viewing and have sat back to be proud of your design

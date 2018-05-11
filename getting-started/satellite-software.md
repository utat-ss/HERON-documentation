# Satellite Software

Some of the subsystems in the satellite have a software component. Each subsystem that uses software has its own **microcontroller**, which executes programs that we write in C. We write code that runs on the microcontrollers (each has its own code, but with some common components).

We use a microcontroller (MCU) called the **ATMega32M1** (32M1), which is part of the AVR family of microcontrollers.

The following subsystems each have an MCU:

## OBC (On Board Computer)
- the main microcontroller on the satellite that coordinates the satellite's actions
- developed by the CDH (Command and Data Handling) subsystem

## PAY (Payload)
- controls the experiment in the paylod
- developed by the Instrumentation subsystem

## EPS (Electrical Power Systems)
- regulates the power/energy systems on the satellites
- developed by the EPS subsystem

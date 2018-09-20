25-pin connector to the board

6-pin connector (pp for obc) on the systems debug board (blue) to cut side of
sketchy protoboard

Connect to programmer

Connect OBC (or PAY or EPS) Tx and Rx to Rx and Tx on the programmer

Connect 3v3 and GND to power supply (look for the labels on the blue debug board)

AVR Programmer Configuration Utility v2, deactivate VCC output (so that your
computer does not attempt to power the board via the programmer)

NOTE: The board cannot be programmed with the Tx and Rx lines connected

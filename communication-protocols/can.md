# CAN (Controller Area Network)

CAN is a communication transfer protocol that we use to communicate between 32M1 microcontrollers in different subsystems. CAN transmits **messages**, which can each contain up to 8 bytes of data.




## Acronyms

**RX** - Receiving

**TX** - Transmitting

**MOB/MOb** - A message object (like a mailbox) that transmits or receives particular types of messages.

**CAN Transceiver** - The hardware device connected to each 32M1 that transmits and recives CAN messages on the bus.

**CAN Bus** - The set of wires connecting all CAN transceivers.

**CANH** - CAN high (bus wire)

**CANL** - CAN low (bus wire)




## Hardware

Each 32M1 is connected to its own CAN transceiver, which handles transmitting and receiving CAN messages. The CAN bus connects all the CAN transceivers using two wires called CANH and CANL. The signal on the bus is defined as the difference between the CANH and CANL voltages.




## MOBs

A MOB (message object) behaves like a mailbox that transmits or receives particular types of messages. It must be designated as either RX (receiving) or TX (transmitting). Each RX MOB has a mask (filter) that determines which messages it receives.

Each MOB is defined as a struct in C.




## RX MOBs

Here is an example of an RX mob on PAY that receives commands for requesting sensor data. See later documentation for how these values are assigned.

```C
mob_t cmd_rx_mob = {
	.mob_num = 3,                  // MOB number
	.mob_type = RX_MOB,            // MOB type (RX)
	.dlc = 3,                      // Expected length of CAN message (number of bytes)
    .id_tag = PAY_CMD_RX_MOB_ID,   // Tag is used to determine which messages to receive
	.id_mask = CAN_RX_MASK_ID,     // Mask is a filter that is combined with the tag to determine which messages to receive
    .ctrl = default_rx_ctrl,       // Control bits (leave as default)

    .rx_cb = cmd_rx_callback       // The function to be called when this MOB receives a message
};
```

If you want the RX MOB to receive all messages, such as for testing, set its mask to 0.

```C
.id_mask = { 0x0000 }
```

When the RX MOB receives a CAN message, it will trigger an interrupt and call the function specified in `rx_cb`. The CAN system passes the received data bytes and the length (number of bytes) to your function, which you can process however you want. For example:

```C
void cmd_rx_callback(const uint8_t* data, uint8_t len) {
    print("RX Callback\n");

    // Print the bytes in hex (%02x means 2 digits at a time)
    print("Received Message:\n");
    for (uint8_t i = 0; i < len; i++) {
        print("0x%02x ", data[i]);
    }
    print("\n");

    // ...
    // Process the data
}
```




## TX MOBs

Here is an example of an TX mob on PAY that transmits commands with sensor data. See later documentation for how these values are assigned.

```C
mob_t data_tx_mob = {
    .mob_num = 5,                   // MOB number
	.mob_type = TX_MOB,             // MOB type (TX)
    .id_tag = PAY_DATA_TX_MOB_ID,   // The receiving MOB will match this tag with its own tag and mask
    .ctrl = default_tx_ctrl,        // Control bits (leave as default)

    .tx_data_cb = data_tx_callback  // The function to be called before this MOB transmits a message
};
```

Unlike an RX MOB, the TX MOB does not activate on its own. When you want to transmit a CAN message, you must activate it by "resuming" it. In this example, when you call `resume_mob(&data_tx_mob)`, it will resume the TX MOB, triggering an interrupt and calling the function specified in `tx_data_cb`. The CAN system allocates space for the data bytes and the length (number of bytes) to transmit, passing pointers to them to your function. You must get the necessary data and place it in those memory locations (using the pointers). When the function ends, the CAN system takes that data and transmits the CAN message. The MOB then "pauses" itself until you call `resume_mob(&data_tx_mob)` again. For example:

```C
void data_tx_callback(uint8_t* data, uint8_t* len) {
    print("TX Callback\n");

    // Normally we would retrieve some sort of sensor data
    // For this example, just transmit the bytes 0x00 0x01 0x02 0x03

    // Want to transmit 4 bytes
    *len = 4;

    // Set the data
    data[0] = 0x00;
    data[1] = 0x01;
    data[2] = 0x02;
    data[3] = 0x03;

    // After this function ends, the CAN system will transmit this message
}
```




## Initializing CAN

At the beginning of your program, you must initialize the CAN system and each individual MOB. For example:

```C
// Enable CAN interrupts and the CAN transceiver
init_can();

// Initialize MOBs (note the different functions for RX and TX)
init_rx_mob(&cmd_rx_mob);
init_tx_mob(&data_tx_mob);
```

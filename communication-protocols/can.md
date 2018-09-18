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
    * len = 4;

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


## Message Objects (MObs)
Before sending messages over CAN, the message objects (MObs) need to be initialized. A universal MOb structure is defined in `lib-common/include/can/can.h`:

``` C
typedef struct {
    // common
    uint8_t mob_num;
    uint8_t dlc;
    mob_id_tag_t id_tag;
    mob_ctrl_t ctrl;
    mob_type_t mob_type;

    // rx specific
    mob_id_mask_t id_mask;
    can_rx_callback_t rx_cb;

    // tx specific
    can_tx_callback_t tx_data_cb;
    uint8_t data[8];
} mob_t;
```


### mob_num

Each 32M1 has space for **six** MObs which can be active at any given time, denoted by `mob_num`. In the 32M1 datasheet, you might see these referred to as 'pages', although `mob_num` and the page number are functionally the same. Before editing MOb variables, the mob needs to be selected, which is handled automatically by the CAN library. _Further reads or writes to MOb-related registers will only affect the selected MOb_.

Priority is given is given to the MOb with the smallest `mob_num` when choosing which MOb to send/receive. For example, if two TX MObs are initialized and resumed on Board A, then MOb 0 will send first. Likewise, if a TX from Board A can be handled by two RX MObs on Board B, then the RX MOb with the lower `mob_num` will trigger the RX interrupt.

<!-- Check with Sidd that the above TX example is correct, and whether MOb 1 will send.-->

### id_tag and id_mask

Each MOb on the _entire CAN bus_ should be given a _unique_ `id_tag`. ID tags are used by RX MObs when masking incoming transmissions via `id_mask`. Both `id_tag` and `id_mask` are _eleven_ bits long and should be defined as single-element structs during initialization:

You may see references to AUTO MObs in documentation and code. We are not using them, so ignore them.

``` C
// MOb A
.mob_type = TX_MOB,
.id_tag =   { 0x0001 },

// MOb B
.mob_type = TX_MOB,
.id_tag =   { 0x0002 },

// MOb C
.mob_type = RX_MOB,
.id_tag =   { 0x0003 },
.id_mask =  { 0xFF01 },
```

From the above example, notice that each MOb has been given a unique ID and that the RX MOb has been given the mask `0xFF01`. Assume that the TX MObs are initialized on Board A and the RX MOb on Board B.

Masks work such that if `id_mask` has a 1 in a certain bit position, then the incoming `id_tag` and the `id_tag` of the RX MOb must be _equal_ in that bit position. If `id_mask` has a 0 in that position, then the mask treats that position as a "don't care".

In this example, the RX MOb will ignore transmissions from MOb B. The RX mask cares about equality in bits 10-8 and bit 1. All the ID tags have 0's in bits 10-8, but since the RX MOb has a 1 in bit 0 and MOb B has a 0, the RX MOb will mask transmissions from MOb B. Meanwhile, both the RX MOb and MOb A share a 1 in bit 0, so the RX MOb will accept incoming transmissions from MOb A.

### ctrl and mob_type

The `mob_type` variable defines the type of MOb and should be one of `TX_MOB` or `RX_MOB`, depending on the type of MOb you would like to define. The `ctrl` variable is a six-element struct defined in `lib-common/include/can/can.h` which holds CAN control parameters. Default TX and RX control configurations are also defined in `can.h` (and work pretty well for the majority of cases).

``` C
// struct to hold RTR, IDE, IDE Mask, RTR Mask and RBnTag bits;
// all boolean
typedef struct {
    uint8_t rtr; // 1 for remote frames, 0 for data frames
    uint8_t ide; // specifies CAN rev; should always be 0, for rev A
    uint8_t ide_mask; // masking bits for RX
    uint8_t rtr_mask; // masking bits for RX
    uint8_t rbn_tag; // masking bit for RX
    uint8_t rplv; // RPLV bit
} mob_ctrl_t;

// TODO: change these; ide_mask SHOULD matter
#define default_rx_ctrl { 0, 0, 0, 0, 0, 0 }
#define default_tx_ctrl { 0, 0, 0, 0, 0, 0 }
```

### dlc

The `dlc` variable stores the number of bytes to be sent/received, and can be up to 8 bytes long. DLC needs to be defined upon initialization for RX MObs. <!--(and possibly TX MObs sending remote frames for auto-reply, but that's a Sidd question)--> If the incoming message does not have the expected DLC an error will be thrown. <!--technically a warning, will test this-->

When the MOb is initialized for TX, `dlc` needs to be set to the length of the data (in bytes) to be transmitted. This is assigned in the TX callback function pointed to by `tx_data_cb`, which is described in the following section.

### rx_cb, tx_data_cb and data[8]

The `rx_cb` and `tx_data_cb` variables store function pointers to the RX and TX callback functions for a specific MOb. Different callback functions can be defined for each individual MOb. These functions are called from their respective interrupt-handling functions in `lib-common/src/can/can.c`. They are passed a pointer to the array of data to be sent/received, and either the length of the array (to the RX callback) or a pointer to the length of the array (to be set in the TX callback).

`tx_data_cb` is called upon initialization and after a transmission has been sent. After a transmission is sent, it generates a `TXOK` interrupt and calls `tx_data_cb` from `load_data` to get fresh data. `rx_cb` is called from `handle_rx_interrupt()` after once a transmission has been sucessfully recieved, generating a `RXOK` interrupt. The functions should be defined in the file which includes CAN, and the two variables can be set simply by passing them the name of the function.

``` C
void rx_callback(uint8_t* data, uint8_t len) {
    print("TX received!\n");
    print("%s\n", (char *) data);
}

void tx_callback(uint8_t* data, uint8_t* len) {
    * len = 7;
    char str[] = "Hello!";

    for(uint8_t i = 0; i < *len; i++) {
        data[i] = str[i];
    }
}
```


## MOb Allocation
The limited number of CAN Message Objects that can be created on the ATmega32M1 \(only 6\) requires us to strictly allocate the purpose of each MOb. The allocation is done under the following assumptions:

* No MOb shall be reinitialized or renamed
* Only three subsystem microcontrollers are used, referred to as OBC, EPS and PAY
* EPS and PAY do not communicate with each other
* A message cannot have multiple recipients, but multiple senders can send to the same MOb
* No two MObs on the bus can have the same ID

The MOb names are with respect to the OBC (e.g. STATUS_TX is transmitted from the OBC, STATUS_RX is received on the OBC).

| \# | Name | OBC (00) | EPS (10) | PAY (01) |
| :--- | :--- | :--- | :--- | :--- |
| 0 | ```STATUS_Tx_MOb``` | Tx | Rx | Rx |
| 1 | ```STATUS_Rx_MOb``` | Rx | Tx | Tx |
| 2 | ```CMD_Rx_MOb``` | Rx | Tx | Tx |  
| 3 | ```PAY_CMD_Tx_MOb``` | Tx | N/A | Rx |  
| 4 | ```EPS_CMD_Tx_MOb``` | Tx | Rx | N/A |  
| 5 | ```DATA_Rx_MOb```| Rx | Tx | Tx |  

The Message ID of every message is structured as the following:

| Bit | 10-9 | 8-3 | 2-1 | 0 |
| :--- | :--- | :--- | :--- | :---
| | Receiver ID | `_BV(MOb #)` | Sender ID | Rx/T̅̅x |

The Receiver and Sender IDs are 00 for OBC, 01 for PAY, and 10 for EPS.

Bits 8-3 are used to encode the universal MOb number, 0-5, in one-hot encoding. The `_BV(MOb #)` macro simply means ```000000001 << MOb #```. So, if `MOb # = 5`, the value of bits 8-3 would be `100000`, and for `MOb # = 2` it would be `000100`.

Bit 0 is 0 if it is a Tx mailbox, or 1 if it is an Rx mailbox.

This allows a single universal mask to be valid for all mailboxes:
`MOb_Mask = 111 1111 1000`.
This allows selectivity in Receiver ID and MOb #, while remaining sender-agnostic.

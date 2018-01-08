# 4. Data Transfer Protocols

## 4.1 Universal Asynchronous Receiver-Transmitter \(UART\)

## 4.2 Serial Peripheral Interface \(SPI\)

### 4.2.1 Background
SPI is a synchronous serial communication protocol used to communicate between devices. SPI uses a master-slave architecture, with a single master device initiating the communication frame, and operates in full-duplex mode (data can be sent in both directions).

To achieve this, SPI typically uses four wires to communicate, which are referred to as the _SPI bus_. Three lines, SCK, MOSI and MISO, are shared between _all devices_ on a SPI bus. The fourth line, CS, is _unique to every slave device_, and are all connected to the master device. To initiate communication, the master device writes CS LOW (chip select LOW) to a particular device, and begins clocking out a square wave on SCK \(source clock\). This square wave synchronizes the two devices, and data is then transferred on either the rising or falling edge. MOSI \(master out slave in\) and MISO \(master in slave out\) serve as the data transmission lines.

In case all this is new to you \(which it very likely is\) in digital systems data is typically transferred in the form of 1's and 0's. In hardware, 1's and 0's are represented as high and low voltages. Our satellite uses 3.3V, so a 1 corresponds to ~3.3V on a particular line, and a 0 corresponds to ~0V.


![Diagram of a general SPI bus](./figures/spi_bus.png)\




## 4.3 Inter-Integrated Circuit \(I2C\)

## 4.4 Controller Area Network \(CAN\)

## 4.1 Background

## 4.2 Message Objects \(MObs\)
Before sending messages over CAN, the message objects (MObs) need to be initialized. A universal MOb structure is defined in `can.h` and reproduced below:

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


### 4.2.1 mob_num

Each 32M1 has space for **six** MObs which can be active at any given time, denoted by `mob_num`. In the 32M1 datasheet, you might see these referred to as 'pages', although `mob_num` and the page number are functionally the same. Before editing MOb variables, the mob needs to be selected, which is handled automatically by the CAN library. _Further reads or writes to MOb-related registers will only affect the selected MOb_. 

Priority is given is given to the MOb with the smallest `mob_num` when choosing which MOb to send/receive. For example, if two TX MObs are initialized and resumed on Board A, then MOb 0 will send first. Likewise, if a TX from Board A can be handled by two RX MObs on Board B, then the RX MOb with the lower `mob_num` will trigger the RX interrupt.

<!-- Check with Sidd that the above TX example is correct, and whether MOb 1 will send.-->

### 4.2.2 id_tag and id_mask

Each MOb on the _entire CAN bus_ should be given a _unique_ `id_tag`. ID tags are used by RX and AUTO MObs when masking incoming transmissions via `id_mask`. Both `id_tag` and `id_mask` are _eleven_ bits long and should be defined as single-element structs during initialization:

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

### 4.2.3 ctrl and mob_type

The `mob_type` variable defines the type of MOb and should be one of `TX_MOB`, `RX_MOB` or `AUTO_MOB`, depending on the type of MOb you would like to define. 'The `ctrl` variable is a six-element struct defined in `can.h` which holds CAN control parameters. Default TX and RX control configurations are also defined in `can.h` (and work pretty well for the majority of cases).

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

The most important exception to the default control configuration is when setting up auto-reply. For an AUTO MOb, `rtr` and `rplv` need to be set to 1. When setting up a TX MOb to send remote frames for use with AUTO MObs, only `rtr` needs to be set. Refer to `can_print_auto.c` and `can_print_auto_remote.c` in `lib-common/examples/` for more detail.

### 4.2.4 dlc

The `dlc` variable stores the number of bytes to be sent/received, and can be up to 8 bytes long. DLC needs to be defined upon initialization for RX MObs <!--(and possibly TX MObs sending remote frames for auto-reply, but that's a Sidd question)-->. If the incoming message does not have the expected DLC an error will be thrown. <!--technically a warning, will test this-->

When the MOb is initialized for TX, `dlc` needs to be set to the length of the data (in bytes) to be transmitted. This is assigned in the TX callback function pointed to by `tx_data_cb`, which is described in the following section.

### 4.2.5 rx_cb, tx_data_cb and data[8]

The `rx_cb` and `tx_data_cb` variables store function pointers to the RX and TX callback functions for a specific MOb. Different callback functions can be defined for each individual MOb. These functions are called from the function handling the interrupt in `can.c` and passed a pointer to the array of data to be sent/received.

These functions are called when the respective The functions should be defined in the file which includes CAN, and the two variables can be set simply by passing them the name of the function.
``` C
void rx_callback(uint8_t* data, uint8_t len) {
    print("TX received!\n");
    print("%s\n", (char *) data);
}

void tx_callback(uint8_t* data, uint8_t* len) {
    *len = 7;
    char str[] = "Hello!";

    for(uint8_t i = 0; i < *len; i++) {
        data[i] = str[i];
    }
}
```
**

The `rx_cb` function takes two inputs, a pointer to the array of incoming data and the length of the array. `tx_cb` takes a pointer to the array of data to be sent, and the length of the array should be passed



## 4.3 Initializing CAN

## 4.4 Sending TX and RX

## 4.5 AUTO MObs

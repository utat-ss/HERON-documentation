# MOb Allocation
The limited number of CAN Message Objects that can be created on the ATmega32M1 \(only 6\) requires us to strictly allocate the purpose of each MOb. The allocation is done under the following assumptions:

* No MOb shall be reinitialized or renamed
* Only three subsystem microcontrollers are used, referred to as OBC, EPS and PAY
* EPS and PAY do not communicate with each other
* A message cannot have multiple recipients, but multiple senders can send to the same MOb
* No two MObs on the bus can have the same ID

The MOb names are with respect to the OBC \(e.g. STATUS{% raw %}\_{% endraw %}TX is transmitted from the OBC, STATUS\_RX is received on the OBC).

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
| | Receiver ID | ```_BV(MOb #)``` | Sender ID | Rx/T̅̅x |

The Receiver and Sender ID are 00 for OBC, 01 for PAY, and 10 for EPS.

Bits 8-3 are used to encode the universal MOb number, 0-5, in one-hot encoding. For those unfamiliar with the \_BV macro, it simply means ```000000001 << MOb #```. So, if MOb # is 5, the value of bits 2-7 would be 100000, and for MOb # = 2 it would be 000100.

Finally, bit 0 is 0 if it as Tx mailbox, 1 if it is an Rx mailbox.

This allows a single universal mask to be valid for all mailboxes:
``` MOb_Mask = 111 1111 1000 ```
This allows selectivity in Receiver ID and MOb #, while remaining sender-agnostic.

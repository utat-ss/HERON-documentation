# 9. CAN MOb Allocation

The limited number of CAN Message Objects that can be created on the ATmega32M1 \(only 6\) requires us to strictly allocate the purpose of each MOb. The allocation is done under the following assumptions:

* No MOb shall be reinitialized or renamed
* Only three subsystem microcontrollers are used, referred to as OBC, EPS and PAY
* EPS and PAY do not communicate with each other
* A message cannot have multiple recipients, but multiple senders can send to the same MOb
* No two MObs on the bus can have the same ID

The MOb names are with respect to the OBC \(e.g. STATUS{% raw %}\_{% endraw %}TX is transmitted from the OBC, STATUS\_RX is received on the OBC).

| \# | Name | OBC | EPS | PAY |
| :--- | :--- | :--- | :--- | :--- | 
| 0 | STATUS\_Tx\_MOb | Tx | Rx | Rx | 
| 1 | STATUS\_Rx\_MOb | Rx | Tx | Tx | 
| 2 | CMD\_Rx\_MOb | Rx | Tx | Tx |  
| 3 | PAY\_CMD\_Tx\_MOb | Tx | N/A | Tx |  
| 4 | EPS\_CMD\_Rx\_MOb | Tx | Tx | N/A |  
| 5 | DATA\_Rx\_MOb | Rx | Tx | Tx |  




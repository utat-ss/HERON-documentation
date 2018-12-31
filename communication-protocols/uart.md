# UART (Universal Asynchronous Receiver Transmitter)

UART is a protocol that allows us to use the 32M1 to print messages to the terminal in CoolTerm/XTerm.

```c
init_uart();
print("Hello World!\n");
```

The `print()` function behaves like C's `printf()` function (but prints to UART instead of `stdout`), so you can use format specifiers (see [documentation](http://www.cplusplus.com/reference/cstdio/printf/)).


## Implementation of UART

[Stanford CS140](https://web.stanford.edu/class/cs140e/notes/lec4/uart-basics.pdf)

[Wikipedia](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter)

[Circuit Basics](http://www.circuitbasics.com/basics-uart-communication/)

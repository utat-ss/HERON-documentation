# UART (Universal Asynchronous Receiver Transmitter)

UART is a protocol that allows us to use the 32M1 to print messages to the terminal in CoolTerm/XTerm.

```c
init_uart();
print("Hello World!\n");
```

The `print()` function behaves like C's `printf()` function (but prints to UART instead of `stdout`), so you can use format specifiers (see [documentation](http://www.cplusplus.com/reference/cstdio/printf/)).

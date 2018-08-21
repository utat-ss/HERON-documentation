# Volatile Variables

The `volatile` keyword instructs the C compiler to not make assumptions about the value of a variable. Normally, the compiler optimizes the program based on things guaranteed to be true about variable values. But these optimizations are under the assumption that only the current program can change a variable's value.

The 32M1 has **MMIO** (Memory-Mapped Input/Output) at specific memory locations. This means that values in specific locations in RAM are directly mapped to hardware devices such as port and peripherals. Things other than the C program can change the values of these variables.

In these special cases, you need to use the `volatile` keyword on a variable so the compiler doesn't assume that only the C program can change its value. If you don't put `volatile` and the compiler makes that assumption, the program will probably behave strangely and produce incorrect values.

For example, in `spi.h`:

```C
typedef volatile uint8_t* port_t;
```

The `volatile` keyword is used because the port location in memory is directly mapped to pins in the hardware which could change outside of the program. This ensures correct behaviour.

# Technical

## Memory Allocation and `malloc()`

**You are not allowed to use `malloc()`.** This is because our satellite is an embedded system and `malloc()` is a non-deterministic operation.

- There is a limited amount of memory, so it is likely to fail.
- It may be very slow, blocking the system.

**All arrays must be statically declared with a fixed number of elements**, preferably using a named constant that can be easily changed.


## Integer Types

**Do not use the `int` type.** All integer types should be declared with explicit sizes using types in the `<stdint.h>` library, such as `uint8_t`, `uint32_t`, and `int16_t`. This is to be clear about the maximum value the integer can hold and should support.

**Be careful about integer sizes.** We need to avoid integer overflows at all costs, so make sure your integer types are large enough for the values they will contain.


## Infinite Loops and Timeouts

**Do not allow the possibility of an infinite loop. Every loop should have a guaranteed maximum number of times it can execute.** Do not assume hardware will always behave as expected. Always add a timer as a fallback for loops waiting for a hardware event so the MCU cannot get stuck in an infinite loop.

The most common timeout structure starts at 65,535 (2^16 - 1, declared as the built-in `UINT16_MAX` constant), with a `uint16_t` type counting down to 0.

For example, say we are waiting for the pin PB0 to go low before proceeding.

```C
// bad, could infinite loop
while (bit_is_set(PINB, PB0)) {
    continue;
}

// good, protect against rare case of infinite loop
uint16_t timeout = UINT16_MAX;
while (bit_is_set(PINB, PB0) && timeout > 0) {
    timeout--
}
// might want to check if timeout == 0 here if you want to detect a timeout
```


## Functions

If you declare a function without any parameters (and nothing in the parentheses), C considers the function to accept any arguments. Functions that do not take any parameters should have `void` in the parentheses, to explicitly declare it as taking no parameters.

See a technical explanation [here](https://stackoverflow.com/questions/20091233/declare-a-void-function-in-c)

```C
// bad
void do_something();
...
void do_something() {
    ...
}

// good
void do_something(void);
...
void do_something(void) {
    ...
}
```


## Header File Guards

All header files (`.h`) should have a guard statement to prevent duplicate declarations when included multiple times.

Say we have a header file called `file.h`:

```C
#ifndef FILE_H
#define FILE_H

<code>

#endif
```


## Compiler Warnings

**You should not have any compiler warnings.** These often detect non-obvious bugs such as bitshift overflows.

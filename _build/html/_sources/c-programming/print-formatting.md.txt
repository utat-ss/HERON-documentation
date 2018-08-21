# Print Formatting

[C `printf()` formatting](http://www.cplusplus.com/reference/cstdio/printf/)

The UART `print()` function behaves almost identically to the standard C `printf()` function. This gives a powerful way to format values as strings for output.

**Using `%f` or `%lf` requires a special compiler flag to be enabled. Talk to Sidd about how to do this.**

Here are the format specifiers for common data types:

Data Type(s) | Format Specifier(s)
- | -
`uint8_t`, `uint16_t` | `%u` (Unsigned)
`uint32_t` | `%lu` (Long Unsigned)
`int8_t`, `int16_t` | `%d` (Decimal - Signed)
`int32_t` | `%ld` (Long Decimal - Signed)
`uint8_t`, `uint16_t`, `int8_t`, `int16_t` | `%x` (Hexadecimal - Lowercase), `%X` (Hexadecimal - Uppercase)
`uint32_t`, `int32_t` | `%lx` (Long Hexadecimal - Lowercase), `%lX` (Long Hexadecimal - Uppercase)
`float` | `%f` (Float)
`double` | `%lf` (Long Float)


## Integer Formatting

An example of controlling integer formatting output as hex:

```C
print("0x%02x", value);
// lowercase hex, always with 2 hex digits (zero padded if necessary)
// add "0x" at the beginning to indicate to the reader that the output is in hex
```

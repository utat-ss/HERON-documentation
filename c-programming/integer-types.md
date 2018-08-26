# Integer Types

https://www.nongnu.org/avr-libc/user-manual/group__avr__stdint.html
https://en.cppreference.com/w/cpp/language/integer_literal

You will often see variable types such as `uint8_t` and `int16_t` in our code. Instead of using the standard `int` type, we use these types to specify how many bits each integer has. This is because the number of bits in `int` varies depends on the compiler used, so we want to be explicit about the number of bits used. This is also to prevent accidental integer overflow if the number of bits is too small.

Standard integers are signed (can be positive, zero, or negative). The `u` prefix means unsigned (can only be positive or zero). The number specifies the number of **bits** (not bytes). The `_t` suffix is a C convention for naming types.

When writing **integer literals** (constant integer values written out in code), the AVR compiler defaults to 16-bit integers. If we want to express a literal that uses more than 16 bits, we need to use an **integer suffix** for a larger bit size.

For example, we can't use the literal `1000000` because the maximum 16-bit unsigned literal is `65535` (2^16 - 1) and the maximum 16-bit signed literal is `32767` (2^15 - 1). To make this work, we need to use the literal `1000000L` or `1000000UL`. The `L` suffix means `long` (signed 32-bit) while the `UL` suffix means `unsigned long` (unsigned 32-bit).


## Available Types

| Type | Integer | Literal Suffix | sizeof |
| :--- | :--- | :--- | :--- | :--- |
| `int8_t` | signed 8-bit | None | 1 |
| `uint8_t` | unsigned 8-bit | None | 1 |
| `int16_t` | signed 16-bit | None | 2 |
| `uint16_t` | unsigned 16-bit | None | 2 |
| `int32_t` | signed 32-bit | `L` (long) | 4 |
| `uint32_t` | unsigned 32-bit | `UL` (unsigned long) | 4 |
| `int64_t` | signed 64-bit | `LL` (long long) | 8 |
| `uint64_t` | unsigned 64-bit | `ULL` (unsigned long long) | 8 |

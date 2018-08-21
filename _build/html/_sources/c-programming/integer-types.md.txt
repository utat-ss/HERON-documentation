# Integer Types

You will often see variable types such as `uint8_t` and `int16_t` in our code. Instead of using the standard `int` type, we use these types to specify how many bits each integer has. This is because the number of bits in `int` varies depends on the compiler used, so we want to be explicit about the number of bits used. This is also to prevent accidental integer overflow if the number of bits is too small.

The `u` prefix means unsigned (only positive numbers). The number specifies the number of **bits** (not bytes). The `_t` suffix is a C convention for naming types.

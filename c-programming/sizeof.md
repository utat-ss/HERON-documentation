# sizeof

https://en.cppreference.com/w/cpp/language/sizeof

`sizeof` is a special operator in C that is evaluated at **compile-time**. It tells you how many bytes a type or value take up in memory. So, putting the expression `sizeof(type)` evaluates to a compile-time value, an integer of how many bytes `type` takes up in memory. If you use the expression `sizeof(value)`, it tells you how many bytes the type of `value` takes up.

Example expressions:

```C
sizeof(uint8_t) // evaluates to 1
sizeof(int32_t) // evaluates to 4

sizeof(7)   // evaluates to 2 (integer literal defaults to 16 bit)
sizeof(7L) // evaluates to 4 (`L` creates a 32-bit integer literal)
```

## Array Size

`sizeof` can be used to determine the size of an array. Even though all arrays in C have a constant size, this is useful if you change the size of an array and want your code to update to use that size without having to change code somewhere else.

```C
uint16_t array[10];

// Say we want to loop through all array values without assuming
// the array size in the loop
for (uint8_t i = 0; i < ( sizeof(array) / sizeof(array[0]) ); i++) {
    print("%u ", array[i]);
}
```

What happens here is that the expression `sizeof(array)` evalutes to the **total number of bytes that the array takes up**. The expression `sizeof(array[0])` evalutes to the the **number of bytes that one value in the array takes up** (using the first value since all take up the same number of bytes).

So, if we change the array declaration to `uint16_t array[12]`, we do not need to change the loop since it will calculate the array size on its own.

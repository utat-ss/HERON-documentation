# Bitwise Operators

These operators directly manipulate the bits in numbers. A lot of our software is manipulating 8 bit registers on our microcontroller so bitwise operators are very common in our software. The first two are very similar to ```&&``` and ```||``` mentioned above. They are the “and” and “or” bitwise operators, represented by ```&``` and ```|```. They both combine two numbers into one. We will explain different operators.


## AND (`&`)

![](../figures/fig4.jpg)

We move from right to left comparing each pair of bits. If both the bits are 1 the result
will have a 1 in that location, otherwise it will have a 0. Just like ```&&``` it needs two 1’s to get a 1.

```
  10110001
& 00101110
----------
  00100000
```

There is only a 1 in the 6th bit position because that is the only position where there is a 1 in both the numbers.


## OR (`|`)

This operator is similar, except if at least one of the bits is a 1 the result will have a 1 in that position.

![](../figures/fig5.jpg)


## XOR (`^`) (Exclusive OR)

Exclusive OR is similar to OR, but if one and only one of the bits is a 1 then the result will have a 1 in that position.

![](../figures/fig6.jpg)


## NOT (`~`) (Compliment)

This only applies to one number (instead of two), and it flips all the bits in the number so 1’s become 0’s and vice-versa.

**Example 1:**

```C
uint8_t x = 0b10100011;
x = ~x;
```

x becomes `0b01011100`.

**Example 2:**

```C
uint8_t x = 0b11110000;
x = ~x;
```

x becomes ```0b00001111```.




## Modified Assignment

You can use the following operators to modify and assign a variable in one step: ```&=```, ```|=```, and ```^=```.


## Bitwise Shifts (`<<`, `>>`)

There are left (```<<```) and right (```>>```) bit shifts. They do what the name suggests, shift the bits in a number by a certain number of places.

![](../figures/fig7.jpg)

The bits are shifted to the left and 0’s are shifted into the empty spots. You
might think x is now ```0b111111110000```, but that is incorrect because that is
no longer an 8 bit number. It is actually ```0b11110000```. With bit shifts you
have to consider the size of the variable because if you shift a number to the
left past its limit those bits will be cut off.

![](../figures/fig8.jpg)

When we shift this number by four, four of the leading zeros are being cut off so we end up with ```0b0000111111110000```, or more nicely ```0x0FF0```.

The right bit shift does the exact same thing except in the opposite direction. No matter the size
of the variable the bits will be cut off.

![](../figures/fig9.jpg)

In this case the bits are shifted out of the variable to the right and 0’s are shifting into the variable on the left. Here we will end up with x as ```0b00001111```.

# Bitwise Operators

These operators directly manipulate the bits in integer numbers (generally represented as `uint` types). A lot of our software involves manipulating 8 bit registers on our microcontroller, so bitwise operators are very common in our software.

The first two are very similar to ```&&``` and ```||``` mentioned above. They are the “and” and “or” bitwise operators, represented by ```&``` and ```|```. They both combine two numbers into one.


## Single Bit Operations

First, we'll discuss how bitwise operators are applied between two bits. In these cases, we take two 1-bit values and apply the operation to get a 1-bit result.

Bitwise operation behaviour is commonly represented using a **truth table** as shown below. It shows all possible combinations of inputs and the resulting outputs.

### AND

The AND operation produces a 1 if both of the inputs are 1. Otherwise, it produces a 0.

A | B | A AND B
-- | -- | --
0 | 0 | 0
0 | 1 | 0
1 | 0 | 0
1 | 1 | 1

### OR

The OR operation produces a 1 if either or both of the inputs are 1. Otherwise, it produces a 0.

A | B | A OR B
-- | -- | --
0 | 0 | 0
0 | 1 | 1
1 | 0 | 1
1 | 1 | 1

### XOR (Exclusive OR)

The XOR operation produces a 1 if either but not both of the inputs are 1 (the meaning of exclusive). Otherwise, it produces a 0. This is similar to OR, but if both inputs are 1 the result is 0.

A | B | A XOR B
-- | -- | --
0 | 0 | 0
0 | 1 | 1
1 | 0 | 1
1 | 1 | 0

### NOT (Compliment/Inverse)

The NOT operation is called an **unary** operator because it only has one input. The NOT operation "flips" a bit.

A | NOT A
-- | --
0 | 1
1 | 0


## Multi-Bit Operations

We generally use the AND, OR, and XOR operations on numbers with multiple bits. These operations simply apply the operator between each pair of bits.

## AND (`&`)

We represent AND using the `&` operator in C.

![](../figures/fig4.jpg)

```
  10110001
& 00101110
----------
  00100000
```

There is only a 1 in the 6th bit position because that is the only position where there is a 1 in both the numbers.


## OR (`|`)

We represent OR using the `|` operator in C.

If at least one of the bits is a 1, the result will have a 1 in that position.

![](../figures/fig5.jpg)

```
  10110001
| 00101110
----------
  10111111
```

## XOR (`^`)

We represent XOR using the `^` operator in C.

If one and only one of the bits is a 1, then the result will have a 1 in that position.

![](../figures/fig6.jpg)

```
  10110001
^ 00101110
----------
  10011111
```

## NOT (`~`)

This still only applies to one number, and it flips all the bits so 1’s become 0’s and vice-versa.

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


## Bitwise vs. Logical Operators

Note that `&` and `|` are different from `&&` and `||`. `&` and `|` are **bitwise operators**, which take two integers and produce another integer. `&&` and `||` are **logical operators**, which take two booleans and produce another boolean (commonly used in conditional expressions).

Also note that `~` (bitwise NOT) is different from `!` (logical NOT).


## Modified Assignment

You can use the following operators to modify and assign a variable in one step: ```&=```, ```|=```, and ```^=```.


## Bitwise Shifts (`<<`, `>>`)

C has bitwise shift operators for left (`<<`) and right (`>>`) bit shifts. They do what the name suggests, shift the bits in a number by a certain number of places.

![](../figures/fig7.jpg)

The bits are shifted to the left and 0’s are shifted into the empty spots. You
might think x is now ```0b111111110000```, but in this case that is incorrect because that is
no longer an 8 bit number. It is actually ```0b11110000```. With bit shifts you
have to consider the size of the variable because if you shift a number to the
left past its limit those bits will be cut off.

![](../figures/fig8.jpg)

When we shift this number by four, we end up with `0b0000111111110000` (or `0x0FF0`) because this 16 bit variable can hold those bits.

The right bit shift does the exact same thing except in the opposite direction. No matter the size
of the variable the bits will be cut off.

![](../figures/fig9.jpg)

In this case the bits are shifted out of the variable to the right and 0’s are shifted into the variable on the left. Here we will end up with x as ```0b00001111```.


## Applications

Here are the most common uses of bitwise operators in our software.

Very frequently we have to manipulate specific bits in numbers (usually 8 bit, 16 bit, or 32 bit). For example, this is necessary to change settings in the microcontroller or other devices. We use bitwise operators to change certain bits while not affecting others.

### OR

If you OR any bit with 0, you always get the same bit. If you OR any bit with 1, you always get 1. We can use the OR operator to force specific bits to be 1 while leaving other bits as they are.

For example, this is how to set bit 5 to 1.

![](../figures/fig12.jpg)

Now the value of x is ```0b00100000```.

### AND

If you AND any bit with 0, you always get 0. If you AND any bit with 1, you always get the same bit. We can use the AND operator to force specific bits to be 0 while leaving other bits as they are.

For example, you can set bit 5 to 0 with the following:

![](../figures/fig13.jpg)

The expression `1 << 5` is `00100000`, so `~(1 << 5)` is `11011111`.

Now the value of x is `0b11011111`.

### XOR

If you XOR any bit with 0, you always get the same bit. If you XOR any bit with 1, you always get the opposite bit. We can use the XOR operator to flip specific bits while leaving other bits as they are.

For example, you can switch the value of a bit with the following:

![](../figures/fig14.jpg)

Now the value of x is `0b11011111`.

### NOT

If you NOT any bit, you always get the opposite bit. We can use the NOT operator to flip all bits.

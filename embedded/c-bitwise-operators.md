# Bitwise Operators

These operators directly manipulate the bits in numbers. A lot of our software is manipulating 8 bit registers on our microcontroller so bitwise operators are very common in our software. The first two are very similar to ```&&``` and ```||``` mentioned above. They are the “and” and “or” bitwise operators, represented by ```&``` and ```|```. They both combine two numbers into one. First I will explain the “and” operator.

This is how it is used.

![](../figures/fig4.jpg)

It works like this. We’ll move from right to left comparing the bits. If both the bits are 1 the result
will have a 1 in that location, otherwise it will have a 0. Just like ```&&``` it needs two 1’s to get a 1.

```
10110001
00101110
--------
00100000
```

There is only a 1 in the 6th bit position because that is the only position where there is a 1 in both the numbers.

The “or” operator works exactly the same except if at least one of the bits is a 1 the result will have a 1 in that position.

![](../figures/fig5.jpg)

There is another operator really similar to those two. It’s called the XOR (exclusive or) operator
and is represented as ```^```. If one and only one of the bits is a 1 then the result will have a 1 in that
position.

![](../figures/fig6.jpg)

All these operators can also be used as ```&=```, ```|=``` and ```^=```.

The next group of bitwise operators is bit shifts. There is left (```<<```) and right (```>>```) bit shifts. They do what the name suggests, shift the bits in a number.

![](../figures/fig7.jpg)

The bits are shifted to the left and 0’s are shifted in. You might think x is now ```0b111111110000```
but that is incorrect because that is no longer an 8 bit number. It is actually ```0b11111111```. With bit
shifts you have to consider the size of the variable because if you shift a number to the left past
its limit those bits will be cut off.

![](../figures/fig8.jpg)

When we shift this number by four, four of the leading zeros are being cut off so we end up with ```0b0000111111110000```, or more nicely ```0x0FF0```.

The right bit shift does the exact same thing except in the opposite direction. No matter the size
of the variable the bits will be cut off.

![](../figures/fig9.jpg)

In this case the bits are shifted out of the variable to the right and 0’s are shifting into the variable on the left. Here we will end up with x as ```0b00001111```.

There is one final bitwise operator called the not or compliment operator. It flips all the bits in the number so 1’s become 0’s and vice-versa. It is represented by the ```~``` symbol.

**Example 1:**

![](../figures/fig10.jpg)

In this example x becomes 0b01011100.

**Example 2:**

![](../figures/fig11.jpg)

In this example x becomes ```0b1111111100001111```. Don’t forget about those leading zeroes.


## Applications of Bitwise Operators

Here are the most common uses of bitwise operators in our software.

Very frequently we have to set bits in an 8 bit number to change settings in our microcontroller. We use bitwise operators to change certain bits while not affecting others.

This is how to write a certain bit to 1

![](../figures/fig12.jpg)

Now the value of x is ```0b00100000```.

And you can set a certain bit to 0 with the following,

![](../figures/fig13.jpg)

Now the value of x is 0b11011111

You can also switch the value of a bit with the following:

![](../figures/fig14.jpg)

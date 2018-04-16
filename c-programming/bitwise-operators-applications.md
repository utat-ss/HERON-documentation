# Applications of Bitwise Operators

Here are the most common uses of bitwise operators in our software.

Very frequently we have to manipulate specific bits in numbers (usually 8 bit, 16 bit, or 32 bit). For example, this is necessary to change settings in the microcontroller or other devices. We use bitwise operators to change certain bits while not affecting others.

## OR

If you OR any bit with 0, you always get the original bit. If you OR any bit with 1, you always get 1. We can use the OR operator to force specific bits to be 1 while leaving other bits as they are.

For example, this is how to set bit 5 to 1.

![](../figures/fig12.jpg)

Now the value of x is ```0b00100000```.

## AND

If you AND any bit with 0, you always get 0. If you AND any bit with 1, you always get the original bit. We can use the AND operator to force specific bits to be 0 while leaving other bits as they are.

For example, you can set bit 5 to 0 with the following:

![](../figures/fig13.jpg)

The expression `1 << 5` is `00100000`, so `~(1 << 5)` is `11011111`.

Now the value of x is `0b11011111`.

## XOR

If you XOR any bit with 0, you always get the original bit. If you XOR any bit with 1, you always get the **opposite** of the original bit. We can use the XOR operator to flip specific bits while leaving other bits as they are.

For example, you can switch the value of a bit with the following:

![](../figures/fig14.jpg)

Now the value of x is `0b11011111`.

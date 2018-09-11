# Functions

We’ll look at the following code to learn how to do functions in C and see what a C file should look like.

![](../figures/fig19.jpg)

First, include ```stdint.h```. See the section about header files.

In C the `main()` function returns an int. By default it returns `0`. All C projects must contain one and only one `main()` function.

The function `sum` takes in a ```uint8_t``` array and its size and returns a ```uint32_t```. In C you have to always pass the size of an array with the array.

The only difference with functions in C is that for the function to be recognized throughout the file it needs to have a function prototype as show above.

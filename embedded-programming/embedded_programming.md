# 2. Embedded Systems

## 2.1 Levels of Abstraction

## 2.2 Microcontrollers and Processors

## 2.3 The Physical Layer

## 2.4 C on Microcontrollers

### 2.4.1 Bitwise Operations

### 2.4.2 Registers

### 2.4.3 Memory and Data Management

### 2.4.4 Interrupts

_EXISTING WRITE-UP FROM SHIMI SMITH_









## Microcontrollers
A microcontroller is a processing unit, like the brain of a circuit.

### AVR/ATmega32M1
Our subsystem's components will be controlled by the ATmega32M1 microcontroller on the satellite, part of the AVR family of microcontrollers. It is programmed in the C language, and you will need to install the AVR software to compile and upload code to it (which we will help you to get set up).

![](./figures/avr.png)

### Arduino
Arduino is an open-source platform of microcontrollers, which we sometimes use to test components quickly. Since Arduino has more built-in code libraries, it is faster to write and upload code to test individual components than using the AVR microcontroller. Arduino is programmed using a slightly modified version of the C++ language.

![](./figures/arduino.jpg)




# Uploading Code (AVR)
Here is how to upload your code to the AVR ATmega32M1 microcontroller.

1. Follow the instructions at https://github.com/HeronMkII/coms-board to download and install the AVR software.

2. Download the CoolTerm application from http://freeware.the-meiers.org. This will be used to view transmissions from UART, a communication protocol to transmit log messages (from the print() function) from the board to the computer.

3. Get the programmer hardware device. Connect the 6-pin connector to the "Programming" header on the PCB, which is used to upload code. Connect the "RX" pin on the programmer to the "MOSI_A" pin on the PCB. RX refers to the "receive" pin, while MOSI_A refers to the "alternative" MOSI line, used for UART which is separate from SPI.

4. Turn on the power supply, set the output to 3.3V, and connect the power and ground lines to the "3V3" and "GND" header pins on the PCB.

5. See the instructions at https://github.com/HeronMkII/coms-board for finding the correct USB port and modifying the makefile (it might already be the correct one).

6. Open CoolTerm and modify the options to set the correct port (see https://github.com/HeronMkII/coms-board; the port for UART is the **opposite** of the port for uploading code). Click Connect.

7. Navigate to the folder for the local copy of the Git repository on your computer. Run ```make upload``` to compile the program and upload it to the board. Fix any compile-time errors if they occur.















# C Programming

In this document, we will provide a brief overview of the basics of the C programming language, putting emphasis on the most relevant areas. I will assume some previous programming experience and for certain topics universal to all programming language I will only provide an example of the C syntax with a brief explanation. In addition to this document I would recommend **“The C Programming Language”** by Brian Kernighan and Dennis Ritchie. **You can find a pdf of this book on the Google Drive in /Instrumentation/Literature.** This book describes all the features of C in great detail and is suitable for all levels of programming experience.


## So, what makes C different than other programming languages?
Well, one thing is that it’s not an object oriented programming language like Java or C++ or a whole lot of other programming languages. In addition to that, C is considered a relatively low level programming language since most C operations can be moderately easily translated into assembly. Because of this, C is a great choice for developing software that interfaces directly with hardware. Another unique part of C is pointers and dynamic memory allocation. C allows its programmers to have a generous amount of control over the computer’s memory.

Now, let’s get into C.

## Variables in C
C includes the following most common types: char, int, long, double and float. A char is always a byte but the size of the other variables is machine dependent. Due to the confusion caused by variable sizes on different machines, in our software we use the types defined in stdint.h. These include the following unsigned types: uint8_t, uint16_t, uint32_t and uint64_t and the following signed types int8_t, int16_t, int32_t and int64_t, where the numbers are the number of bits the variable holds. These types allow us to have better control over the sizes of our variables. So don’t use a 64 bit variable in a for loop that loops 10 times.

Here you can see how to initialize and set variables using binary, hex and decimal. The ``//`` before text is how you make a comment in C.

![](./figures/fig1.jpg)

You may have noticed no mention of strings or booleans, which are common types in several other languages. While a char represents a byte, it also can represent a character, hence the name char. So a string of characters is just an array of chars with a terminating character (```\0```) as the last element. ```char[] s = “Hello World”``` is a away to initialize a char array.

As for booleans we just use a char or uint8_t to represent them. 0 represents false and any other number is true. By convention we use 1 to represent true. So the statement ```5 == 5``` evaluates to 1 whereas ```5 != 5``` evaluates to 0. Here are the mathematical operators you can do in C:

> \* (multiplication)  
> / (division)  
> + (addition)  
> - (subtraction)  
> % (modulus)  

Here are the comparative operators:

> ```>``` (greater than)  
> ```>=``` (greater than or equal to)  
> ```<``` (less than)  
> ```<=``` (less than or equal to)  
> ```==``` (checks equality)  
> ```!=``` (checks inequality)

There are also the following logical operators:

> ```&&``` (and) - ```a && b``` is true if and only if a and b are true  
> ```||``` (or) - ```a || b``` is true if and only if at least one of a and b are true


There are also the following short forms for incrementing or decrementing numbers.

![](./figures/fig2.jpg)

In addition to ```a++``` and ```a--``` there is ```++a``` and ```--a``` which are indistinguishable without a context.

![](./figures/fig3.jpg)

In *example 1*, a is used first in the ```a == 5``` check and then is incremented, whereas in *example 2*, a is first incremented and then used in the ```a == 5``` check.

## Bitwise Operators
These operators directly manipulate the bits in numbers. A lot of our software is manipulating 8 bit registers on our microcontroller so bitwise operators are very common in our software. The first two are very similar to ```&&``` and ```||``` mentioned above. They are the “and” and “or” bitwise operators, represented by ```&``` and ```|```. They both combine two numbers into one. First I will explain the “and” operator.

This is how it is used.

![](./figures/fig4.jpg)

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

![](./figures/fig5.jpg)

There is another operator really similar to those two. It’s called the XOR (exclusive or) operator
and is represented as ```^```. If one and only one of the bits is a 1 then the result will have a 1 in that
position.

![](./figures/fig6.jpg)

All these operators can also be used as ```&=```, ```|=``` and ```^=```.

The next group of bitwise operators is bit shifts. There is left (```<<```) and right (```>>```) bit shifts. They do what the name suggests, shift the bits in a number.

![](./figures/fig7.jpg)

The bits are shifted to the left and 0’s are shifted in. You might think x is now ```0b111111110000```
but that is incorrect because that is no longer an 8 bit number. It is actually ```0b11111111```. With bit
shifts you have to consider the size of the variable because if you shift a number to the left past
its limit those bits will be cut off.

![](./figures/fig8.jpg)

When we shift this number by four, four of the leading zeros are being cut off so we end up with ```0b0000111111110000```, or more nicely ```0x0FF0```.

The right bit shift does the exact same thing except in the opposite direction. No matter the size
of the variable the bits will be cut off.

![](./figures/fig9.jpg)

In this case the bits are shifted out of the variable to the right and 0’s are shifting into the variable on the left. Here we will end up with x as ```0b00001111```.

There is one final bitwise operator called the not or compliment operator. It flips all the bits in the number so 1’s become 0’s and vice-versa. It is represented by the ```~``` symbol.

**Example 1:**

![](./figures/fig10.jpg)

In this example x becomes 0b01011100.

**Example 2:**

![](./figures/fig11.jpg)

In this example x becomes ```0b1111111100001111```. Don’t forget about those leading zeroes.

## Applications of Bitwise Operators
I mentioned that bitwise operators are very common in our software so now I will show you the most common uses in our software.

Very frequently we have to set bits in an 8 bit number to change settings in our microcontroller. We use bitwise operators to change certain bits while not affecting others.

This is how to write a certain bit to 1

![](./figures/fig12.jpg)

Now the value of x is ```0b00100000```.

And you can set a certain bit to 0 with the following,

![](./figures/fig13.jpg)

Now the value of x is 0b11011111

You can also switch the value of a bit with the following:

![](./figures/fig14.jpg)


## If statements in C
If statements in C look very similar to if statements in java

![](./figures/fig15.jpg)


## Switch statement
Switch statements are used quite often in our software.

![](./figures/fig16.jpg)


## While Loop

![](./figures/fig17.jpg)


## For Loop

![](./figures/fig18.jpg)

There are two useful commands for conditional statements and loops that are worth mentioning.
These commands exist in most other programming languages.

>**break** - exits the current statement or loop    
>**continue** - skips the current iteration of the loop and continues with the next

## Functions

In C functions are very similar to functions/methods in other programming languages. We’ll look at the following code to learn how to do functions in C and see what a C file should look like.

![](./figures/fig19.jpg)

First I included ```stdint.h```. I will go into header files a bit more later.

In C the main method returns an int. By default it returns 0. All C projects must contain one and only one main method.

The function sum takes in a ```uint8_t``` array and it’s size and returns a ```uint32_t```. In C you have to always pass the size of an array with the array.

The only difference with functions in C is that for the function to be recognized throughout the file it needs to have a function prototype as show above.

## Header Files
A header file that we have already seen is ```stdint.h```. The purpose of header files is to have definitions and declarations that can be shared amongst multiple files by including the header. By putting function prototypes in a header file and including it, you can call those functions in other files. For non-library header files make sure to include them using ```#include “file_name.h”``` whereas library headers are included with ```<file_name.h>```. Header files can also include other header files.

You can also put define and typedef statements in header flies. These statements that I will describe shortly, can also be put in the source file but for better organization tend to be put in a header file.

![](./figures/fig20.jpg)

The preprocessor will replace text in the source code based on the define statements. So every occurrence of foo in the source code will be replaced with bar.

The ```typedef``` statement shown above lets you use “boolean” as a type. Now the following is a valid statement.

![](./figures/fig21.jpg)


## Pointers
A pointer is a type of variable whose value is the memory address of another variable. This is how you declare and use a pointer.

![](./figures/fig22.jpg)


## Structs
Structs are a useful way of bunching multiple variables together. This is probably the closet C gets to objects. We tend to declare our structs in a header file using typedef for easier use.

![](./figures/fig23.jpg)


![](./figures/fig24.jpg)

I think this should be enough of a crash course on C to get you writing programs. There are more details on pointers and structs that I have left out. I would suggest the C book I mentioned earlier to learn more about those topics.

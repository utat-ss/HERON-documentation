# Formatting

## Spaces

All code is formatted with **spaces, not tabs**, using **4 spaces** to represent an indent.

If you are using Atom, this is done as follows: in the menu bar, select `Atom > Preferences`, select the `Editor` tab, and change the settings as follows:

![](../figures/atom-spaces-1.png)

![](../figures/atom-spaces-2.png)

![](../figures/atom-spaces-3.png)


## Naming

Functions should be named similar to a command or action, as close as reasonably possible to how you would describe an action in English.

```C
// bad, not as natural
void uart_init(void) {
}

// good, closer to English action
void init_uart(void) {
}
```

A function's name should give some indication of which library is comes from, generally by having the library's name (or a shortened form/acronym of it) in the name.

```C
// bad, don't know what kind of callback we are registering
// (could be UART, timer, CAN, etc.)
void register_callback(void) {
}

// good, we know  this is a UART callback
void register_uart_callback(void) {

}
```

Variables, functions, and types are named using the "lower snake case" convention, where all letters are lowercase and all words are separated with underscores.

```C
// bad
void dosomethingnow() {
}

// bad
void DoSomethingNow() {
}

// bad
void Do_Something_Now() {
}

// good
void do_something_now() {
}
```


Preprocessor macros (including constants) should be named with the "upper snake case" convention, where all letters are uppercase and all words are separated with underscores.

```C
// bad
#define adc_cs PB3

// bad
#define ADCCS PB3

// good
#define ADC_CS PB3
```

Struct and enum names should end with `_t` to represent a type.

```C
// bad
typedef struct {
    ...
} mob;
typedef enum {
    ...
} clk;

// good
typedef struct {
    ...
} mob_t;
typedef enum {
    ...
} clk_t;
```


## Operator Spacing

There should be a space on each side of a binary operator (operates on two values) or a ternary operator (operates on three values).

```C
// bad
a=b+c;
x=a?b:c;
if (thing1&&thing2) {
}
for (int i=0; i<5; i++) {
}

// good
a = b + c;
x = a ? b : c;
if (thing1 && thing2) {
}
for (int i = 0; i < 5; i++) {
}
```


For unary operators (operates on one value), there should be a space opposite the value but no space beside the value.

```C
// bad
if (! enable) {
    x = ~ x;
    i ++;
}

// good
if (!enable) {
    x = ~x;
    i++;
}
```


## Punctuation Spacing

There should be a space after commas and semicolons (except semicolons that end a line).

```C
// bad
my_func(a,b,c);
for (int i = 0;i < 5;i++) {
}

// good
my_func(a, b, c);
for (int i = 0; i < 5; i++) {
}
```


## Functions

A function should generally not be more than 40 lines long (including comments and whitespace). An easy check is that all of its code should be able to fit on your screen at once.


## Pointers

Pointers should be declared with `*` beside the type name, not beside the variable name.

```C
// bad
void my_func(uint8_t *ptr) {
}

// good
void my_func(uint8_t* ptr) {
}
```


## Parentheses

If statements, while loops, functions, structs, etc. should have the first curly brace on the same line as the declaration, not the following line. There should be a space between the closing parenthesis and the opening curly brace.

```C
// bad
int main(void)
{
    ...
}

//  bad
int main(void){
    ...
}

// good
int main(void) {
    ...
}
```

```C
// bad
if (condition){
    ...
}

// good
if (condition) {
    ...
}
```


## Switch Blocks

Do not nest switch blocks within each other; only one switch block at a time. This is because having nested switch blocks is hard to read and make it easy to forget a `break` statement.


## Comments

Every file should have a comment at the top with a high-level description of the code in the file. It should describe what the code does, and list the author(s) of the file. If the file contains code to control a particular component, it should give the part number, a link to its datasheet, and a list of important page numbers in the datasheet. It should also describe any assumptions or operational modes used.

Example:

```C
/*
MCP23S17 port expander (PEX)
Datasheet: http://ww1.microchip.com/downloads/en/DeviceDoc/20001952C.pdf

A port expander is a device with many GPIO (general purpose input/output) pins.
Each GPIO pin can function as either an input or an output, depending on what
you want to use it for. Using a port expander gives us more GPIO pins to work
with since we have a limited number on the 32M1 itself.
...
AUTHORS: Dylan Vogel, Shimi Smith, Bruno Almeida, Siddharth Mahendraker
*/
```

Every function should have a comment before it with a description of what it does. On separate lines, it should describe each of its parameters (including scientific units if applicable) and its return value (if applicable).

```C
// bad

uint8_t send_spi(uint8_t cmd) {
    ...
}


// good

/*
Transmits 8 bits of data while simultaneously receiving 8 bits of data over the SPI bus.
cmd - byte of data to transmit
Returns - byte of data received
*/
uint8_t send_spi(uint8_t cmd) {
    ...
}
```


Variables should generally having a comment describing what they represent. This is especially important for global variables or struct fields, but also useful for local variables within functions.

```C
// bad

queue_t rx_message_queue;


// good

// CAN messages received but not processed yet
queue_t rx_message_queue;
```

Comments should not explain things that are obvious from C syntax.

```C
// bad

// An integer representing the number of errors occurred
uint8_t num_errors = 5;


// good

// The number of errors occurred
uint8_t num_errors = 5;
```


## Scope

Declare variables at the smallest possible level of scope for their use. Do not repeat a variable name from a larger scope within a smaller scope. Even though this is valid C, it can lead to subtle bugs and confusion.

```C
// bad
uint8_t count = 0;
...
for (uint8_t i = 0; i < 5; i++) {
    uint8_t count = 0;
    ...
}


// good
uint8_t count = 0;
...
for (uint8_t i = 0; i < 5; i++) {
    uint8_t count_inner = 0;
    ...
}
```


## Whitespace

Only one line of code per line.

Code should generally be organized into logical blocks or sections with spaces between them.


## File Organization

For a pair of `.c` and `.h` files, `#include` statements should generally be in the `.h` file, except for the `.c` including its corresponding `.h` file.

Includes, constants, macros, and global variables should generally come first in a file.

Here is the general structure of a `.c` file, say it is called `file.c`:

```
introductory file/library comment block
#include "file.h" (corresponding header file)
global variables
functions (with comments)
```

Here is the general structure of a `.h` file, say it is called `file.h`:

```
#ifndef FILE_H
#define FILE_H

standard C library includes (e.g. <stdint.h>)
AVR library includes (e.g. <avr/interrupt.h>)
lib-common includes (e.g. <uart/uart.h>)
local file includes (same folder, e.g. "other_file.h")
#define constants
typedefs, struct definitions, enum definitions
function prototypes

#endif  // for FILE_H
```


## Magic Numbers

Try to avoid hard-coding constants such as conversion ratios and array sizes. These should be declared as easy to read constants using `#define`.

```C
// bad
uint8_t can_message[8];
for (uint8_t i = 0; i < 8; i++) {
    can_message[i] = ...
}

// good
#define CAN_MESSAGE_LENGTH 8
...
uint8_t can_message[CAN_MESSAGE_LENGTH];
for (uint8_t i = 0; i < CAN_MESSAGE_LENGTH; i++) {
    can_message[i] = ...
}
```


## Line Length

Lines should be no more than 80 characters wide. This is a common standard for text width in editor windows.


## Testing

When developing libraries of code, do not put temporary testing code in `main.c`. Any code that is for testing specific functions in a library should be written in a separate program in the `examples`, `harness_tests`, or `manual_tests` folders, separate from the main program.


## Manual tests

When developing manual tests, try to print messages during the test to indicate what the user should check to see if the test is successful. This makes it more obvious for the person running the test so they don't have to memorize what things to check for. For example, print "CHECK:" when the user should check some result.

For example,

```C
// ... code to set DAC output
print("CHECK: DAC pin 1 (VOUT_A) is 2.1V\n");

// ... other code to change DAC output
print("CHECK: DAC pin 1 (VOUT_A) is 0.8V\n");
```

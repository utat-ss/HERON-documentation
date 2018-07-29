# Formatting

## Spaces

All code is formatted with **spaces, not tabs**, using **4 spaces** to represent an indent.

If you are using Atom, this is done as follows: in the menu bar, select `Atom > Preferences`, select the `Editor` tab, and change the settings as follows:

![](../figures/atom-spaces-1.png)

![](../figures/atom-spaces-2.png)

![](../figures/atom-spaces-3.png)


## Naming

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

Struct names should end with `_t` to represent a type.

```C
// bad
typedef struct {
    ...
} mob;

// good
typedef struct {
    ...
} mob_t;
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

Functions that do not take any parameters should have `void` in the parentheses.

```C
// bad
int main() {
    ...
}

// good
int main(void) {
    ...
}
```

A function should generally not be more than 40 lines long (including comments and whitespace). An easy check is that all of its code should be able to fit on your screen at once.


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


## Comments

Every file should have a comment at the top with a high-level description of the code in the file. It should describe what the code does, and list the author(s) of the file. If the file contains code to control an electrical component, it should give the part number and list important page numbers in the datasheet.

Every function should have a comment before it with a high-level description of what it does, its parameters, and its return value (if applicable).

```C
// bad

uint8_t send_spi(uint8_t cmd) {
    ...
}


// good

/*
Transmits 8 bits of data while simultaneously receiving 8 bits of data over the SPI bus.
Transmits the provided data `cmd` and returns the received data.
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
int num_errors = 5;


// good

// The number of errors occurred
int num_errors = 5;
```


## Scope

Declare variables at the smallest possible level of scope for their use. Do not repeat a variable name from a larger scope within a smaller scope. Even though this is valid C, it can lead to subtle bugs and confusion.

```C
// bad
int count = 0;
...
for (int i = 0; i < 5; i++) {
    int count = 0;
    ...
}


// good
int count = 0;
...
for (int i = 0; i < 5; i++) {
    int count_inner = 0;
    ...
}
```


## Whitespace

Only one line of code per line.

Code should generally be organized into logical blocks or sections with spaces between them.


## File Organization

For a pair of `.c` and `.h` files, `#include` statements should generally be in the `.h` file, except for the `.c` including its corresponding `.h` file.

Includes, constants, macros, and global variables should generally come first in a file.


## Magic Numbers

Avoid hard-coding constants such as conversion ratios and array sizes. These should be declared as easy to read constants using `#define`.

```C
/// bad
uint8_t can_message[8];

/// good
#define CAN_MESSAGE_LENGTH 8
uint8_t can_message[CAN_MESSAGE_LENGTH];
```


## Line Length

Lines should generally be no more than 80 characters long. This is a common standard for text width in editor windows.


## Testing

When developing libraries of code, do not put temporary testing code in `main.c` and/or `main.h`. Any code that is for testing specific functions in a library should be written in a separate program in the `examples` or `tests` folder, separate from the main program.

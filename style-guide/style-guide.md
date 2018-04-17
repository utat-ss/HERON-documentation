# Programming Style Guide

This is a collection of the software programming style guide for the Heron Mk II codebase. Feel free to suggest revisions or new guidelines.


## Spaces

All code is formatted with **spaces, not tabs**, using **4 spaces** to represent an indent.

If you are using Atom, this is done as follows: in the menu bar, select `Atom > Preferences`, select the `Editor` tab, and change the settings as follows:

![](../figures/atom-spaces-1.png)

![](../figures/atom-spaces-2.png)

![](../figures/atom-spaces-3.png)


## Naming

Variables, functions, and types are named using the "snake case" convention, where all letters are lowercase and all "words" are separated with underscores.

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

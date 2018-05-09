# `.c` and `.h` Files

In our software repositories, you will often see pairs of files with the same name but different extensions (`.c` and `.h`). The `.c` file is often called the C file or implementation, while the `.h` file is often called the header file or interface.

`.c` files contain the implementation of the code, while `.h` files exist to provide interfaces that allow a file to access functions, global variables, and macros from other files.

When you `#include "file.h"` or `#include <folder/file.h>` in a file, it includes the header file in the current file so that you can call functions from `file.h`.

For functions, the `.c` file contains the entire function. You copy the corresponding **function prototype** to the `.h` file, which is just the first line containing the function name, return type, and parameters.


## Example

For example, say we have a function called `foo()` in a file called `test.c`.

```C
double foo(double a, double b) {
    return a + b;
}
```

In the corresponding header file, `test.h`, we put just the function prototype so other files can call this function.

```C
double foo(double a, double b);
```

Say we want to call this function from `main.c`.

```C
#include "test.h"

int main(void) {
    foo(1.1, 2.3);
    ...
}
```

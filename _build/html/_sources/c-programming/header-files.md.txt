# Header Files

The purpose of header files is to have definitions and declarations that can be shared amongst multiple files by including the header. By putting function prototypes in a header file and including it, you can call those functions in other files. Standard library headers (such as for C or AVR) are included with ```<file_name.h>```, such as ```stdint.h```. Non-library header files (such as the code we write) are included using ```#include “file_name.h”```. Header files can also include other header files.

The `#define` and `typedef` statements are commonly used in header files, but can be used in any file.

# #define

This tells the preprocessor to replace a particular piece of text with another piece of text before compiling the C program. It is commonly used to associate a readable name with a commonly used constant value.

```C
#define REGISTER_DEFAULT 0x4B
```

For example, we can use `#define` to clarify the use of a register's default value. Now whenever you put `REGISTER_DEFAULT` in your code, the compiler will substitute that with the value `0x4B` before compiling. This ensures constants are defined in one place and have a clear name.


# typedef

This creates a new type and associates it with some other existing type. This is commonly used to give a simpler and clearer name for a type for a particular use instead of writing a complex type many times.

```C
typedef volatile uint8_t* port_t;   // Don't need to worry about what `volatile` means
```

This example creates a new type `port_t` and associates it to mean the same type as `volatile uint8_t*`. This is a standard type to represent a port on the 32M1, so using the name `port_t` is more clear.

# Header Files

The purpose of header files is to have definitions and declarations that can be shared amongst multiple files by including the header. By putting function prototypes in a header file and including it, you can call those functions in other files. For non-library header files make sure to include them using ```#include “file_name.h”``` whereas library headers are included with ```<file_name.h>```, such as ```stdint.h```. Header files can also include other header files.

You can also put `#define` and `typedef` statements in header flies. These statements that I will describe shortly, can also be put in the source file but for better organization tend to be put in a header file.

![](../figures/fig20.jpg)

The preprocessor will replace text in the source code based on the define statements. So every occurrence of foo in the source code will be replaced with bar.

The ```typedef``` statement shown above lets you use “boolean” as a type. Now the following is a valid statement.

![](../figures/fig21.jpg)

# Debugging Programs Atmel-Studio

Using the Atmel ICE debugging board, we can step through and debug programs. Note that setting breakpoints and inspecting variables only works if you use the Atmel ICE board instead of the normal programmers.

You will need to install 'Atmel Studio' (Windows only):

**When programming, you must first setup and check everything is detected by going to 'Tools > Device Programming'. You must change the interface to use 'debugWire' instead of ISP.**

**When you are done with your debugging, always remember to disable debugWire mode by changing the interface to ISP (and clicking Apply). You should disable the debugWire and close by going to the 'debug' heading. If you do not disable debugWire, the board will not work with normal programmers.**

When prompted, toggle the power (remove and reinsert vcc) to set the fuses.

Some notes: You may need to run Atmel Studio as administrator to get programs to compile and build. If the program isn't running as expected, it may be due to compiler optmizations.

Create a new project for the program you want to debug. Currently, to include other header and source files, you will have to manually link each and every one. To do this, add each file to the project as a link (this includes all files in lib-common that your program uses):

![](../figures/atmel_studio_addfile1.PNG)

![](../figures/atmel_studio_addfile2.PNG)

Note that when using Atmel Studio, you need to create an Atmel Studio project that uses an auto-generated makefile instead of our own makefiles. This even applies to lib-common files - we tried compiling lib-common with our own makefiles, but this did not work since we could not set breakpoints or step into code in lib-common, likely due to differences in compiler settings from the Atmel Studio build system.

To run your program in debugWire mode with breakpoints, click the "Debug Program" button instead of the "Run Program" option. Note that you cannot change breakpoints while your program is running, so you need to set them before running or reupload your program.


## Breakpoints

Note that when setting breakpoints, it might not function exactly as you expect due to optimizations in the compilation of your program. For example, if you set a breakpoint on a line that modifies a variable whose value is never actually used for anything, the compiler might optimize it out, causing it to never hit that breakpoint or produce strange behaviour.

To be continued...

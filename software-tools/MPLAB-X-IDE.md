# Debugging Programs MPLAB X IDE

This is one of the software debugger tools available for Atmega and Atmel ICE on Mac OS.

## Installation
To install the IDE, visit [this website](https://www.microchip.com/mplab/mplab-x-ide). Make sure that your installed version is v5.05 or later.

Next, install compiler XC8 for Mac on [this website](https://www.microchip.com/mplab/compilers). When prompted in the installation process, set compiler settings to 'all users of this machine'.

## Project Creation
To make a new project, which is required to run code on MPLAB X IDE, follow the steps outlined below
1. Open MPLAB X IDE
2. Click 'New Project' in the menu bar
3. You then be allowed to set the settings of the project. The following settings should be selected for the project (ordered by when they are prompted):
    - Microchip embedded
    - Standalone project
    - Family: AVR MCUs
    - Device: ATmega64M1 or Atmega32M1 (depending on which microcontroller you are using)
    - Hardware Tools: Atmel-ICE
    - Compiler: XC8
4. Name your project (you can choose the name)

Your newly created project will be initialized with a couple of folders. The header folder is where the header/.h files are found. The source folder has the src/.c files. The important files contain the makefile for the XC8 compiler (you shouldn't have to change this folder/file).

On the menu, the hammer icon builds your project (equivalent of make). The downward arrow onto a microcontroller icon build and uploads your project (equivalent of make upload). And the red square on a white sheet with a green arrow is the debug icon.

Side-note: If you have multiple projects, only the main project (bolded) will be uploaded. You can switch main projects by right clicking the project you want to switch to, and clicking ‘Set as Main Project’

## Adding Files
The UTAT software files on git need to be manually added. To add a file:
1. Right click on the folder you want to add a file to
2. Click 'add existing file' on the drop-down menu
3. Set store path as: 'Absolute'
4. Find file within your computer
5. Set file format (.c/.h etc..)
6. Click 'Select'

Note: - Add '#include <xc.h>' and '#include <stdint.h>' somewhere all files can access (such as a header file)

If you receive an error about the IDE not finding header files, perform the following steps:
1. Click 'File' on the IDE top menu
2. Click 'Project Properties'
3. Under Conf: [default]
4. Select your compiler (XC8 Compiler)
5. In 'Option categories' dropdown menu choose 'Preprocessing and messages'
6. Click '...' beside 'Include directories'
7. Click 'Browse...'
8. Find and select the location of the include. For example, if the include is <uart/uart.h>, whose directory path is lib-common->includes->uart->uart.h. You would include the lib-common/includes path

## Debugging
To debug, you must enable the DeBug Wire:
1. Under 'Project Properties', Conf: [default], Atmel-ICE
2. Set 'Option categories' to 'communication'
3. Change interface to 'debugWIRE'
4. Run your program. It will give you a message to configure to debugWIRE, click agree

Remember to disable the DeBug Wire to allow for normal programming when you are finished. To disable the DeBug Wire, do the opposite of the above steps (change debugWIRE to ISP and run)

### Print statements
Note that the XC8 compiler has trouble compiling our print statement function. Therefore, you can comment out all print statements (go to atom, Command F all print statements and comment them out with //). If you require print statements, declare another character array variable and set it to what you want to print out (char printVar[] = "print this out"). Then in the print function, pass in that variable (printVar).

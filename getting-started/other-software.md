# Other Software

Besides the AVR toolchain, there is other software we use to design the satellite's software.


## Git and GitHub

Git is a version control system that allows teams of people to track changes and collaborate on the same codebase. It provides mechanisms for tracking versions of code, so you can see previous iterations and revert back if necessary.

GitHub is a platform build on top of the Git version control system that provides a web interface for viewing and managing repositories (projects) and code. It also provides a desktop application called GitHub Desktop that makes it easier to view and modify code on your local computer.


## Make

As you will later see in the Hello World tutorial, compiling and uploading a program to the 32M1 requires several complex commands. Instead of having to type these every time, we use a tool called Make to automate these commands.


## Terminal Program (usually CoolTerm)

We use a communication protocol called UART to allow us to view log messages from the 32M1 on a laptop. You need a terminal program that receives and displays these messages.

We recommend using CoolTerm, but other options such as TeraTerm or Xterm are available.

 The following settings will probably work:
- Baud Rate: 9600
- Data: 8 bit
- Parity: None
- Stop: 1 bit
- Flow control: none

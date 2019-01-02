# Test Harness

The **test harness** is a program we use to automate testing parts of our code. It
takes a folder of test programs, uploads each program to the microcontroller,
runs it, observes its output, determines whether the tests passed or failed, and
prints the results.

Each **test suite** contains a set of tests, which contain assertion statements.
An **assertion** is a condition we expect to be true at a certain time if the
program is working correctly. If all assertions succeed, the program is working
correctly. If any assertion fails, we know the program is not working correctly
and can isolate the problem.

The test harness is written in Python and requires the `pyserial` library to be
installed.


## Running the Test Harness

The test harness Python script is located in `lib-common/bin/harness.py`.

The command to run the test harness is:

`$ python harness.py -p <port> -d tests`

**IMPORTANT NOTES:**

**You must replace** `<port>` with the port the programmer is connected to (what you normally use to upload programs), for example `$ python harness.py -p /dev/tty.usbmodem00208212 -d tests` (Mac) or `$ python harness.py -p COM3 -d tests` (Windows).

**You may have to replace** `harness.py` with the path to the Python script, such as `bin/harness.py` if you are running from the `lib-common` folder.

**You may have to replace** `tests` with the path to the folder of your test files.

**If you are running tests that use 2 boards** (e.g. CAN), you must specify a second port with a space after the first port, e.g. `$ python harness.py -p /dev/tty.usbmodem00208212 /dev/tty.usbmodem00187462 -d tests`.


## Useful Links

[Pull request for test harness development](https://github.com/HeronMkII/lib-common/pull/28) for more details.

[Available assertion commnads](https://github.com/HeronMkII/lib-common/blob/master/include/test/test.h)

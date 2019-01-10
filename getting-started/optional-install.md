# Optional Software installation

This page contains miscellaneous software that is not essential, but that you may want to install sometime later when you need it.

## Pololu USB AVR Programmer v2

This utility is for changing the settings of a hardware programmer device.

Download and install the Pololu USB AVR Programmer v2 for your operating system [here](https://www.pololu.com/product/3170/resources). If you're using the older programmer, this might not work.

## Python and Pyserial

These are needed to run the test harness, our automated software testing framework.

We are only supporting Python 3 with the test harness, not Python 2. Open your command line and type `$ python -V` to check the version.

If you don't have Python at all or need Python 3, download it from [here](https://www.python.org/downloads/). Get the latest version (v3.7.1 as of Nov. 28, 2018) and install it.

If you are on macOS, make sure to follow the instructions on the last page of the installer to install certificates.

If you are on Windows, make sure to check the box called "Add Python 3.7 to PATH".

Run `$ python -V` to check that you have Python 3. If you are on macOS, it probably distinguishes the versions using `python` for Python 2 and `python3` for Python 3. You can try running `$ python3 -V` to see what happens. If that works, use `python3` instead of `python` from now on so that you use Python 3.

Now you will need to install the Pyserial library, which the test harness requires to interface with serial ports. We will do this using the pip package manager.

`$ pip install pyserial`

If you are on macOS, you might need to use `pip3` instead of `pip`.

If this doesn't work, you might need to follow the instructions in the terminal to upgrade the version of pip on your computer.

Please ask for help if you encounter any problems.

## SpeedCrunch

This app is useful for quickly converting numbers between decimal, hexadecimal, and binary.

Download and install [here](https://speedcrunch.org/download.html).

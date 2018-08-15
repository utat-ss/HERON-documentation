Welcome to the Heron MkII CubeSatellite Documentation
=====================================================

Heron MkII is a 3U cubesatellite being developed by the University of Toronto Aerospace Team (UTAT), Space Systems division (SS). It's primary mission objective is to study the effects of microgravity on Candida Albicans, a type of yeast found in human gut flora. 

All of the code and some of the hardware design for our project can be found on the `UTAT Space Systems`_ GitHub. If you're currently on the GitHub, our documentation can also be viewed on `Read the Docs`_.

.. _UTAT Space Systems: https://github.com/HeronMkII
.. Update this link as needed
.. _Read the Docs: https://utat-space-systems-software-documentation.readthedocs.io/en/latest/

This documentation covers the software used on our satellite and some useful getting started information for new members. It has been created with the following goals in mind:

- To enable new UTAT SS members to quickly get up to speed and begin contributing to the codebase
- To document the functionality of the various libraries we develop
- To contain information regarding build instructions and debugging on our custom hardware

Our documentation is organized into the following sections:

- :ref:`getting-started`
- :ref:`software-tools`
- :ref:`troubleshooting`
- :ref:`embedded-systems`
- :ref:`c-programming`
- :ref:`communication-protocols`
- :ref:`software-style-guide`
- :ref:`electrical-systems`
- :ref:`reading-datasheets`

.. _getting-started:
.. toctree::
	:maxdepth: 2
	:caption: Getting Started

	getting-started/rtsys
	getting-started/avr-toolchain
	getting-started/install
	getting-started/install-mac
	getting-started/install-win
	getting-started/install-linux
	getting-started/atmega32m1
	getting-started/hello-world
	getting-started/git-and-github
	
.. _software-tools:
.. toctree::
	:maxdepth: 2
	:caption: Software Tools
	
	software-tools/software-workflow
	software-tools/test-harness
	software-tools/our_toolchain
	software-tools/lib-common
	software-tools/uart-terminal
	
.. _troubleshooting:
.. toctree::
	:maxdepth: 2
	:caption: Troubleshooting
	
	troubleshooting/troubleshooting
	
.. _embedded-systems:
.. toctree::
	:maxdepth: 2
	:caption: Embedded Systems
	
	embedded/microcontrollers
	embedded/registers
	embedded/interrupts
	embedded/avr-fuses
	
.. _c-programming:
.. toctree::
	:maxdepth: 2
	:caption: C Programming
	
	c-programming/introduction
	c-programming/variables
	c-programming/operators
	c-programming/bitwise-operators
	c-programming/bitwise-operators-applications
	c-programming/control-structures
	c-programming/functions
	c-programming/header-files
	c-programming/pointers
	c-programming/structs
	c-programming/c_h_files
	c-programming/binary-hex-literals
	c-programming/integer-types
	c-programming/print-formatting
	c-programming/volatile-variables
	
	
.. _communication-protocols:
.. toctree::
	:maxdepth: 2
	:caption: Communication Protocols
	
	communication-protocols/communication-protocols
	communication-protocols/can
	communication-protocols/can-mobs
	communication-protocols/can-mob-allocation
	communication-protocols/spi
	communication-protocols/spi-bus
	communication-protocols/spi-using-spi
	communication-protocols/uart
	
.. _software-style-guide:
.. toctree::
	:maxdepth: 2
	:caption: Software Style Guide
	
	style-guide/style-guide
	style-guide/technical
	style-guide/formatting
	
.. _electrical-systems:
.. toctree::
	:maxdepth: 2
	:caption: Electrical Systems
	
	electrical/websites
	electrical/components
	electrical/equipment
	electrical/circuit-platforms
	electrical/pcb-design
	
.. _reading-datasheets:
.. toctree::
	:maxdepth: 2
	:caption: Reading Datasheets
	
	datasheets/datasheets
	

If you would like to contribute to our documentation, create a branch and start a pull request. If you would like to report an error, please `submit an issue`_.

.. _submit an issue: https://github.com/HeronMkII/documentation/issues 

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

	getting-started/atmega32m1
	getting-started/avr-toolchain
	getting-started/command-line
	
.. _software-tools:
.. toctree::
	:maxdepth: 2
	:caption: Software Tools
	
	software-tools/lib-common
	
.. _troubleshooting:
.. toctree::
	:maxdepth: 2
	:caption: Troubleshooting
	
	troubleshooting/troubleshooting
	
.. _embedded-systems:
.. toctree::
	:maxdepth: 2
	:caption: Embedded Systems
	
	embedded/avr-fuses
	
.. _c-programming:
.. toctree::
	:maxdepth: 2
	:caption: C Programming
	
	c-programming/binary-hex-literals
	
.. _communication-protocols:
.. toctree::
	:maxdepth: 2
	:caption: Communication Protocols
	
	communication-protocols/can
	
.. _software-style-guide:
.. toctree::
	:maxdepth: 2
	:caption: Software Style Guide
	
	style-guide/formatting
	
.. _electrical-systems:
.. toctree::
	:maxdepth: 2
	:caption: Electrical Systems
	
	electrical/circuit-platforms
	
.. _reading-datasheets:
.. toctree::
	:maxdepth: 2
	:caption: Reading Datasheets
	
.. Insert guidelines on contributing, adding bug reports

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

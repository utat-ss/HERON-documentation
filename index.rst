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
- :ref:`software-and-programming`
- :ref:`electronics-and-altium`

.. _getting-started:
.. toctree::
	:maxdepth: 2
	:caption: Getting Started

	getting-started/getting-started
	useful-websites/useful-websites


.. _software-and-programming:
.. toctree::
	:maxdepth: 2
	:caption: Software and Programming

	software-tools/software-tools
	troubleshooting/troubleshooting
	embedded/embedded-systems
	communication-protocols/communication-protocols
	c-programming/c-programming
	style-guide/style-guide
	software-testing/software-testing


.. _electronics-and-altium:
.. toctree::
	:maxdepth: 2
	:caption: Electronics and Altium

	altium/altium
	electrical/electrical-systems
	datasheets/reading-datasheets
	elec-style-guide/elec-style-guide

.. _it:
.. toctree::
	:maxdepth: 2
	:caption: IT

	remote-access/remote-access

If you would like to contribute to our documentation, create a branch and start a pull request. If you would like to report an error, please `submit an issue`_.

.. _submit an issue: https://github.com/HeronMkII/documentation/issues

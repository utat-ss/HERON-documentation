Print Formatting
================

`C printf() formatting <http://www.cplusplus.com/reference/cstdio/printf/>`_

The UART ``print()`` function behaves almost identically to the standard C ``printf()`` function. This gives a powerful way to format values as strings for output.

**Using `%f` or `%lf` requires a special compiler flag to be enabled. See `lib-common/src/uart/log.c` about this, which is enabled when compiling testing programs but not for main programs.**

AVR's ``printf()`` doesn't support printing 64-bit integers.

Here are the format specifiers for common data types:

.. list-table::
    :header-rows: 1

    * - Data Type(s)
      - Format Specifier(s)
    * - ``uint8_t``, ``uint16_t``
      - ``%u`` (Unsigned)
    * - ``uint32_t``
      - ``%lu`` (Long Unsigned)
    * - ``int8_t``, ``int16_t``
      - ``%d`` (Decimal - Signed)
    * - ``int32_t``
      - ``%ld`` (Long Decimal - Signed)
    * - ``uint8_t``, ``uint16_t``, ``int8_t``, ``int16_t``
      - ``%x`` (Hexadecimal - Lowercase), ``%X`` (Hexadecimal - Uppercase)
    * - ``uint32_t``, ``int32_t``
      - ``%lx`` (Long Hexadecimal - Lowercase), ``%lX`` (Long Hexadecimal - Uppercase)
    * - ``float``, ``double``
      - ``%f`` (Float)


Integer Formatting
------------------

An example of controlling integer formatting output as hex:

.. code-block:: C

    print("0x%02x", value);
    // lowercase hex, always with 2 hex digits (zero padded if necessary)
    // add "0x" at the beginning to indicate to the reader that the output is in hex

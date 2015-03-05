===============================
 HiPack Specification v1 (WIP)
===============================

:Authors: Adrián Pérez de Castro <aperez@igalia.com>

.. contents::


Types
=====

* *Integer* represents an integer number.
* *Float* represents a floating point number.
* *Bool* represents a ``true`` or ``false`` value.
* *String* represents a string value.
* *List* represents a sequence of values.
* *Dictionary* represents a set of values, each labelled with a *Key*.


Integer
-------
Integral numbers are signed integers with 32 bits of precision. Numbers can be
formatted as decimal, hexadecimal, or octal:

* ``0x``, or ``0X`` denote the beginning of an hexadecimal number.
* ``0`` (without a following ``x``) denote the beginning of an octal number.
* Otherwise, the number is decimal.

The first character of a number may specify its sign, which can be either
``+``, or ``-``.


Float
-----
Floating-point numbers can be formatted both in dotted notation (e.g.
``-12.4``), or using scientific notation (e.g. ``+2.3e-5``). Additionally,
the following values are recognized, disregarding case:

* ``NaN`` (Not-a-number).
* ``Inf`` (infinity).
* ``Infinity`` (infinity).


Bool
----
There are only two possible values, which can optionally be written with
their first letter in caps:

* ``True`` / ``true``
* ``False`` / ``false``


String
------
Strings are delimited by double-quote characters (``"``).

The backslash (``\``) character can be used to escape a number of characters
that otherwise could be difficult to represent:

======== ===== ==================
Sequence ASCII Character
-------- ----- ------------------
``\0``   0x00  Null
``\t``   0x09  Horizontal tab
``\n``   0x0A  New line
``\r``   0x0D  Carriage return
======== ===== ==================

Any other character that follows a backslash represents itself. This allows
to embed backslash, and double-quote characters themselves into strings.


Encoding
========

All HiPack messages *must* be encoded as UTF-8.

* Message structure markers, number values, and boolean values, use exclusively
  ASCII characters. In particular:

  - Delimiters are the ASCII characters ``:``, ``{``, ``}``, ``[``, ``]``,
    ``,``, ``"``, and ``#``.

  - Valid white space are the following ASCII characters:

    - 0x09: Horizontal tab, ``\t``.
    - 0x0A: New line, ``\n``.
    - 0x0D: Carriage return, ``\r``.
    - 0x20: Space.

* Dictionary keys, and string values may contain any valid UTF-8 character
  sequence.

* Comment text between an octothorpe (``#``) marker and the end of line marker
  (``\n``) may contain any valid UTF-8 character sequence.



Grammar
=======

The syntax for the basic value types is specified in their corresponding
sections: Integer_, Float_, Bool_, String_.

::

  Value = Integer
        | Float
        | Bool
        | String
        | List
        | Dict

  Whitespace = '\t' | '\n' | '\r' | ' '

  ItemSeparator = ','
                | Whitespace

  ListItem = ItemSeparator Value

  List = '[' ']'
       | '[' Value ListItem* ItemSeparator? ']'

  KeyValuePair = Key ':'? Value

  DictItem = ItemSeparator KeyValuePair

  Dict = '{' KeyValuePair DictItem* ItemSeparator? '}'

  Message = Dict
          | KeyValuePair*


Note that comments are not specified in the grammar above does not include
comments for the sake of simplicity. Comments can appear anywhere except
inside strings, and they span from the octothorpe sign (``#``) to the end of
the line.


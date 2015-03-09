======================
 HiPack Specification
======================

:Version: 1
:Status: **Release Candidate**
:Authors: Adrián Pérez de Castro <aperez@igalia.com>,
          Andrés J. Díaz López <ajdiaz@connectical.com>,
          Óscar García Amor <ogarcia@connectical.com>
:Date: 2015/03/05

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


Key
---
Dictionary keys are sequences of unquoted characters that satisfy the
following:

* The character ``:`` is not in the sequence.
* The sequence does not contain any whitespace characters.
* Non-ASCII characters are allowed, and they *must* be encoded as UTF-8.

For more details on the meaning of *whitespace*, check the Encoding_ section.


String
------
Strings are delimited by double-quote characters (``"``).

It is possible to introduce arbitrary bytes in a string by escaping its
hexadecimal value with a backslash, followed by two hexadecimal digits:
``\NN`` represents the byte with value ``0xNN``. This mechanism allows
encoding arbitrary data inside strings.

For convenience, the following shorthand backslash escape sequences are
defined:

======== ========= ===== ==================
Escape   Shorthand Value Character
-------- --------- ----- ------------------
``\09``  ``\t``    0x09  Horizontal tab
``\0A``  ``\n``    0x0A  New line
``\0D``  ``\r``    0x0D  Carriage return
``\22``  ``\"``    0x22  Double quote
``\5C``  ``\\``    0x5C  Backslash
======== ========= ===== ==================

Note that strings may contain any arbitrary data. It is *recommended* to
use hexadecimal escapes to encode non-printable characters (both ASCII
and Unicode), or characters that may not be represented correctly by other
means. Specially when messages are intended to be used by humans.


Comments
========

Comments can appear anywhere except inside strings, and they span from an
octothorpe sign (``#``) to the next carriage return character (``\n``,
ASCII code ``0x0D``).

Note that the grammar_ does not include comments because they are an optional
construct that is intended to be removed by the lexer, before input reaches
the parser. This means that comments are “transparent”: a HiPack message with
its comments removed is always equivalent to itself.

The motivation of allowing for simple, line-based comments is reusing the
HiPack format for configuration files. When used for this purpose, messages
(read: configuration files) are likely to be written, and read by humans, so
they are likely to be formatted with proper indentation, where line-based
comments blend naturally.

Comments effectively prevent formatting messages in a more compact form
without carriage returns and unneeded whitespace. This choice has been made
on purpose to *discourage* using comments in this case, where messages are
expected to be as compact as possible. On the other hand, in those cases
where comments are used, it is more likely that humans would read the
messages, in which case it would be desirable to have a more readable
formatting anyway.


Encoding
========

All HiPack messages *must* be encoded as UTF-8:

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



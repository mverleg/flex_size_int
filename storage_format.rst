
Storage format
===============================

Signed
-------------------------------

Integers are stored in blocks of bytes (8 bits each), as follows:

* The first bit of every byte indicates whether there is a next byte, ``0`` being yes and ``1`` being no.
* All bytes up to and including the first one that starts with ``1`` are concatenated except their first used bit.
* Like standard integers, the first data bit indicates positive (``0``) or negative (``1``).
* The data bits after the sign are ordered from most to least significant, representing the absolute value (this is unlike the typical two-complements storage method, which is less logical in this case because the range is flexible).
* Up to 7 bits of the first byte may be used for other data. The least significant bits are used (for performance and simplicity of implementation reasons) for the other data and the most significant for the the flexible integer.
    E.g. if the prior data is 11 bits ``XXXX XXXX XXX``, there could be 13 for the flexible int (``YYYY YYYY YYYY Y``), which are combined as ``XXXX XXXX | YYYY Y XXX | YYYY YYYY``.
* Storage is big-endian.
* Byte ``11000000`` is the only invalid value (since ``-0`` is the same as ``+0``).

Unsigned
-------------------------------

Unsigned integers are stored the same way, except that the sign bit is used for data. There is no invalid value.

Examples
-------------------------------

* **A 0 <= integer < 64 (without shift)**

An integer such as ``25`` has binary encoding ``11001`` for the value. The sign is ``+`` so ``0``. We have 6 bits, so only one block of 7 bits is needed: set the first bit to ``1``.
This results in ``1`` (last byte) + ``0`` (sign) + ``0`` (padding) + ``11001`` (25).

* **A 64 <= integer < 128**

An integer such as ``115`` has binary encoding ``1110011`` for the value. The sign is ``+`` so ``0``. We have 8 bits, so two blocks of 7 bits are needed: set the first bit of the first byte to ``0`` and the first bit of the second byte ``1``.
This results in ``0`` (there are two bytes) + ``0`` (sign) + ``000000`` (padding) for the first byte, and ``1`` (last byte) + ``1110011`` (data) for the last byte.

* **An integer > long**

An integer such as ``92_233_720_368_547_758_079_418`` has binary encoding ``110_0001101_0100000_0000000_0010001_1010001_1101001_0000011_0010110_0011100_0001010_1001010`` for the value. The sign is ``+`` so ``0``. We have 81 bits, so 12 blocks of 7 bits are needed: set the first bit of all bytes to ``0`` except the first bit of the last byte, which is ``1``.
This results in ``10000110_00001101_00100000_00000000_00010001_01010001_01101001_00000011_00010110_00011100_00001010_11001010``.

* **An integer < -128**

An integer such as ``-413177`` has binary encoding ``110_01001101_11111001`` for the absolute value. The sign is ``-`` so ``1``. We have 20 bits, so three blocks of 7 bits are needed: set the first bit of the first and second byte to ``0`` and the first bit of the third byte ``1``.
This results in ``0`` (there are two bytes) + ``1`` (sign) + ``0`` (padding) + ``11001`` data for the first byte, ``0`` (not last byte) + ``0011011`` (data) for the second byte, and and ``1`` (last byte) + ``1111001`` (data) for the last byte (so ``01011001_00011011_11111001``).



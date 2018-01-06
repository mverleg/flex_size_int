
Flex size int
===============================

This package encodes integers into a flexible-sized binary format. This is useful for compressed storage.

The number of bytes grows as needed to support larger numbers. Small numbers take one byte, but numbers can grow as large as desired without losing precision.

This is only for storing or transmitting numbers. It is not possible to do arithmetic in this format (it would be really slow).

Status
-------------------------------

**This project is under development and is not yet ready to be used!**

Installation
-------------------------------

To use this library, please follow the steps for the platform(s) you use:

* JVM_ (Kotlin/Java/Scala/...)
* Binary_ (Rust/C/C++/C#/...)
* Python_

Advantages
-------------------------------

Compression
+++++++++++++++++++++++++++++++

Since the number of bytes grows or shrinks as necessary, in most cases it takes less space to store numbers in this format than it does to store them directly.

Of course, a little metadata is needed to handle the size, so if your numbers are close to 32 or 64 bits (or whatever storage format you'd otherwise use), you will probably need a bit more space in this flexible format.

This package is useful for storing integer numbers where a good portion of them are smallish (10000 is small), or when some are too big for a fixed-size format.

No overflows
+++++++++++++++++++++++++++++++

The numbers can theoretically grow indefinitely, taking more and more bytes and they get larger. They will never overflow.

In reality, they have to eventually be converted back to a supported computer memory format to be useful, so some care is still needed to avoid overflows.

Disadvantages
-------------------------------

Read/write speed
+++++++++++++++++++++++++++++++

Since some logic is needed to convert to and from this format, it will be slower than normal fixed-sized integers.

For storage to dish or transport over network, I suspect that the performance will likely still be IO-bound.

Unsized / not seekable
+++++++++++++++++++++++++++++++

Since the size of the stored integers is only known after reading them, this format is not convenient for seekable storage formats.

For example, if a hundred integers are stored adjacently in standard 32-bit format, the 37th number starts at the 145th byte. If they are stored in flexible format, you'll need to read the first 36 to find where the 37th is.

Precision takes space
+++++++++++++++++++++++++++++++

This is rather inevitable in any format, but worth mentioning anyway. It takes much less space to store large integers in a float64, which is not precise for high integers (except for the few that are exactly representable). For example, the highest float64 value is around ``1.8*10^308``. In different formats:

* **Float64**: ``1.8*10^308`` is stored in 8 bytes; it's an exact integer, but ``1.8*10^308 - 1`` and ````1.8*10^308 + 1`` are not.
* **Signed Integer64**: ``1.8*10^308`` overflows massively since the maximum value is about ``9.22*10^18``.
* **Unsigned Integer64**: This still overflows massively, the maximum is only twice as large.
* **Integer1024**: in this theoretical format, ``1.8*10^308`` could be represented exactly using 128 bytes (but the format isn't really available). Not that the number ``3`` also takes 128 bytes in this format.
* **Flexible sized integer**: with this package, ``1.8*10^308`` could be represented exactly using 147 bytes. This is more than Integer1024, but:

    1. This format actually exists.
    2. The number ``3`` only takes one byte instead of 128.

Features
-------------------------------

Signed integers
+++++++++++++++++++++++++++++++

Normal signed integers are supported without any upper or lower bound and without loss of precision.

There is even a special value that could be used as ``NaN`` or ``-0``, although whether that's a good idea is debatable.

Unsigned integers
+++++++++++++++++++++++++++++++

If numbers are known to be non-negative, you can save one bit by using the unsigned format, which has no upper bound.

This is for example useful to encode lengths of vectors or other collections, which are often small and always positive.

There is no special ``NaN`` value for unsigned integers.

Adding extra data
+++++++++++++++++++++++++++++++

For even more compression, it is possible to add a fixed number of bits as extra data.

For example, if you are storing a 3-bit identifier followed by a small integer, 3 bits of a byte can be used for the identifier, and the other 5 bits can be used for the integer. If the integer is too large, it will just continue using the next byte in addition to the 5 bits.

Multi-platform
+++++++++++++++++++++++++++++++

This is not actually done yet, but the plan is to support multiple compatible versions:

- Kotlin_ (callable from JVM languages like Kotlin, Java, Scala)
- Rust_ (callable from Rust, C, C++, C#, Julia...)
- Python_ (callable from, well, Python)

This means you can save a file using flexible integers in Java and read it in C++, for example.

How does it work?
-------------------------------

For info about the storage format, see `the format description`_.

Usage & contributions
---------------------------------------

Code is under `Revised BSD License`_ so you can use it for most purposes including commercially.

After the code reaches a functional stage in Python, contributions are very welcome!

Tests
---------------------------------------

The project has good automated test coverage. Tests are run automatically for commits to the repository for all supported versions. This is the status:

.. image:: https://travis-ci.org/mverleg/flex_size_int.svg?branch=master
	:target: https://travis-ci.org/mverleg/flex_size_int


.. _`the format description`: https://github.com/mverleg/flex_size_int/blob/master/storage_format.rst
.. _`Revised BSD License`: https://github.com/mverleg/flex_size_int/blob/master/LICENSE.rst
.. _JVM: https://github.com/mverleg/flex_size_int/blob/master/kotlin/
.. _Kotlin: https://github.com/mverleg/flex_size_int/blob/master/kotlin/
.. _Binary: https://github.com/mverleg/flex_size_int/blob/master/rust/
.. _Rust: https://github.com/mverleg/flex_size_int/blob/master/rust/
.. _Python: https://github.com/mverleg/flex_size_int/blob/master/python/



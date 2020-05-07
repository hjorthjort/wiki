The basic idea of floating point numbers is to let some of the bits of a word
represent the significant digits (the significand, or mantissa), and some the "point", the
exponent. In scientific notation, a number like 33.314 would be `3.3314e2`.
Floating point stores the `3.3314` in one part of the word, and `2` in the
other.

IEEE 754
========

The one, true standard for floating point numbers.

Based on section 2 of this paper:
<https://hal.archives-ouvertes.fr/file/index/docid/576641/filename/computing-midpoint.pdf>

Assumptions may be tested here: <https://www.h-schmidt.net/FloatConverter/IEEE754.html>

- Single precision (32 bits): 23 bits mantissa, 8  bits exponent, 1 bit sign.
- Double precision (64 bits): 52 bits mantissa, 11 bits exponent, 1 bit sign.
- Other word lengths are permitted by the standard, which only specifies how to
  represent different numbers with whatever number of bits you have allocated
  for the mantissa end exponent.

I will use the *tiny* format specified in the article, which uses 3 bits each
for mantissa and exponent. The layout of the numbers in my examples will be

```
[ b6  | b5 b4 b3 | b2 b1 b0    ]
 sign   exponent   mantissa
  _s_   _e_        _f_
```

Sign
----

Sign is straightforward: 0 means non-negative, 1 means negative.

Exponent
--------

There are 2^{|e|} possible exponent values. The highest (all 1's) and the lowest
(all 0's) are reserved.

The exponent `011...1` is interpreted as 2^0. Consider `011...1` to be 0, and
just count up and down as usual from there. It is not like 2s complement (but it
may be like 2s complement with the sign flipped, I think). In *tiny*, `011` is
0, `010` is -1, `001` is -2, and `000` is reseverved (but would also be used to
represent the 2^{-2} exponent). `100` is 1, `101` is 2, and `110` is 3, and
`111` is reseverved. In mathematical notation, if |e| is the number of exponent
bits and e is interpreted as an unsigned integer, the exponent's actual value
becomes

E = e - 2^{|e| - 1} - 1

See the "special numbers" section for what happens at the low and high end of
the exponent.

Mantissa
--------

Sometimes called significand, at least when interpreted.

Holds the significant digits. Usually, the mantissa bits `011` would be
interpreted as 1.011 (in what is called normal numbers). Only for subnormal
numbers is it interpreted 0.011.

Normal and subnormal numbers
----------------------------

How do we represent the number 0.011 (binary)? It could be represented in many
ways in say, single-precision, because we can choose to shift the significant
digits and change the exponent.

0.011 = 0.011 * 2^0 = 0.11 * 2^-1 = 1.1*2^-2 = 11 * 2^-3

for example.

We call this one 1.1*2^-2 the *normal* number. It has a 1 in front of the point.
Since most numbers are represented this way, we actually drop the leading 1.
if we need to lead with a 0 (for *subnormal* numbers, that is inferred from
the exponent being 0).

So the normal representation becomes 1.1*2^-2, but without the leading 1, so we
store the value 1 in the mantissa and the value -2 in the exponent.

```
[ 0 | 0 0 1 | 0 0 1 ]
```

If we divide this number by two, we get 1.1*2^-3 = 0.11*2^-2. In the *tiny*
format, the smallest exponent is -2, so we end up with either having to round
all the way to 0, or to somehow deal with not having a leading 1 implicit in the
significand. We choose the latter. If we didn't, we would end up with a big gap
around 0, because we wouldn't be able to represent anything smaller than
`0|000|001` which is 2^-2.

So if the exponent is all 0s, `0...0`, then we interpret the actual exponent to
be the minimal value (-2 in *tiny*), and then interpret the mantissa as being a
significand with a 0 before the point. This is a *subnormal number*. Now, the smallest positive number is
`0|001|000` which is 0.001 * 2^-2 = 0.00001. Much closer to 0!

Special numbers
---------------

0 can be represented by all 0s in exponent and mantissa, and either bit in the
sign. Thus, there is negative and positiv 0.

The highest value exponent (all 1s) means either \infty or NaN. If the mantissa
is 0, then it means \infty, using the sign to distinguish negative or positive
infinity. If the mantissa is any other number, it means NaN. Note that this
means that NaN is the only value that has many different representations.

The lowest value exponent (all 0s) represents subnormal numbers. It's a way to
ensure that we can represent numbers very close to 0.

Determinism
-----------

Floating point with canonical NaNs makes floating point deterministic, but slows it down a bit
The rationale for keeping them is that if you include Rust code which uses floats, and the compiler can't optimize away those functions, then you will still have some floating points in your contract, but it's expected they will not be used.

# Mini Float png

A mini float format wedged into an 8 bit per channel RGBA png. Each 8
bit RGB channel has 6 Significand with the 2 LS exponent bits, the
remaining exponent bits are in what is normally the alpha channel.

In the 8 repourposed alpha bits, the MSb is a 1 bit alpha, next is a
signed/unsigned mode flag, then the 2 MSbs of each RGB channel
exponent/sign bit.

Asymetrical bias with a per-pixel control of signed/unsigned mode,
default bias weighted to favor positive numbers.

4 bit exponent in unsigned mode, and 3 bit exponent in signed mode. The
mode flag in the alpha sets signed/unsigned for the pixel, applying to
all three RGB channels. In signed mode, each individual RGB channel can
be either positive or negative in the range ±1.984375

The top MSb of the exponent is *either* a 4th exponent bit for positive
floats, ***or*** is the sign bit.

-   Apply a bitwise OR between the flag and the MSb (flag\|MSb) and this
    will *always* show sign for the number, 1==(+) 0==(-), regardless of
    mode.

Bit segmenting is the two MSb of the exponent, the full mantissa is in
position on each RGB channel.

Significand (Mantissa) is 7 bits (6 bits explicit, 1 implied).

### Bias and Base can be arbitrary
Set in the `mFLT` chunk, along with an optional scale.

-   Default bias of 8 and base 2, provides a range of -1.98 to +508.0
-   Signed mode range is ± 1.984375
-   Unsigned mode is 0 to 508.0
-   In signed mode, the top MSb is the sign bit.
-   In unsigned mode, the top MSb it given to the exponent & indicates
    the exponent's sign.
-   Signed or unsigned mode is per pixel, and can be dynamically
    switched.
    -   caveat: values *less than zero* **can not be in the same pixel**
        *with values greater than 1.98*
    -   This only applies to dynamic signed/unsigned mode.
    -   Encoder can resolve edgecase conflicts by clamping negative to 0
        or overs to 1.98
    -   The mFLT chunk should have an image-wide lock for the
        sign/unsign flag.

### An arbitrary base can be used

-   The base can be 10 for instance
-   For more granularity, the base "could" be something like square root
    of 2 (1.414...)
    -   Though it might be more efficient to introduce a scale factor in
        the mFLT chunk
    -   The one bit used as alpha might be better used to dynamically
        select/enable a scale factor.

### Summary

-   Effective precision of an 11 bit float (4bit exponent, 7bit
    mantissa)
-   Maps to an 8 bit per channel png (RGBA) container
-   Expected to compress well using the standard png compression
    schemes.
-   Arbitrary base supported (image wide)
-   Scale factor supported (image wide)
-   Adjustable bias (image wide)

<!-- -->

-   Precision is acheived by:
    -   Asymmetric bias to maximize useful range for images.
    -   Dynamic per pixel sign/unsign mode assumes that:
        -   negative RGB data is less common, and
        -   when present in a pixel, the other pixels are likely to be
            in the ±1.98 range,
        -   encoder can resolve edge case conflicts by clamping
            negatives to 0 if a high value is present or vice versa,
            with minimal issues.

![MiniFloat png file pixel structure oct28-2023](https://github.com/Myndex/10bit-png/assets/42009457/c5f01d7b-3944-4a4e-8e1c-6c702e1e9380)


©2023 Andrew Somers

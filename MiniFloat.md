# Mini Float with Dynamic Bias png

**WORK IN PROGRESS**

_Still working on denormalized values vs normalized ones. I need sleep
before I can finish that_.

----
## Wut?
A mini float format wedged into an 8 bit per channel RGBA png. Each 8
bit RGB channel has 6, 7, or 8 bits mantissa with the remaining bits for
the LSb of the exponent. Then the two MSb of each exponent are placed in
what is normally the alpha channel, into the 6 LSbs of that channel.

In the repurposed alpha, the two MSbs are a 2 bit bias selector, then
the 2 MSbs of each RGB channel's exponents.

Asymmetrical image-global bias, with per-pixel control of one of four
bias values. Default bias values depend on the number of exponent bits.

2, 3, or 4 bit exponent per channel, then 2 bit _bias select_ to select
one of 4 preset image-wide bias values (see chart)

A sign bit can be used with a 2 or 3 bit exponent. Otherwise, the values
are unsigned. This table shows the number of mantissa bits per
exponent/sign configuration.

![MiniFloat bias chart interim oct29-2023 v2](https://github.com/Myndex/10bit-png/assets/42009457/fced2f22-74fb-463d-a03d-6fd052ddc647)

Bias is always subtracted so a negative bias value would move the
exponent farther positive. Bias values overlap in an amount relative
to the size of the exponent.

Note: normalized versus denormalized numbers needs to be worked out.

-   For signed modes (1a or 2a), each individual RGB channel can be either positive
    or negative.

-   The sign bit, if it exists, is always adjacent to the MSb of the
    mantissa.

The full mantissa occupies each RGB channel. For mantissas of 6 (or 7)
bits, the 7th (or 8th) bit is either LSb of the exponent or the sign bit.

Significand (Mantissa) is 6,7, or 8 bits plus 1 implied.

**Base, scale, and the four bias can be set in the MFLT chunk.**

-   Default Base 2, scale 1.0,
-   Bias matrix is based on mantissa bit depth
-   In signed mode, the sign bit is adjacent the top MSb of the
    mantissa.

<!-- -->

-   **Caveat:** values if a given pixel is lower than can be realized by the
    current bias, the higher channel has priority, and the lower channel
    will be one of (in order of precedence)
    -   Clipped to a predetermined black level (set in MFLT chunk).
    -   Clipped to the bottom of the bias range
    -   Clipped to 0
    -   Encoder can selectively give priority to lower colors and
        resolve edge-case conflicts by clamping the overs to a preset
        value.
    -   The MFLT chunk should have an image-wide flag for
        signed/unsigned.
    -   An image-wide scale factor is in the MFLT chunk, and can be used
        to compress the distance between values for better tracking.

### *Summary*

-   Effective precision of an 11 bit float (2,3,4 bit exponent with
    9,8,7 bit mantissa)
-   Maps to an 8 bit per channel png (RGBA) container.
-   Expected to compress well using the standard png compression
    schemes.
-   Arbitrary base supported (image wide)
-   Scale factor supported (image wide)
-   Adjustable set of 4 indexed biases (image wide)
    -   each pixel can select one of four biases.
<!-- -->

-   Precision is achieved by:
    -   Asymmetric bias to maximize useful range for images.
        -   The three RGB tuples are likely to be in the range of a
            single indexed bias,
        -   encoder can resolve edge case conflicts by clamping
            negatives to a preset or 0 if a high value is present or
            vice versa, with minimal issues.


![MiniFloat png file pixel structure oct29-2023 v2](https://github.com/Myndex/10bit-png/assets/42009457/aab04be9-9f6d-4f41-84df-f514f5876997)


©2023 Andrew Somers

# Y<sub>PQ</sub>UV png

This converts 12bit per channel PQ RGB to YUV, where Y is PQ gamma at 12
bits, and the U and V are 10 bits each.

The advantage is maintaining the essential luminance resolution, but
compacting the full PQ signal into an 8bpc png container.

Coefficients are applied to the gamma encoded RGB tuples for
computational efficiency, as is common practice in broadcasting.

In the layout below, the V is inverted to -V, essentially putting blue
on top. In this way, the truncated U Y -V channels very roughly fit R G
B, for a semi-viewable image at the file system level.

**Advantages**

-   A 12 bit PQ image in an 8bpc container.
-   Uses standard png compression
-   Y<sub>PQ</sub> is created with the PQ gamma for computational
    simplicity
-   UV is used as it is a common, simple transform.



![YpqUV 655](https://github.com/Myndex/10bit-png/assets/42009457/389e3d0c-13f9-45e6-843e-98c3ace86a50)


------------------------------------------------------------------------

## Y<sub>PQ</sub>UV 422 png

This converts 12bit per channel PQ RGB to YUV, where Y is PQ gamma at 12
bits, and the U and V are 12 bits each, but only U or V is present on
any given pixel.

The advantage is maintaining the essential luminance resolution, but
compacting the full PQ signal into an 8bpc png container, and maintiaing
higher color depth than the previous method, trading off for spatial
resolution instead.

Coefficients are applied to the gamma encoded RGB tuples for
computational efficiency, as is common practice in broadcasting.

Unlike the above version (12 10 10), this version will not be
particularly visible in legacy viewers, but it does retain a full 8 bit
alpha channel. 

It should compress well, as the vertically adjecent pixels will both be    
either U or V type, so though the horizontal adjacent pixels alternate    
U or V, the prefilter should in theory select the vertically adjacent   
pixel for the deltas.

An alternate scheme is, horizonally UUVVUUVV, and offseting each line by 1 pixel as:

UUVVUUVV    
UVVUUVVU    
VVUUVVUU    
VUUVVUUV    

Which would give the prefilter more options for which pixel(s) to select for the deltas.

Advantages

A 12 bit PQ image in an 8bpx container.
Uses standard png compression
U pixels are aligned vertically, as are V, which should help compression.

**Advantages**

-   A 12 bit PQ image in an 8bpx container.
-   Uses standard png compression
-   U and V are distributed to pixels in a vertically aligned way that should compress well.
-   Y<sub>PQ</sub> is created with the PQ gamma for computational
    simplicity
-   UV is used, as it is a common, simple transform.
-   U and V are at 12 bits, and trade out each pixel.
-   The full 8 bit Alpha is maintained.


![YpqUV odd U even V vertical pixels](https://github.com/w3c/PNG-spec/assets/42009457/469fc256-dc6c-42db-9640-76fb0de3b5af)

©2023 Andrew Somers

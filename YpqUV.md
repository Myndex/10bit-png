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
alpha channel. Also it may not compress as well due to the altering U
and V per pixel in a checkerboard pattern. More work is needed to find
the best configuration to work with the existing png compression scheme.

**Advantages**

-   A 12 bit PQ image in an 8bpx container.
-   Uses standard png compression
    -   however, it should be noted that altering U and V every pixel is
        likely less efficient compressing than the 12/10/10 versions
        above.
-   Y<sub>PQ</sub> is created with the PQ gamma for computational
    simplicity
-   UV is used, as it is a common, simple transform.
-   U and V are at 12 bits, and trade out each pixel.
-   The full 8 bit Alpha is maintained.


![YpqUV odd U even V](https://github.com/Myndex/10bit-png/assets/42009457/fd8dd43b-f0aa-46e2-a225-71767bf42e51)


©2023 Andrew Somers

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



![YpgUV  png file pixel structure oct29-2023](https://github.com/Myndex/10bit-png/assets/42009457/4bc18ee5-cd1e-4e08-8313-4d773eee246e)




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

Unlike the above version (12 10 10), this version will not be particularly    
visible in legacy viewers, but it does retain a full 8 bit alpha channel. 

It should compress well, as the vertically adjecent pixels will both be    
either U or V type, so though the horizontal adjacent pixels alternate    
U or V, the prefilter should in theory select the vertically adjacent   
pixel for the deltas. I.e.:

UVUVUV    
UVUVUV

An alternate scheme is, horizonally UUVVUUVV, and offsetting each line by 1 pixel as:

The stagger should progress right:

UUVVUUVV    
VUUVVUUV    
VVUUVVUU    
UVVUUVVU

This way a U (or V) will always have a U (or V) either above or to the left.


### Advantages

-   A 12 bit PQ image in an 8 bpc container.
-   Uses standard png compression
-   U and V are distributed to pixels in a vertically aligned way that should compress well.
    - Alternately, UU and VV pairs alternate, with a march right per line.  
-   Y<sub>PQ</sub> is created with the PQ gamma for computational simplicity
-   UV is used, as it is a common, simple transform.
    - Of course other color encodings could be used.
-   U and V are each 12 bits signed (or offset?) and alternate each pixel, or a marching-offset pair.
-   The full 8 bit Alpha is maintained for the alpha version.


### Unknown Issues
- Not sure if this scheme will compress usefully, it seems it should but tests are needed.
- No legacy safe fallback view such as proposed for the 10bit RGB of this repo.

![YpqUV 422  png file pixel structure oct29-2023](https://github.com/Myndex/10bit-png/assets/42009457/bdfd8cf4-4392-4e41-9ef8-ff9250bb8bee)

Sampling U and V at half the spatial is a common strategy, as the Y holds all the important spatial detail. THis is in accordance with the human vision system's handling of hue/chroma at a third or less the resolution of luminance.

Rather than UV, other color difference modes could of course be used... But thinking efficiency, simplest transforms, avoid any unnecesarry math for converting from YUV to RGB..

The question, is LZ77 decompress fast enough for a streaming use case...

©2023 Andrew Somers

# 10bit-png
A proposal for a compression efficient 10bit variant of png.

This is also [**issue 357 at the png-spec repo**.](https://github.com/w3c/PNG-spec/issues/357) comments should probably be made there in the interim.

While it is possible to use the sBIT chunk to put a 10bit per channel image into a 16bit container.....
> ....More efficient coding of 10-bit (and 16-bit) words requires a different coding technique, but that is a totally different (and more complex) issue IMHO....

This problem spawned an idea.....
### Not your granddad's DPX
When I stumbled [onto this thread](https://github.com/w3c/ColorWeb-CG/issues/69), first thought was the existing three channels of 10bit into four bytes format of DPX, but I don't think that fits well into png.


### The Alpha Wolf Shares with the Pack
But if there is a desire to conserve bandwidth, it occurred to me that the **color type 6** 8bit **rgba** png, might easilly be modified so the two LSbits of each 10bit channel are mapped onto the 6 LSbs of the alpha channel, and the 2 MSbs of the alpha could still be used for a one or two bit alpha.

A two bit alpha could be combined with the `tRNS` chunk to have 4 indexed transparency values (though that may cause compatibility issues), otherwise, `0b00` = 0% opaque, `0b01` = 33%, `0b10` = 66%, `0b11` = 100% opaque.

### Fall Backwards (compatibility)?
The next question is, is there a configuration where a decoder/viewer that was not capable of handling this `segmented10bit` format, and just discarded the bits in the alpha as if fully opaque? In this case, the LSbs would be truncated, and while truncation is a poor way to handle down sampling, and does have artifacts, the image would still be reasonably viewable.

The `sBIT` chunk provides the way to make this happen, to mask the LSbs in the alpha and also show the 2 MSbs of transparency, so a 10 bit image could display with a reasonable fallback in a naïve/legacy viewer.

Setting `sBIT` to `8 8 8 2`, then current decoders/viewers should display the truncated-to-8 image okay, and with the alpha `sBIT` at `2` bits, only the two MSbs would be used, with the 6 LSb rgb bits being hidden.

### Virtual Signaling 
As per the graphic below, the IHDR chunk would indicate 8 bit and color type 6, for fallback compatibility. So how to tell the decoder we're a segmented 10bit image? We use a `tEXt` chunk with one string that says `segmented10bit` to signal the format, and this maintains backwards compatibility.

![10 bit png a visual info graphic that shows the data arrangement for a 10 bit PNG, and the chunks that would be involved](https://github.com/w3c/ColorWeb-CG/assets/42009457/e304ad1e-01be-4c26-a17a-820c5ab2ef2f)

## Advantages

1) A 10bit png format with all the advantages of png, but a bit depth to match many current video and HDR formats.

2) Should compress similarly to an 8bit RGBA png. First three bytes are not unlike an 8 bit image, then the one LSb/alpha byte, which might not compress as small as a typical alpha, but overall this scheme should be substantially more efficient than 10-in-16.

3) So long as the decoder supports `sBIT`, this should be backwards compatible, with the caveat that images with truncated LSbs may have artifacts. 

4) And finally, though tests need to be run, this seems like an efficient way to handle 10bit images as far as the compression and total data size is concerned.
    - IMO it has the potential for use in an image-sequence stream.


Curious your thoughts? Add to the discussion!


Thank you for reading.


_Andrew Somers_    
_Director of Research_    
_Inclusive Reading Technologies, Inc._    

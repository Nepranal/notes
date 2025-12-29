# The Updated GPU Pipeline

To accomodate for texture operations, the pipeline adds one more component called the texture unit:

![alt text](<sources/Textures on The GPU/Screenshot 2025-12-02 at 6.11.26 PM.png>)
accessible trhough both shader components but we mostly use it at the fragment shader.

The texture unit is an actual piece of hardware dedicated to texture filtering. So things like bilinear/trilinear filtering, mipmaps, etc. Are handled by this unit.

# Accessing Textures

As you'd expect, using textures is kinda like using everything else graphics related. You'll need to first bind a texture to the texture unit and then you can use it.

Something peculiar to deal with here is dealing with multiple textures. It's not as simple as just assigning all textures into the texture unit. In fact, there are multiple ways you can do so. The simple way, is to imagine that you have multiple texture units and assign each texture to a texture unit accordingly.

# Reference

https://youtu.be/WULOKMqEGA0?si=Qvq2K9Wzx_etKvOH
https://stackoverflow.com/questions/34497195/difference-between-format-and-internalformat

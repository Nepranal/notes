# Textures

# Representing Textures

To think about this, think about what textures are supposed to do. Put it simply, there are there to store colour information at each vertex. So you can imagine that perhaps, you can store it as an image; indeed you can. More than that, you can also store textures as a 1D selection of colours, and a 3D block

![Screenshot 2025-11-02 at 8.48.30 PM.png](sources/Textures%2029a1aa5d8a0e808fa67ff228c398cac7/Screenshot_2025-11-02_at_8.48.30_PM.png)

Additionally, you can also store procedural textures where you query the texture colours from a function. Quite popular these days with NeRFs (Neural Radiance Field) and all

# uv Vs st coordinates

Now, say we have an image to be used as our texture. The next thing you have to do is to assign where in the texture, the associated vertex is supposed to be. The texture itself has a coordinate system called uv-coordinates. It’s nothing fancy, just imagine your typical cartesian coordinate system. Something to be aware here though is that there’s another one called st-coordinates.

![Screenshot 2025-11-02 at 8.52.11 PM.png](sources/Textures%2029a1aa5d8a0e808fa67ff228c398cac7/Screenshot_2025-11-02_at_8.52.11_PM.png)

Luckily, they don’t have much of a difference, is just that st-coordinates are normalized. Though, It is noted that st-coordinate is more popular than uv-coordinates because of the flexibility they provide. Imagine if you have a higher resolution texture. With st-coordinate, you don’t have to reassign the vertices again if you want to replace the underlying texture

# Bilinear Filtering

# References

https://www.youtube.com/watch?v=Yjv6hc4Zqjk&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=15&t=10s
https://www.youtube.com/watch?v=WULOKMqEGA0&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=15

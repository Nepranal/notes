# Barycentric Coordinates

# Introduction

The concept is actually quite simple. I just made this note because it’s something new to me and the name’s kinda funny considering what it actually is.

![Screenshot 2025-11-02 at 8.33.29 PM.png](sources/Barycentric%20Coordinates%2029a1aa5d8a0e80eebb57e72a0e6c71a5/Screenshot_2025-11-02_at_8.33.29_PM.png)

This is it. It’s just figuring out the coordinate of a point within a triangle in terms of the other vectors. Since P lies in the plane as P0, P1, and P2, P can then be written as a linear combination between the 3 vectors. Relating this to linear algebra, you can resolve for P by solving a set of linear equations or, you can solve it based on areas as follows:

![Screenshot 2025-11-02 at 8.42.25 PM.png](sources/Barycentric%20Coordinates%2029a1aa5d8a0e80eebb57e72a0e6c71a5/Screenshot_2025-11-02_at_8.42.25_PM.png)

What’s cool about the barycentric coordinates, the little coefficients can be used for interpolating properties stored in the vertices e.g. Colours, for when we have to do texturing.

# References

https://www.youtube.com/watch?v=HV2dKIQcp6k&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=13&pp=iAQB

# Rendering

Rendering is the process that takes your scene and putting it on the screen. You can imagine it as the process of flattening whatever's in the canonical view space. The main problem rendering algorithms solve is to figure out for a particular primitive element, e.g. The triangle of a mesh, where it will show up at the screen i.e. Which pixel(s) will it affect.

Generally, rendering algorithms can be grouped into rasterization or ray tracing algorithms. Rasterization deals with rendering by considering, for each object, where it will show up on the screen. Ray tracing is kind of the opposite of this process. It goes from each pixel and try to figure out which object 'emits' that ray; all of the objects are considered at once.

The good news is that, a lot of the algorithms are actually related. You can even kind of think of ray tracing and rasterization to be the opposite of one another

![alt text](<sources/Rendering Algorithm/Screenshot 2025-12-12 at 1.52.43 AM.png>)

# Painter's Algorithm

Let's start with the very basic one: The painter's algorithm. And, before we do so, let's just somehow assume that we know, for a given primitive, which pixel(s) it will affect.

The painter algorithm will first sort all of the object of your scene by their position in an ascending order. This way, you'll first 'paint' the object from the most back to the most front. I hope you can intuitively see why this wouldn't cause any object to be overlapped if you ignore the sorting issue.

What sorting issue? Consider the following arrangement

![alt text](<sources/Rendering Algorithm/Screenshot 2025-12-11 at 10.43.38 PM.png>)

How would you define which one's infront or which one's at the back? The issue here is that the ordering is too coarse. We need something finer. Btw, you can also consider some other cases where it's not clear which object's at the back and which at the front. Additionally, you may also consider, how do you sort an entire geometry? You can of course come up with some heuristic but then you may run into the aforementioned issue.

**Painter's Algorithm**

- Need Sorting
- Cannot handle intersecting geometry

# Z-Buffer Rendering

Z-Buffer solves the issue of painter's algorithm by storing for each pixel the depth information. So, if we just start as usual, get a primitive object, figure out the correspondence of the each point of the object into the pixel of the screen and record the colour along with it's depth. Then, for the next primitive, we'd do the same. If there's a conflict, we just need to compare the depth. The one more front wins.

This works very well and not once did I mention sorting since you only need the most front. It's kinda like searching for a max value in an array. Though it may struggle with transparent objects. Consider:

![alt text](<sources/Rendering Algorithm/Screenshot 2025-12-11 at 11.11.44 PM.png>)

If you got the transparent object first, the blue coloured pixel won't be considered since it's at the back even though in this case it should've.

**Z-Buffer Rendering**

- Need sorting for transparent object

## Anti-Aliasing

Just a little tangent, do you think that the produced raster images looks so far away from the actual objects? Well, you can sort of explain it by the resolution of our raster. If we had a higher resolution, perhaps we won't see the issue. Regardless, there's actually still some stuff we can do to make our raster looks better

The first approach is called Super Sample Anti-Aliasing (SSAA). The idea is to increase the number of points within the screen's square. Recall that a pixel is a point. The square is an approximation of that point to display on our screen. The pixel is typically located at the center of the square. SSAA increases the number of pixels within a square. So instead of 1, perhaps we'll have 4, 8, 16, etc. Points.

![alt text](<sources/Rendering Algorithm/Screenshot 2025-12-11 at 11.39.48 PM.png>)
The idea being, we can now have more information on how to better represent the colour. If on that square say we detected a tinge of red and white by the number of pixels within, we can draw a paler red on it instead of only white just because the red colour never passed the pixel. The more points you have, the better of an approximated colour you can have. You can sort of imagine doing SSAA is kinda like doing rendering for a higher resolution screen but for a lower resolution.

This sounds great but we need to consider the expenses. Having more points within a pixel means you'll have to shade each of the pixel independently. If you're doing 4x SSAA, you'll have to, for each primitive, shade the pixels 4 more times than without; considerably more shading. Furthermore, you'll have to store more information per pixel. With 4x SSAA and Z-Buffer rendering, you'll store RGBA + z x 4 amount of data in the buffer of a pixel.

An alternative of SSAA is Multi Sample Anti-Aliasing (MSAA). The idea is, instead of just sampling at a higher resolution, we simply sample more depth per pixel while storing only a single colour. When a new primitive crosses some points within our pixel, we will then consider the new colour by the amount of points you cross + the previous colour stored within the buffer of that pixel. This way you're back to shading once per primitive

# A-Buffer Rendering

Here's the improvement (kind of) for Z-Buffer rendering. This time, we keep a linked list of the colour as well per pixel + coverage of the primitive. And yes, with A-Buffer, you don't need to do MSAA or SSAA.

![alt text](<sources/Rendering Algorithm/Screenshot 2025-12-12 at 1.38.00 AM.png>)

The idea is then you can freely order the list. If the transparent object comes first, then you don't immediately reject the blue triangle instead you store the info in the linked list ordering it appropriately there.

**A-Buffer Rendering**

- Requires dynamic memory
- Typically for offline rendering

# REYES

This one is actually pretty similar to the previous algorithms discussed for rasterising. The only different is, it is not dependent to any primitive. Implicitly, we've only discussed meshes with triangular bases. REYES gives the freedom of using others e.g. Bezier curves, NURBS, hexagonal faces, etc.

The main idea is to break down the surface as much as you can such that they're now way smaller than a pixel becoming micropolygons. The rest follows similarly (kinda, i don't really know).

# Ray Tracing

As mentioned, ray tracing goes from pixel and following a ray from that pixel to figure out the corresponding primitive. It's a rather simple idea but what makes it so interesting is the fact that you can keep following the ray as it bounces off objects. This capability allows ray tracing to perform very realistic rendering. You can imagine for example that if a mirror correspond to a pixel, you can figure out what it's reflecting by following the reflected ray.

However, ray tracing is very expensive. First of all, you need to go through all of the primitives in your scene which is a number of very high magnitude for **each** ray. Ray tracing also acceses data in a more random way. It doesnt access the next closest point of the primitive but rather it could jump from 1 object to another very quickly (and is expected to do so). Memory access would be a siginificant cost especially when there's a lot of primitives.

There is a saving grace however. Ray tracing usually comes with a spatial data structure that essentially allows you to do binary search searching for the primitives closest to your ray (this thing also the one responsible for the random access aspect). Consider that for rasterization where going through each object would mean a linear time. So, in terms of complexity, ray tracing does beat rasterization but you'd need a lot of primitives. So, more often than not, rasterization does still have ray tracing beat but there's a limit.

# Reference

1. https://youtu.be/0WrzyD8nBlk?si=SAwZSVa3l3xy3Rew
2. https://learnopengl.com/Advanced-OpenGL/Anti-Aliasing

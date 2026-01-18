# Fisheye Lens Camera

The fisheye lens is a type of lens that allows you to take in the hemishperic view of a scene instead of your typical plane

![alt text](<sources/Wide Angle Cameras/Screenshot 2026-01-09 at 8.46.00 AM.png>)

It utilizes lenses which are convex on one side and concave on the other. As you can imagine, the idea is to take in as light from different angles as much as possible and focus them into one particular point. In this case however, we only have an approximation of a point which is fine (the point is the small rectangular-like lens).

> It's very important for camera systems to have this singular point of focus. As long as you have this, you can still project the scene into a single perspective. If you don't, then information of the 3D structure of the scene becomes kind of neccessary in order to project it.

# Planar Mirrors and Reflected Cameras

What would happen if you use mirrors near your cameras? Well, the main thing that happen is your cemara gets to see at a different angle. Another way of putting it is as if your camera got duplicated at the mirror creating what we call a 'virtual camera'. This virtual camera behaves much like your actual camera and can be reflected more times

![alt text](<sources/Wide Angle Cameras/Screenshot 2026-01-09 at 8.57.24 AM.png>)

# Hyperbolic Mirror Cameras

Planar mirrors cannot really expand the field of view of the camera more than just rotating it around. To expand, we use hyperbolic mirrors

![alt text](<sources/Wide Angle Cameras/Screenshot 2026-01-09 at 9.04.12 AM.png>)

You see, hyperbolas have 2 points of focus. In the image you'll see this as `focus 1` and `focus 2`. The big idea behind hyperbolic mirrors is that any light from the world that is pointing at `focus 1` will be reflected to `focus 2`. So you'll imagine that `focus 2` will sort of where you'll put your pinhole.

Another property of such a mirror would be that the image formed will be that of the perspective of the world as seen from `focus 1`. I think the main idea here is that the pinhole setup is reflected into `focus 1`.

This mirror also works for light coming in below that horizontal line from `focus 1`.

# References

1. https://www.youtube.com/watch?v=oYOpEkaeq_o&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=6&t=959s

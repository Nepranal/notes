# Problem

> Find all values of n > 1 for which one can dissect a rectangle into n right
triangles, and outline an algorithm for doing such a dissection.

# Solution

$n$ can be any number bigger than 1. To get $n=2$, we can simply draw a diagonal between 2 corners of the rectangle.

From here, there are multiple approaches to it. To get $n>3$, what we can do is to first start from $n=2$ case and draw on one of the sides of the 2 right triangles. Then, you can draw a line from the 90 degree corner into the horizontal line to create a right angle triangle (it would be the shortest line from that point to the horizontal line). But notice, once you've done this, you get 2 90 degree corners you can draw from. Each line you draw, gives a right angle triangle. One pattern that you can get from this is a sort of zig-zag lines on one side of the triangle

![alt text](<src/Rectangle Dissection/Screenshot 2026-11-08 at 7.39.43 PM.png>)

Another approach splits the problem into 2 cases: even and odd $n$. In the case for when $n$ is even, remember that starting with the original rectangle we can get 2 right angle triangles. So, let's just cut the original rectangle into $n/2$ smaller rectangles and for each of the rectangles, we'll draw a diagonal line. For odd $n$, we can first get $n-1$ right angle triangles by means of previous methods. Then we can draw a an extra line within one of the right triangles to get 1 extra right angle triangles

![alt text](<src/Rectangle Dissection/Screenshot 2026-11-08 at 7.45.28 PM.png>)

The whole idea behind the strategy is to draw from a 90 degree angle and that each newly drawn right angle triangle will produce 2 new right angle triangles

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.
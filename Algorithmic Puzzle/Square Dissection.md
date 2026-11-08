# Problem

> Find all values of n for which one can dissect a square into n smaller squares and outline an algorithm for doing such a dissection.

# Solution

A very obvious solution is to continuously split the rectangle first into 4 equal pieces. Then, what you can do is to choose one of the squares, and do the same operation again and again to that square $x$ times. This will give

$$
n = 3x + 1
$$

where $ x > 1$ is the number of cuts. But, can we do better?

A thing that we can consider is to adjust the sizes of the squares instead of keep splitting. Let's say we want to get $n = 6$, then we can just do

![alt text](<sources/Square Dissection/Screenshot 2026-02-04 at 5.02.42 AM.png>)

Notice that we can do this for any even number $n>2$. We can't really do $n=2$ since that will just give us rectangles no matter how you split it since they both shares a side.

Great, but can we do odd numbers as well? Well, we can't do $n=3$ and $n = 5$. The little squares must be present at the right angles of the original square. $n = 3$ won't have enough while $n = 5$ would have an akward extra square which you can't fit anywhere.

But other than those two cases, you can indeed construct it when $n$ is odd. The approach is the same but now, you split one of the edge squares into 4 smaller pieces. This means we can get to any (even number > 2) + 3.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

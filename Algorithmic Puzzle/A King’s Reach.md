# Problem

> (a) The king in chess can move to any neighboring square horizontally, vertically, or diagonally. Assuming that the king starts on some square of an infinite chessboard, in how many different squares can it be after n moves?
> (b) Answer the same question if the king makes no diagonal moves.

# Solution

For part (a), after 1 move, the king piece could be in any of it's 8 neighbors. After 2 moves, it could be in the neighbours of the neighbour or back at the origin. You can somewhat imagine that this would just produce a square

![alt text](<sources/A King’s Reach/Screenshot 2026-01-18 at 2.45.46 PM.png>)

Tracing it out, Your king could be in any of the black dots. So, it will be $(2n+1)^2$ except when $n=1$ which would just be $8$.

For part (b), consider the cases to be either odd or even. You can first trace the possible cases for small $n$ as before

![alt text](<sources/A King’s Reach/Screenshot 2026-01-18 at 3.23.33 PM.png>)

As you trace, you'll find that depending on whether you have an even step or odd numbered steps, it is not necessarily true that all previous squares could be visited as before. Particularly you'd find that if $n$ is even, then all of the black coloured dots are visitable while the white ones aren't and vice versa if $n$ is odd. So, the total number is the squares that fall under the same colour.

To compute this, it would be nice to first know given a step $n$, what's the size of the outermost square it could travel to.

$$
2(n+1) + 2(n-1) = 4n
$$

Now, for even $n$ you can set up the following equation

$$
1 + 4(2) + 4(4) + \dots + 4(n) = 1 + 4*2(1 + 2 +\dots+n/2)
$$

We can simplify

$$
1 + 8 \frac{\frac{n}{2}(\frac{n}{2}+1)}{2} = 1 + 2n(\frac{n}{2}+1) = 1 + n^2 + 2n = (n+1)^2
$$

Turns out, the case for odd $n$ follows similarly. Say that $n$ is odd so $n = 2k+1$ for some integer $k$.

$$
4(1) + 4(3) + \dots + 4(n) = 4(2(0) + 1 + 2(1) + 1 + \dots + 2k+1)
$$

We have $k + 1$ terms so it would turn out to be

$$
4(k+1 + 2(1+2+\dots+k)) = 4(k+1+k(k+1)) = 4(k+1)^2
$$

But, as per the assumption, $k = \frac{n-1}{2}$. So, we can get

$$
4(\frac{n-1}{2} +\frac{2}{2})^2 = 4(\frac{n+1}{2})^2 = (n+1)^2
$$

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

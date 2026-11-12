# Problem

> The squares of an 8 × 8 chessboard are mistakenly colored in blocks of two colors as shown in Figure 2.4. You need to cut this board along some lines separating its rows and columns so that the standard 8 × 8 chessboard can be reassembled from the pieces obtained. What is the minimum number of pieces into which the board needs to be cut and how should they be reassembled?
>
> ![alt text](<sources/Chessboard Reassembly/Screenshot 2026-01-18 at 2.14.40 PM.png>)

# Solution

As the picture would already give away, in order to turn the figure into a chessboard, you'll need alternatig colours. The central 4 x 4 are actually somewhat already there but they need to be rotated. You can imagine that you can first fix all of the 4 x 4 so that they match the top left corner of the 4 x 4.

The other problematic part is the side 2 x 1. You need to flip them as well so that they will follow the chessboard pattern accordingly. Overall you'll have to cut the board into 25 pieces.

But here's a question: how do we know that this arrangement is the smallest number of pieces we can cut the board into? To have the smallest possible, it must be that each piece is already close to the solution. What you'll find is that the 2x2 blocks are the maximal for which this is true. Any 3 x n rectangles you cut out will violate the condition of being a valid chessboard and will need at least 1 more cut. The 2x2 is the only 2x2 we can find and so is optimal.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

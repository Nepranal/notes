# Problem

> Is there a way for a chess knight to start at the lower left corner of a standard 8 × 8 chessboard, visit all the squares of the board exactly once, and end at the upper right corner? (The knight’s moves are L-shaped jumps: two squares horizontally or vertically followed by one square in the perpendicular direction.)

# Solution

The key to this is the ideas of an invariant. Since this is an 8x8 board, the starting point (bottom left corner) and the end (top right corner) will have different colours. Every odd number of moves can get you to a different coloured square and the opposite when you have even numbered moves.

To cover all boxes before the end square would require 63 moves from the starting point. This means you will end up in a different coloured square no matter your strategy. Since an odd numbered moves brings you to a different colour, a succesful strategy would require that the start and end point has the same colour. The proposed scenario is impossible.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

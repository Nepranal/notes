# Problem

> A jigsaw puzzle contains 500 pieces. A “section” of the puzzle is a set of one or more pieces that have been connected to each other. A “move” consists of connecting two sections. What is the minimum number of moves in which the puzzle can be completed?

# Solution

In the beginning we have a scatter of 500 pieces or 500 sections. As we make a move, we decrease the number of sections by 1. After $n$ moves, we'll have $500 - n$ remaining sections. When you make a move for sections with more than 1 pieces in them, you have to consider the current state of the puzzle with those sections as "big" pieces.

After 499 moves, you have only 1 section left, the puzzle itself.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

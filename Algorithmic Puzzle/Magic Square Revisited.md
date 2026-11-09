# Problem

> A magic square of order 3 is a 3 × 3 table filled with nine distinct integers from 1 to 9 so that the sum of the numbers in each row, column, and two corner-to-corner diagonals is the same. Find all the magic squares of order 3.

# Solution

First observation we can make is to find the total sum. All sum of the numbers ($a_1+a_2+\dots+a_9 = 9(10)/2 = 45$). Notice that this is also sum of the 3 rows of the square

$$
3S = 45\\
S = 15
$$

Where $S$ is the total sum of a row/column/diagonal. Another bit of arithmetic that we can do, is if you write out all of the sum, you'll notice that $a_5$ is involved with all of the individual terms

$$
a_1 + a_5 + a_9 = 15\\
a_2 + a_5 + a_8 = 15\\
a_3 + a_5 + a_7 = 15\\
a_4 + a_5 + a_6 = 15
$$

We can then write

$$
3a_5 + 45 = 4*15
$$

where we'll find $a_5 = 5$. This means

$$
\begin{bmatrix}
    x&&x&&x\\
    x&&5&&x\\
    x&&x&&x\\
\end{bmatrix}
$$

where we need to figure out the rest. We can try one by one but notice, if we take 9, you can't put 9 in any of the corners because if you do, the rest of the 2 numbers of the row and column must sum up to $15 - 9 = 6$ for which, the distinct numbers we can get is $2+4$. This means 9 must be at the sides and with that

$$
\begin{bmatrix}
    6&&7&&2\\
    1&&5&&9\\
    8&&3&&4\\
\end{bmatrix}
$$

Any other arrangements are simply a permutation of this matrix. If you rotate around you'll get 4 and then you can swap either 3 and 7 or 1 and 9 and rotate again to get another 4 getting a total of 8

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.


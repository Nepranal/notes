# Problem

> Pages of a book are numbered sequentially starting with 1. If the total number of decimal digits used is equal to 1578, how many pages are there in the book?

# Solution

This is really more of a counting exercise

If $n = 9$, then we have a total of $9$ total digits. If $n = 99$, then we have $9 + 2(90) = 189$. If $n = 999$, $189 + 3(900) = 2889$. Here, we know that $n$ must be a 3 digit numbers.

A general formula would be $189 + 3(n - 100 + 1), 100 \leq n \leq 999$. Now we just gotta solve $n$ for

$$
189 + 3(n-99) = 1578
$$

Which gives $n=562$

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

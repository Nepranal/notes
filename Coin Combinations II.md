# Problem Statement

> Consider a money system consisting of n coins. Each coin has a positive integer value. Your task is to calculate the number of distinct ordered ways you can produce a money sum x using the available coins.

# Solution

This problem is, first of all, an extension of Coin Problems I (also in cses) but now we have to enforce order.

## First Attempt

Let's imagine that the sum 1 to `x` are all empty boxes. Using the approach of Coin Problems I, we're basically just using all possible boxes and combine them together without considering different permutations of the sum.

A thing we can do is to make an assumption that previous boxes only have unique sums. The issue then lies in how we aggregate the previous sums and more importantly, how can we get rid of possible duplicates. Now, I only have a hunch, but you can probably do this by the inclusion-exclusion principle and that if there is an overlap, then having the first two terms must match.

What I mean is, if you have 3 coins $c_1, c_2, c_3$ with sets $C_1, C_2, C_3$ where each set contains all possible sums using that $c$, then the actual count for the current target sum would be

$$
x = |C_1| + |C_2| + |C_3| - (|C_1 \cap C_2| + |C_1 \cap C_3| + |C_2 \cap C_3|) + |C_1 \cap C_2 \cap C_3|
$$

which withouth a doubt, is expensive to compute.

## Actual Solution

The solution turns out to be very simple. Consider the same model as before but now we start filling up the box by each coin individually. That is, for each boxes, we try our best to fill it in with only one type of coin. Then in the next loop, we'll do it again with another coin. In a way, for each coin, you're kinda building a postfix of sums which then you just add the current coin to.

Let's generalise: Assume that our boxes have already been filled with coins $c_1$ to $c_n$ and that each boxes have all possible combinations of sums. When I put in a new coin $c_{n+1}$ for a particular target sum of $x$, I first check the bucket for target $x - c_{n+1}$ which contains yet another combinations of sum and we have to combine with the sums in target $x$. Note that if we're currently on $x$, then $x-c_{n+1}$ is assumed to have all possible distinct sums. Also, I'd like to just emphasise here that all coins are distinct.

First, using only our current set of coins, all of the sums present must be all possible sums because the only missing coin is $c_{n+1}$ and the only possible way for it to make it up to $x$, would be to know all possible way of summing $x - c_{n+1}$ with $c_{n+1}$ included.

Then, are they all distinct? The previous sums in box $x$ are assumed to be and the incoming one can't have any overlaps since the postfix (i.e. in $c_{n+1} + ...$, the '...' part) is distinct by assumption.

Hence, our assumption is still there throughout the process.

# Implementation

```python
n, x = [int(x) for x in input().split()]
c = [int(x) for x in input().split()]

MOD = 10**9 + 7


num_of_ways = [1] + [0 for _ in range(x)]
for coin in c:
    for i in range(coin, x + 1):
        num_of_ways[i] = (num_of_ways[i - coin] + num_of_ways[i]) % MOD

print(num_of_ways[-1])
```

# Reference

1. https://cses.fi/problemset/task/1636

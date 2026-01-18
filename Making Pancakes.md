# Problem

> You have to make n ≥ 1 pancakes using a skillet that can hold only two pancakes at a time. Each pancake has to be fried on both sides; frying one side of a pancake takes 1 minute, regardless of whether one or two pancakes are fried at the same time. Design an algorithm to do this job in the minimum amount of time. What is the minimum amount of time as a function of n?

# Solution

Consider that if $n = 1$, then you need two minutes. If $n$ is an even number, then you can get $n$ pancakes after $n$ minutes.

What if $n$ is odd? Say $n = 3$. Then you can actually get it in $3$ minutes. The idea is that in a way, the fryer can output something along the line of 1 pancake a minute (2 pancakes after 2 minute). The idea is to wonder if this hold true as well if $n = 3$.

In order to do this, we need to fry 6 sides in 3 minutes where we can get 2 sides per minute. One way is to first fry one side of 2 pancakes for a minute (+1) and then swap one out with another one and flip the other one. After the 2nd minute (+1), one pancake is done and we put in the half-cooked one and of course flip the other one. After one more minute (+1), we'll have 3 cooked pancake and we only need $3$ minutes.

As we need 3 minutes to cook 3 pancakes, if $n$ is odd, we can subtract 3 pancakes to make it even again which will take $n - 3$ minutes. So, overall, if $n>1$, it will take $n$ minutes to cook $n$ pancakes.

$n$ minutes is actually the optimal time as well. Consider that we have $2n$ sides and our fryer can at best fry $2$ sides of a pancake at a time. We would need at least $\frac{2n}{n} = n$ minutes.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

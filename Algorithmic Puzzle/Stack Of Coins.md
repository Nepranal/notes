# Problem

> There are 10 stacks of 10 identical-looking coins. All of the coins in one of these stacks are counterfeit, and all the coins in the other stacks are genuine. Every genuine coin weighs 10 grams, and every fake weighs 11 grams. You have an analytical scale that can determine the exact weight of any number of coins. What is the minimum number of weighings needed to identify the stack with the fake coins?

# Solution

We'll first label the stack from 1 to 10. Then from the label 1, we'll take 1 coin, from the 2nd, we'll take 2, and so on. After this we will weigh them together.

The expected total sum would be $(1 + 2 + \dots + 10)10 = 550$ grams. However, since some of the coins may be fake, what we find instead is a number bigger than $550$. There's something interesting about this extra weight. It will always be $11n$ where $n$ is the number of coins. Since we know the expected weight and the deficit after one weighing, we can figure out to see how many coins are fake in our scale. But, since by looking at the stack of number of coins can tell us which tower it comes from, we would only need $1$ weighing.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

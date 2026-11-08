# Problem

> There are 10 stacks of 10 identical-looking coins. All of the coins in one
of these stacks are counterfeit, and all the coins in the other stacks are
genuine. Every genuine coin weighs 10 grams, and every fake weighs 11
grams. You have an analytical scale that can determine the exact weight of
any number of coins. What is the minimum number of weighings needed
to identify the stack with the fake coins?

# Solution

You can identify the fake coins by only a single weighing. Let's label the stacks from 1 to 10. From the 1st stack we'll take 1 coin, from the 2nd we'll take 2, and so on and put them all on top of the scale

Let's write down the sum and let's assume the stack of the fake coin is the 4th stack

$$
1*10 + 2*10 + \dots + 4*(10+1) + \dots +10*10
$$

We can rewrite this as:

$$
1*10 + 2*10 + \dots +10*10 + 4 = S + 4
$$

where $S = 550$ is the weight if there were no fake coins. Notice that the index of the assumed stack of fake coins shows up in the final expression

We can then conclude, if we take the sum of our configured weighing and subtract it with 550, we'll get the label of our fake stack of coins


# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.
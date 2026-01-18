# Problem Statement

> You are given an integer n. On each step, you may subtract one of the digits from the number.
> How many steps are required to make the number equal to 0?

# Solution

What we'll have is some boxes labelled 1 to $x$ where x is the target number. Each box is assumed to have the lowest number of step to turn it into 0.

Then, going from 1 to $x$, for each box, we'll get it's digit $d$ and checkout box $x - d$. The idea here is that, the smallest way must be the smallest step created by subtracting the number with it's digit + 1.

# Implementation

```python
n = int(input())


num_of_ways = [0]
for i in range(1, n + 1):
    num_of_ways.append(float("inf"))
    for c in str(i):
        num_of_ways[i] = min(num_of_ways[i], num_of_ways[i - int(c)] + 1)

print(num_of_ways[-1])
```

# Reference

1. https://cses.fi/problemset/task/1637

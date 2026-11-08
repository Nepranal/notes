# Problem Statement

> You know that an array has n integers between 1 and m, and the absolute difference between two adjacent values is at most 1.
> Given a description of the array where some values may be unknown, your task is to count the number of arrays that match the description.

# Solution

Assume that we have a 2D array `arr` where `arr[i][j]` tells you how many arrays there are if the value at index `i` has value `j`.

Now, we'll consider this problem element by element. Assume that for an array of length `n`, we have `arr[n - 1][j]` for all `j`. That is, if we only want to know how many variations the array of length `n`, we'd just sum all possible `j` values of `arr[n-1]`. What will happen if we add one more element?

If that element is not a blank, with a value `v`, then it can only have `j` = `v`. As there can be a difference of at most 1, we can only consider `arr[n - 1][v]`, `arr[n - 1][v - 1]`, `arr[n - 1][v + 1]` and take their sum. That is

```python
arr[n][v] = arr[n - 1][v] + arr[n - 1][v - 1] + arr[n - 1][v + 1]
```

If the element to be added is a variable, then we'll have to consider all possible values it can be. So it's actually quite similar to before but now with a loop:

```python
for v in values:
    arr[n][v] = arr[n - 1][v] + arr[n - 1][v - 1] + arr[n - 1][v + 1]
```

In actual implementation, you'll have to be a little careful with boundary conditions.

What I have shown here is the recursive argument. As you try to implement, what you'll find is that the base case kinda requires special handling although, not so different.

If the first value is free, then all `j` can be set to 1 since it can be any of those values. If not, then only set `arr[0][v] = 1` and move on.

# Implementation

```python
n, m = [int(x) for x in input().split()]
arr = [int(x) for x in input().split()]

MOD = 10**9 + 7

dp = [[0 for _ in range(m + 2)] for _ in range(n)]

# 0th index case
if arr[0] == 0:
    for j in range(1, m + 1):
        dp[0][j] = 1
else:
    dp[0][arr[0]] = 1

for i in range(1, n):
    if arr[i] == 0:
        for j in range(1, m + 1):
            dp[i][j] = (dp[i - 1][j - 1] + dp[i - 1][j] + dp[i - 1][(j + 1)]) % MOD
    else:
        dp[i][arr[i]] = (
            dp[i - 1][arr[i]] + dp[i - 1][arr[i] - 1] + dp[i - 1][(arr[i] + 1)]
        ) % MOD
print(sum(dp[-1]) % MOD)
```

# References

1. https://cses.fi/problemset/task/1746
2. https://www.geeksforgeeks.org/competitive-programming/cses-solutions-array-description/

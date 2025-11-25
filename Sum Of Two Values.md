# Problem

> You are given an array of n integers, and your task is to find two values (at distinct positions) whose sum is x.

# Model

A naive solution would be to find all possible 2 pair of numbers from the array. This would be $O(n^2)$

Though, is it true that this is as fast as we can make it? Not at all. Consider that the first number we choose could be any other number in the array. However, the 2nd number is determined once the first one is picked i.e. $a_2 = x - a_1$. If the array is sorted, the search for $a_2$ can be done with binary search. This would lead to $O(nlg(n))$

Turns out, we can push this even further with a sorted array. Say we have picked 2 numbers at the edges of $arr$ with indices 0 and $N - 1$ where $N$ is length of $arr$. Also say that $arr$ is sorted ascendingly and let $a_i = arr[i]$. If the sum $s = a_0 + a_{N-1}$ is bigger than the target $x$, we can get rid of the rightmost element since it will never be part of a solution. Any other number summed with $a_{N-1}$ will always be bigger than $x$. You can make a similar argument with $s < x$. You can keep doing this until either the solution is found or the array is has $\le1$ element left

# Implementation

```python
n, x = [int(x) for x in input().split()]
arr = sorted([(int(x), i + 1) for i, x in enumerate(input().split())])

l = 0
r = len(arr) - 1
sol = None
while l < r:
    s = arr[l][0] + arr[r][0]
    if s == x:
        sol = (arr[l][1], arr[r][1])
        break
    if s < x:
        l += 1
    else:
        r -= 1
if sol is None:
    print("IMPOSSIBLE")
else:
    print(sol[0], sol[1])
```

# Reference

https://cses.fi/problemset/task/1640

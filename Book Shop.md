# Problem Statement

> You are in a book shop which sells n different books. You know the price and number of pages of each book. You have decided that the total price of your purchases will be at most x. What is the maximum number of pages you can buy? You can buy each book at most once.

# Solution

First, let's assume we have an array `arr` and each index `i` of `arr` stores the most number of pages that cost at most `i`. Let's add another condition that it does so for books 1 to $k$.

Now, let's say we're considering another book with `p` pages. At a particular price point `x`, the only other price point that we can consider will be $x - p$ because anything higher may cost too much (if it doesn't, it would have at least as much pages) and anything too low would have pages less than or equal pages at price point $x - p$.

But is this optimal for our book? Well, for a price that's at most $x$, and the fact that our book could only be used once, it must be that whatever rest of books price must sum to at most $x-p$. And for reasons mentioned, it must be at most $x-p$. By our assumption, `arr[x - p]` contains the most optimal number of pages for at most it's price.

This whole thing is basically a recursive argument. I've just shown that if `arr` is optimal for book 1 to k, then it can also be made optimal for book 1 to k + 1. The base case can be trivially achieved

# Implementation

```python
n, x = [int(x) for x in input().split()]
costs = [int(x) for x in input().split()]
pages = [int(x) for x in input().split()]

arr = [0 for _ in range(x + 1)]
for k, c in enumerate(costs):
    for i in range(x, c - 1, -1):
        arr[i] = max(arr[i - c] + pages[k], arr[i])
print(arr[-1])
```

# References

1. https://cses.fi/problemset/task/1158
2. https://www.geeksforgeeks.org/competitive-programming/cses-solutions-book-shop/

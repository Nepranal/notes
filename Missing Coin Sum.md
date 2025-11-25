# Problem

> You have n coins with positive integer values. What is the smallest sum you cannot create using a subset of the coins?

# Model

We can just try the values one-by-one starting with 1. This would require the array to have 1. Then, the next smallest number would be 2. Either the array has one more 1 or it has a 2. At 2, what's the next smallest number that our current set cannot create? Mind you that it may not be 3 because if we add 2, and we know that before 2 we can make 1, logically we should be able to make 3

Let's generalize a little bit. Assume for a second that our current array, $arr_{new}$ can only get values from $[1, S - 1]$ where $S$ is the smallest value $arr_{new}$ cannot create. We'll be creating $arr_{new}$ from original array $arr_{old}$ by taking the smallest value first from $arr_{old}$.

Now, let's add a new value $n$ from $arr_{old}$ to $arr_{new}$. If $S - n < 0$ then the sum is indeed the smalllest we cannot create because we know that without $n$ we cannot create it and with $n$ it requires a subset of the current array that sums to a negative value which is smaller than our assumed smallest value i.e. '1'. Adding another value won't help since it will be bigger than or equal to $n$.

Now, what if we can indeed get the smallest sum. Well, then we add $n$ to $arr_{new}$ and compute the next smallest sum. say next smallest sum is $S'$. The next smallest number, you can try to find this by messing around, is $n + S$. This is because, we know that $n + (S - 1)$ can be represented but to create $n + S$, you'll need to allocate $n$ in $S$'s creation and hope that you can create another $n$ with what's left. This is impossible. If you can do this, you'll violate our assumption that the array without $n$ cannot make $S$.

# Implementation

```python
n = int(input())
arr = sorted([int(num_str) for num_str in input().split()])

smallest_sum = 1
for a in arr:
    if smallest_sum - a < 0:
        break
    smallest_sum += a
print(smallest_sum)

```

# Reference

https://cses.fi/problemset/task/2183

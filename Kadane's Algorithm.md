# Introduction

Kadane’s algorithm deals with the problem of finding between all possible contiguous sub-array, the biggest sum. In code, it is written as such:

```python
def max_subarray(numbers):
	"""Find the largest sum of any contiguous subarray."""
	best_sum = float('-inf')
  current_sum = 0
	for x in numbers:
	      current_sum = max(x, current_sum + x)
	      best_sum = max(best_sum, current_sum)
	return best_sum
```

In words, it basically just sums every element it sees, check if the resulting sum is bigger than current, and occasionally take snapshot of that sum storing it in `best_sum`.

# Recursive Argument

Assume that you know the best sum from an array of size n. Now, add one element to the back and find the biggest sum. What you’ll notice is that any new solution must involve the last element since every other contiguous sub-array (except for the actual solution itself) will be beaten by the assumed solution. This means, all solution must be of the form

- [1, …, n + 1]
- [2, …, n+1]
- …
- [n, n+1]

i.e. To find the solution with the last element, you just need to find the biggest of these numbers.

# Implementation

The implementation reflects the recursive argument. `best_sum` actually keeps track of the maximal sum between 1 to n+1. You can try some cases to see it for yourself.

# Reference

https://en.wikipedia.org/wiki/Maximum_subarray_problem

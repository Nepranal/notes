# Problem Statement

Here's the problem statement:

> You are given a list of n integers, and your task is to calculate the number of distinct values in the list.

# Using Sorting

You can sort the given array and then you can imagine the array having groups of numbers within them as such:

![](sources/Distinct_Numbers/img.png)

Then you can just count the number if groups within the array to get the answer.

```python
n = int(input())
arr = sorted([int(x) for x in input().split()])

count = 1
for i in range(1, len(arr)):
    if arr[i] != arr[i - 1]:
        count += 1
print(count)
```

# Using A Map

There exist a more straightforward solution. Consider that when you go trough the original array, you pick each number and memorize that number. If you can memorize such a number, and know that you have memorized such a number without having to look trough all the numbers you have memorized (i.e. O(1) access), then you can figure out the amount of distinct numbers within the array by simply checking all the numbers you have memorized potentially more effiecient than the 1st approach.

To achieve this however, you'll need to use the number, and only the number, to compute something unique and store it in such a way that you can access it based on the computation. If it rings any bells, then you can look at hashing. On python, we can do this very simply with dictionaries:

```python
n = int(input())
d = dict({})
for x in input().split():
    d[x] = 1
print(len(d.keys()))
```

# Reference

https://cses.fi/problemset/task/1621

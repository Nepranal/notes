# Problem

> Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target. You may assume that each input would have exactly one solution, and you may not use the same element twice. You can return the answer in any order.

# Solution

The thing here is to notice that once you decided on an entry from the array, say `x`, you only need to make sure the number `target - x` also exists within the array.

Putting it that way, it almost feels like a searching problem. And in fact, you can technically do that: sort the array, and do binary search on the array on the number you'd like to find.

```python
def binary_search(self, arr, target, i):
        left = 0
        right = len(arr) - 1
        while left <= right:
            mid = (left + right)//2
            if arr[mid][1] == target and mid != i:
                return mid
            if arr[mid][1]>target:
                right = mid - 1
            else:
                left = mid + 1
        return None

def twoSum(self, nums: List[int], target: int) -> List[int]:
    arr = [(i,n) for i,n in enumerate(nums)]
    arr.sort(key=lambda x: x[1])
    for i, num in enumerate(arr):
        index = self.binary_search(arr, target - num[1], i)
        if index is not None:
            return [num[0], arr[index][0]]
    return [None, None] # Just for completeness
```

This looks complicated because we want the indexes (in this particular version of the problem)...

On the sorting solution (sorted ascendingly), one more way you can do this is to have two pointer, each located at either ends of the array. If the sum of the two pointers are bigger than the target, then move the right pointer. Move the lef one if the sum is smaller than the target.

The idea here is that if the sum is indeed bigger, then no matter what second number you pick, none of it will ever work since the sum will always be too big. Similarly if the sum was smaller. You keep doing this until you find something or the pointer are pointing to the same index or they have passed one another.

```python
def twoSum(self, nums: List[int], target: int) -> List[int]:
    arr = [(i,n) for i,n in enumerate(nums)]
    arr.sort(key=lambda x: x[1])

    left = 0
    right = len(arr) - 1
    while left<right:
        s = arr[left][1] + arr[right][1]
        if s==target:
            return [arr[left][0], arr[right][0]]
        if s>target:
            right-=1
        else:
            left+=1
    return [None, None]
```

Yet another solution is to use hashmap. This would be the fastest solution. Remember that we only need to know if the other pair exist. So, what we can do is to 'remember' the numbers we have seen so far. If a pair does exist, we would have seen the first of the pair when we get to the latter pair.

```python
def twoSum(self, nums: List[int], target: int) -> List[int]:
    d = dict({})
    for i,n in enumerate(nums):
        leftover = target - n
        if leftover in d:
            return [i, d[leftover]]
        d[n] = i
    return [None, None]
```

# References

1. https://leetcode.com/problems/two-sum/description/
2. https://www.youtube.com/watch?v=Z_c4byLrNBU

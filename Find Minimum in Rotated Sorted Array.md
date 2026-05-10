# Problem

> Suppose an array of length n sorted in ascending order is rotated between 1 and n times. For example, the array nums = [0,1,2,4,5,6,7] might become:
>
> [4,5,6,7,0,1,2] if it was rotated 4 times.
> [0,1,2,4,5,6,7] if it was rotated 7 times.
> Notice that rotating an array [a[0], a[1], a[2], ..., a[n-1]] 1 time results in the array [a[n-1], a[0], a[1], a[2], ..., a[n-2]].
>
> Given the sorted rotated array nums of unique elements, return the minimum element of this array.
>
> You must write an algorithm that runs in O(log n) time.

# Solution

This problem is actually just a variation of finding the boundary value in an array. Consider a function `f(x) = x<=arr[-1]`. Since our array is rotated but is still sorted, what we will find is that the array will be split into 2 sides: one filled with `T` and the other with `F`. Our task is then to just find the first occurance of this `T`.

```python
def findMin(self, nums: List[int]) -> int:
    left = 0
    right = len(nums) - 1

    min_val = None
    while left<=right:
        mid = (left+right)//2
        if nums[mid] <= nums[-1]:
            min_val = nums[mid]
            right = mid - 1
        else:
            left = mid + 1
    return min_val
```

# References

1. https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/
2. https://www.youtube.com/watch?v=Z_c4byLrNBU

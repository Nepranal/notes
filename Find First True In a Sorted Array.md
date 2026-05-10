# Problem

> An array of boolean values is divided into two sections: The left section consists of all false, and the right section consists of all true. Find the First True in a Sorted Boolean Array of the right section, i.e., the index of the first true element. If there is no true element, return -1.

# Solution

This is a usual problem in exploiting binary search. The idea is to just keep memo of the indexes you have so far. Once you have seen your target, you can exclude any parts of the array after that since you will not be using them for your search

```python
def find_boundary(arr: list[bool]) -> int:
    left = 0
    right = len(arr) - 1
    index_seen=-1
    while left<=right:
        mid = (left+right)//2
        if arr[mid] == True:
            index_seen = mid
            right=mid-1
        else:
            left = mid + 1
    return index_seen
```

# References

1. https://www.youtube.com/watch?v=Z_c4byLrNBU
2. https://algo.monster/problems/binary_search_boundary

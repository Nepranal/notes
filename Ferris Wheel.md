# Problem Statement

> There are n children who want to go to a Ferris wheel, and your task is to find a gondola for each child.
>
> Each gondola may have one or two children in it, and in addition, the total weight in a gondola may not exceed x. You know the weight of every child.
>
> What is the minimum number of gondolas needed for the children?

# Problem Model

A way to approach the problem is to consider at first the different absolute cases you may face. For instance, the cases where a child cannot be sat with another child. If x = 10, this would happen when the child has a weight of exactly 10. Or 9, or 8, or basically any weight > 10. In general, if the child has a weight > x/2, that child will be alone in a gondola.

You could've also inferred that if the child has a weight of <= x/2, then that child can be grouped with anyone within the same category and any ordering will work just fine.

Now, we essentially have 2 groups, one > x/2 and the other <= x/2. To have the minimum amount of gondolas, you'll want to group kids from both groups (if both exists) as much as you can.

# Implementation

```python
n, x = [int(x) for x in input().split()]
a = sorted([int(num_str) for num_str in input().split()])

count = 0
ptr1 = 0
ptr2 = n - 1
while ptr1 <= ptr2:
    if ptr1 == ptr2 or a[ptr2] + a[ptr1] <= x:
        ptr1 += 1
    ptr2 -= 1
    count += 1
print(count)
```

# Reference

[Ferris Wheel](https://cses.fi/problemset/task/1090)

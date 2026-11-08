# Problem

> Given two sorted arrays nums1 and nums2 of size m and n respectively, return the median of the two sorted arrays.
>
> The overall run time complexity should be O(log (m+n)).

# Solution

The simplest solution to this problem is to merge the 2 arrays and find their mid point. That however is a linear time operation instead of a logarithmic one

The logarithmic time complexity suggests some sort of binary search is in order. A way we can do so is to compare the middle element of both arrays and iteratively remove part of the arrays until a single element is left: the median. But, how can we remove portions of the array without removing the median?

Say we have 2 sorted arrays $a, b$ with sizes $n_a,n_b$ respectively. Now, let's say we would like to find the part of the array in which that $k$th smallest element is definitely not in. Also say that we split both arrays into 2 partition at index $a_m$ and $b_m$. The 2 partition we'll label to be $a_l$ and $a_r$ (left and right) and that $a[a_m] \isin a_r$ and similarly with $b$. Let's also say that $b[b_m] \geq a[a_m]$

Here, maybe it will be simpler if we pose the approach to be a fancy way to do merging (or a fancy way to avoid doing so). Imagine we have a sorted array $I$ with the elements of $a$ and $b$ i.e. $I = a \cup b$. What we know right now is that $a[a_m]$ is at most put at index $a_m + b_m$ in $I$ while $b[b_m]$ is at least located at $a_m + b_m + 1$ (just right after).

Now, the $k$th smallest element will be at index $k - 1$ in $I$. Hence, if $k - 1 \gt a_m + b_m $, then we can safely remove $a_l$ since $k$ won't be there. If $k < a_m + b_m$, then we can remove $b_r$ since $k$ won't be there either. And weirdly enough if $k = a_m + b_m$, then we can remove both $a_l$ and $b_r$ though you'll have to keep $a_m$.

To be efficient, we pick $a_m, b_m$ to be the midpoint of both arrays i.e. $a_m = \lfloor n_a/2 \rfloor, b_m = \lfloor n_b/2 \rfloor$

Just one last thing. As we remove $a_l$, we will have to update the value of $k$ and subtract it by the elements we have removed. Namely $k := k - a_m$.

The solution specifies how to get the $k$th biggest element. If the number of element is odd, then we only need to get $k = \lfloor \frac{n_a + n_b}{2} \rfloor + 1$. If even, we also get $k = \lfloor \frac{n_a + n_b}{2} \rfloor$

```python
def f1(self, va, vb, m_a, m_b, k):
        updates = [va, vb]
        if k > m_a + m_b:
            updates[0] = m_a + 1
        elif k < m_a + m_b:
            updates[1] = m_b - 1
        else:
            updates[1] = m_b - 1
            updates[0] = m_a
        return updates[0], updates[1]

def f2(self, a, b, k):
    l_a, r_a = 0, len(a) - 1
    l_b, r_b = 0, len(b) - 1
    while l_a <= r_a and l_b <= r_b:
        m_a, m_b = (r_a + l_a) // 2, (r_b + l_b) // 2
        if a[m_a] > b[m_b]:
            l_b, r_a = self.f1(l_b, r_a, m_b, m_a, k)
        else:
            l_a, r_b = self.f1(l_a, r_b, m_a, m_b, k)
    if l_a > r_a:
        return b[k - l_a]
    return a[k - l_b]

def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
    N = len(nums1) + len(nums2)
    k = N // 2
    median = self.f2(nums1, nums2, k)
    if N % 2 == 0:
        median = (median + self.f2(nums1, nums2, k - 1)) / 2
    return median
```

A note on the implementation: you'll notice that we don't change $k$ at all until the very end. This is not necessary because the next comparison would be at some point within the 2 arrays and one of the arrays would have it's index be shifted by $a_m$ amount (to make the other array starts at 0). This constant shift with the point at exactly one of the arrays and $k$ cancels out during the comparison

We can make the example more explicit. Say we have $a_l = a_m$ and that $k := k - a_m$ so in the next iteration we have indexes of $a$ be shifted to the left by $a_m$. The next 2 points we pick will be $b_m$ (b didn't change) and $a'_m - a_m$ (since $a'_m$ was calculated with the new bound). As you can see, the shifting terms cancels out because of array $a$

The fact that $k$ is only subtracted by one of the elements should also have felt weird since $k$ is supposed to be subtracted by all elements we've removed at the left side and this left side could come from either arrays.

Before getting into it, first is that the left terms ($a_l, b_l$) specifies how many left elements have been removed. This is an invariant of the algorithm since we move the left pointers iff we remove the left elements. Now, the index term should then be $k - a_l - b_l$ since both arrays may have it's left elements removed at some point

The calculation would be true if the arrays start from 0 which they may not. In general, since the array starts at index $p_l \geq 0$, we'll have to shift every element by subtracting them with $p_l$. This will give our indexes as $[p_l - p_l, p_r - p_l]$ $p_r$ being the right pointer of our array. We can now use our index

Notice however, to find this position in our original array, we can just simply add $p_l$ back. This will in effect make our index be $k - l_a - l_b + p_l$. If we're talking about $a$, then it will be $k-l_a-l_b+l_a = k - l_b$. Same arguement follows with $b$ with $p_l = l_b$

# Faster Solution

If you notice, we're only getting rid of half of the halves everytime. Is there a better solution?

The idea is similar to our previous one in the sense that we're partitioning our arrays into halves again. The idea is to have earlier halves to have $\lfloor (m+n)/2 \rfloor$ elements.

This time, let's pick a middle point of array $a$ at index $\lfloor m/2 \rfloor$. We no longer need to split $b$ into halves but rather we only need to find a point such that wherever we split $b$, the sum of elements of the lower halves of $b$ and $a$, will both sums up to $\lfloor (m+n)/2 \rfloor$.

If $\max(a_l) \leq \min(b_r)$ and $\max(b_l) \leq \min(a_r)$ then we would have partitioned our arrays perfectly. In which case, the median is either $\max(a_l,b_l)$ or $(\max(a_l,b_l) + \min(a_r,b_r))/2$.

If you didnt find this however, you will need to find a new point in $a$. If $\max(a_l) \gt \min(b_r)$, that means we need to consider the earlier halves of $a$. If $\max(b_l) \gt \min(a_r)$ then we need to move to the latter halves.

Notice that if neither $a<b$ or $a\geq b$, then we will find an index that satisfies this eventually. Otherwise, we can just consider the mid point directly.

```python
def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
    if len(nums1) > len(nums2):
        nums1,nums2 = nums2,nums1

    Na,Nb = len(nums1), len(nums2)
    N = (Na + Nb)//2

    if len(nums1) == 0:
        return nums2[N] if (Na+Nb)%2!=0 else (nums2[N] + nums2[N-1])/2

    nums1.extend([float("inf"),float("-inf")])
    nums2.extend([float("inf"),float("-inf")])
    l,r = 0, Na - 1
    while True:
        m = (l + r)//2
        k = N - m - 1

        print(m,k)
        if nums1[m] <= nums2[k] and nums2[k - 1] <= nums1[m + 1]:
            if (Na+Nb)%2 != 0:
                return min(nums2[k], nums1[m + 1])
            return (min(nums2[k], nums1[m+1]) + max(nums2[k-1], nums1[m]))/2

        if nums1[m] > nums2[k]:
            r = m - 1
        if nums2[k - 1] > nums1[m + 1]:
            l = m + 1
```

# References

1. https://leetcode.com/problems/median-of-two-sorted-arrays/editorial/?envType=problem-list-v2&envId=plakya4j

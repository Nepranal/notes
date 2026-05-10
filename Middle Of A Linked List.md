# Problem

> Given the head of a singly linked list, return the middle node of the linked list.
>
> If there are two middle nodes, return the second middle node.

# Solution

You can obviously count the number of nodes first, $N$, and then count to $\lceil\frac{N}{2}\rceil$ but there's a better approach. Consider you have 2 pointers and have one of the pointer go twice as fast as the other one. This way when the 2nd pointer made it to the end, the first one would be at the middle. Sounds simple, but we'll have to modify it a bit so it satisfies our problem statement.

What we can do is to make it so that when `p1 == 1`, `p2 == 1`. When `p1 == 2`, `p2 == 3` and `p1 == 3`, `p2 == 5`. In other words, `2*p1 - 1 == p2 `. Here when the number of nodes are odd, we'll have `p1` at `(p2 + 1)/2` which is the mid point $(p_2/2 + 1/2)$.

`p2` should not be an even number. But if it is, then it will point at a `null` node at position `p2 + 1` and `2*p1 == p2 + 2`. This means `p1 + p1` will bring you to the next even number after `p2`. This is what the problem would like for us to do

```python
def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
    ptr1 = head
    ptr2 = head
    while ptr2 and ptr2.next:
        ptr1 = ptr1.next
        ptr2 = ptr2.next.next
    return ptr1
```

# References

1. https://www.youtube.com/watch?v=Z_c4byLrNBU
2. https://leetcode.com/problems/middle-of-the-linked-list/

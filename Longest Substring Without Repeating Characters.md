# Problem

> Given a string s, find the length of the longest substring without duplicate characters.

# Solution

A way to do so is to use a sliding window. We'll expand the window until the string is no longer unique. To keep track of the characters we have so far, we can use a hashset / hashmap. When we expanded it so big such that the characters inside are no longer unique, we'll shorten the window by moving the left pointer.

What we're doing is to effectively figure out the longest unique substring for a particular character at a particular index. When we move our left pointer to the right, any of the characters we have left behind would never be part of a longer unique substring.

```python
def lengthOfLongestSubstring(self, s: str) -> int:
    left = 0
    right = 0
    chars = set({})

    max_length = 0
    while right < len(s):
        c = s[right]
        while c in chars:
            chars.remove(s[left])
            left+=1
        chars.add(c)
        max_length = max(max_length, right - left+1)
        right+=1
    return max_length
```

# References

1. https://leetcode.com/problems/longest-substring-without-repeating-characters/description/
2. https://www.youtube.com/watch?v=Z_c4byLrNBU

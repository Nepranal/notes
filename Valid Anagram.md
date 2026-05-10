# Problem

> Given two strings s and t, return true if t is an anagram of s, and false otherwise.

# Solution

If `t` is an anagram of `s`, then `t` would just a permutation of `s`. The number of letters and the available letters must be the same.

An approach then would to just be counting. A quick way to count is to simply use a dictionary and make sure all of the letters matches

```python
def isAnagram(self, s: str, t: str) -> bool:
    d = dict({})

    n = len(s)
    if n != len(t):
        return False

    for i in range(n):
        c_s = s[i]
        c_t = t[i]

        if c_s not in d:
            d[c_s] = 0
        if c_t not in d:
            d[c_t] = 0

        d[s[i]] += 1
        d[t[i]] -= 1

    for k in d:
        if d[k] != 0:
            return False
    return True
```

This is one way to do so. The idea is that `s` and `t` plays opposing role but if they were anagrams, they should cancel one another

# References

1. https://leetcode.com/problems/valid-anagram/
2. https://www.youtube.com/watch?v=T0u5nwSA0w0

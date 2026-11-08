# Problem

> In a movie festival n movies will be shown. You know the starting and ending time of each movie. What is the maximum number of movies you can watch entirely?

# Solution

The idea is to be greedy and take whatever you can. You can do this because it's the best decision you can make as you go through. The setup to this problem is pretty similar to `Restaurant Customers` problem.

But, it's very important you sort the tuple by the exit time. You can consider that if you sort by beginning time, you might run into one that start earliest and also ends the latest. In this case, it should be straigthforward to see the situations in which you will lose.

# Implementation

```python
n = int(input())
intervals = []
for _ in range(n):
    arr = [int(x) for x in input().split()]
    intervals.append((arr[0], arr[1]))
intervals.sort(key=lambda i: i[1])

max_num = 0
end_time = -1
for i in intervals:
    if i[0] >= end_time:
        end_time = i[1]
        max_num += 1
print(max_num)
```

# Reference

https://cses.fi/problemset/result/15454317/

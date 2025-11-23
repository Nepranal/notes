# Problem Statement

> You are given the arrival and leaving times of n customers in a restaurant.
>
> What was the maximum number of customers in the restaurant at any time?

# Problem Model

First, you can imagine the intervals to be all put on a graph. Then, imagine going trough the interval from the beginning and keeping count. When you enter someone's interval, you increment the count and decrement when you hit someone's exit time. The maximum of number you counted up to would then be the maximal number of customer.

There's a remark that I would like to make here in that when I was first faced with this problem, I consider it to be finding the interval of time with the most overlap. I still have no idea how to do this but one thing I can say is that it's not straightforward to do. So, do be careful as to how you model the problem

# Implementation

```python
n = int(input())
enter_times = []
exit_times = []
for _ in range(n):
    arr = [int(x) for x in input().split()]
    enter_times.append(arr[0])
    exit_times.append(arr[1])
enter_times.sort()
exit_times.sort()

max_num = 0
exit_ptr = 0
enter_ptr = 0
s = 0
while exit_ptr < n and enter_ptr < n:
    if exit_times[exit_ptr] >= enter_times[enter_ptr]:
        s += 1
        enter_ptr += 1
    else:
        s -= 1
        exit_ptr += 1
    max_num = max(max_num, s)
print(max_num)
```

# Reference

https://cses.fi/problemset/task/1619

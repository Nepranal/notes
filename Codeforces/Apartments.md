# Problem Statement

> There are n applicants and m free apartments. Your task is to distribute the apartments so that as many applicants as possible will get an apartment.
>
> Each applicant has a desired apartment size, and they will accept any apartment whose size is close enough to the desired size.

# Problem Model

I imagine the problem to look something like this:
![alt text](<sources/Apartments/Untitled Diagram.drawio (8).png>)

Each green ellipse represents the customer's buying price. The center of the ellipse is the customer exact price and you can imagine how the upper and lower bound of the price. The vertical lines / poles represent the selling price of the apartments. What you have to do here is straightforward, make it so that, as much as possible, each pole to have an ellipse where each ellipse can only have 1 pole.

The problem become difficult when you consider that a pole may overlap with multiple ellipses and that if you take one of the ellipses, you might got rid of the possibilities of other poles to use that ellipse. How can we make this decision? Any other special cases we need to consider?

Well, let's just start from a pole that won't cause any controversies. Consider the poles at the edge. These poles are quite nice because they won't have that sharing problem. If you take the ellipse with the smallest centre point that contains the pole, we won't face any consequences. If it doesn't overlap with anyone else, we're safe. If it does, then 2 things will happen:

<ol>
    <li>There no more ellipses in between the poles in which case, it will be best for the pole to take the ellipse</li>
    <li>There are other ellipses: then just assign the pole that ellipse since the other pole can still take the other ellipse(s)</li>
</ol>
Remember that the goal is to get rid of as many poles as possible.

\
With this, you can just process the pole one-by-one from the edge. Once you have assigned an ellipse to a pole, you can remove that ellipse and pole. Keep doing this until all poles or ellipses are gone

# Implementation

Since we're approaching this by ordering the apartment prices and the amount the customer's willing to pay, we should have 2 sorted arrays representing them. Note that the important part of this is to work from the edges so it won't matter whether you sort it ascendingly or descendingly so long as both are sorted the same way.

Then I think the rest is better to just be written in code:

```python
n, m, k = [int(x) for x in input().split()]
a = sorted([int(x) for x in input().split()])
b = sorted([int(x) for x in input().split()])

count = 0
ptr1 = 0
ptr2 = 0
while ptr1 < len(a) and ptr2 < len(b):
    if a[ptr1] >= b[ptr2] - k and a[ptr1] <= b[ptr2] + k:
        count += 1
        ptr1 += 1
        ptr2 += 1
    elif a[ptr1] < b[ptr2] - k:
        ptr1 += 1
    elif a[ptr1] > b[ptr2] + k:
        ptr2 += 1
print(count)
```

# Reference

https://cses.fi/problemset/task/1084

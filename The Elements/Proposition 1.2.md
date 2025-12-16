# Proposition

> Given a point and a line, draw another line through that point s.t. The line is of equal length to the previous line

# Method

![alt text](<../sources/The Elements/Screenshot 2025-12-12 at 7.53.02 PM.png>)

The things we're considering are the point $A$, line $BC$, and the produced line $AL$ of equal length to $BC$. We first start with

![alt text](<../sources/The Elements/desmos-graph.svg>)

By the first proposition 1.1, we can draw an equilateral triangle

![alt text](<../sources/The Elements/desmos-graph (1).svg>)

Then, all you gotta do is to draw a circle with it's centre being the new point and such that it grazes the old circle

![alt text](<../sources/The Elements/desmos-graph (2).svg>)
(The centre would be at point $D$ and grazes at $G$)

Now we basically has that image in the beginning and that it promises $AL = BC$ but how?

First we know that

$$
DG = DL\\
DG = DB + BG\\
DL = DA + AL
$$

Which leads to

$$
DB + BG = DA + AL
$$

And since

$$
AD = BD
$$

It must be that

$$
BG = AL
$$

But, $BG = BC$ since they're radius of the circle centered at $B$. This would imply

$$
AL = BG = BC
$$

$\square$

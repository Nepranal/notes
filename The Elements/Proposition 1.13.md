# Proposition

> If a straight-line stood on a(nother) straight-line makes angles, it will certainly either make two right-angles, or (angles whose sum is) equal to two right-angles.

# Proof

It's a little complicated because of how they do things but lets construct it first

![alt text](<../sources/The Elements/Screenshot 2025-12-23 at 4.31.55 AM.png>)

The 2 lines are $CD, AB$. Lets say $\angle EBD = \angle EBC = \frac{\pi}{2}$. And let $\angle DBA = \alpha, \angle EBA = \gamma, \angle ABC = \beta$. The goal is to show that $\alpha + \beta = \pi/2$.

The difficult part about this proof is that you can only work with right angles since they are what's defined. With that being the case, we know that $\beta + \gamma = \pi/2$. But then we can also write $\beta + \gamma + \pi/2 = \pi$.

Then, since $\alpha = \gamma + \pi/2$, we can add $\beta$ to both side and obtain $\alpha + \beta = \gamma + \beta + \pi/2$. From our previous calculation, we can say that $\alpha + \beta = \pi$.

# Alternative

We know that $\alpha = \gamma + \pi/2$. By looking at only the right side, we can get

$$
\gamma + \beta + \pi/2  = \gamma + \pi/2 + \beta = \pi/2 + \pi/2
$$

or in other words $\alpha + \beta = \pi$.

But I guess this probably assumes commutativity and associativity with addition.

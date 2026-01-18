# Proposition

> If two internal straight-lines are constructed on one of the sides of a triangle, from its ends, the constructed (straight-lines) will be less than the two remaining sides of the triangle, but will encompass a greater angle.

# Proof

![alt text](<../sources/The Elements/Screenshot 2025-12-29 at 9.35.19 AM.png>)

2 triangles here: $\triangle ABC, \triangle BDC$. Show that $\angle BDC > \angle BAC$ but that $AB + BC > BD + DC$.

The line $DE$ is just a continuation of line $BD$.

First, we show the angles. We can show this by applying the fact that an external angle to a triangle is bigger than any angles that are internal and opposite of it. This means that $BDC > DEC > BAE = BAC$.

For the lengths, we know that the sum of two sides of a triangle is bigger than the other side. This would mean that

$$
AB + AE > BE = BD + DE
$$

And that

$$
DE + EC > DC
$$

If we sum both of the inequalities together we will get:

$$
AB + AE + EC + DE > BD + DE + DC
$$

Since $AE + EC = AC$ and that $DE$ exist on both side, which we can remove from both sides without changing the inequality, we can then get

$$
AB + AC > BD + DC
$$

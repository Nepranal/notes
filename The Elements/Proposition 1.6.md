# Proposition

> If a triangle has two angles equal to one another then the sides subtending the equal angles will also be equal
> to one another.

Say, we have $\triangle ABC$

![alt text](<../sources/The Elements/Screenshot 2025-12-13 at 7.53.20 PM.png>)
If $\angle ABC = \angle ACB = \theta$, then $AB = AC$

# Proof

Say that it is not the case i.e. $AB \neq AC$. Also say that $AB > AC$. This means we can draw a line from $C$ to a point $D$ that lies on $AB$ such that $DB = AC$. Now, we have 2 triangles to pay attention to: $\triangle ABC, \triangle BCD$. These two shares BC as a base. We know that $\triangle ABC \neq \triangle BCD$ since $BD = AC < AB$.

Now, notice that both triangles has 2 sides which encloses an angle $\theta = \angle CBD = \angle ACB$. At $\triangle ABC$, the angle is enclosed by $AC$ and $BC$ while $\triangle BCD$ it's $DB$ and $BC$. We have matching angles and matching length which by proposition 1.4 means $\triangle ABC = \triangle BCD$. This is a contradiction $\square$.

# Problem

Find a solution for $x,y\isin\N$

$$
\sqrt{x}+y = 7\\
\sqrt{y}+x = 11
$$

Naively, this problem can lead to a rather complicated solution. Consider that you can convert one of the equation to get rid of the square root term. This however will lead to a quartic equation. If we say take the 2nd one

$$
y = (11-x)^2
$$

and substitute to the first:

$$
x = (7-(11-x)^2)^2
$$

A simpler approach exist for this problem

Consider, a different form to this problem

$$
a + b^2 = 7\\
b + a^2 = 11
$$

where $a = \sqrt{x}$ and $b = \sqrt{y}$ are both integers. This is due to the fact that if $x+\sqrt{y} \isin \N$ is an integer, than it stand to reason $\sqrt{y}\isin\N$. This is important 

If we subtract them

$$
b-a + a^2 - b^2 = (b-a)+(b-a)(a+b) = (b-a)(1+a+b) =  4
$$

A simple set of solutions we can try are: 1x4, 2x2, or 4x1 or both numbers are negative where order matters. The possible solution is due to the fact that $a,b \isin\N$. As it turns out, -1x-4 will work

$$
b-a = -1\\
1-(a+b) = -4
$$

solving the system gives $a=3$,$b=2$ which then gives $x=9, b = 4$

# References

1. https://www.youtube.com/watch?v=o7YPk3lohB0&t=14s

# The Depressed Cubic

It's this

$$
x^3 + ax + c = 0
$$

We want to find an expression for $x$.

# Binary Expansion

An approach I thought is that, if I can solve the quadratic by solving the square, then shouldn't I be able to do the same with cubics by "completing the cube".

$$
(a + b)^3 = a^3 + 3a^2b + 3ab^2 + b^3
$$

However, with "completing the square", the $b$ term cannot contain any $x$ term since that thing is supposed to be moved to the other side and be treated as a constant. With this expansion, you'll find that it's difficult to do so because we have a square term which means we will inevitably run into $x^2$ which we don't have in our original problem. We're going to need something to counteract it and in this case I think we have to use $b$.

# Factorizing Cubes

Perhaps we can factorize the equation using sum of cubes

$$
a^3 + b^3 = (a+b)(a^2-ab+b^2)
$$

We can try this

$$
x^3 + ((ax+b)^{1/3})^3 = (x+(ax+b)^{1/3})(x^2 - x(ax+b)^{1/3}+(ax+b)^{2/3})
$$

which I have no idea what to do with. Though, in any factorizing approach, it's probably not a good idea to leave out a term

$$
ab + c = 0
$$

Can't do much with this since $x$ may exist at both $a$ and $b$.

# Naive Factorization

Say perhaps

$$
x^3 + ax + b = (a' + b')(c'+d') = a'c' + a'd' + b'c' + b'd'
$$

Here, we need to have an $x$ and $x^2$ because we only have two multiples of values. So, let's say

$$
(x + b') (x^2 + d') = x^3 + xd' + x^2b' + b'd'
$$

We don't have an $x^2$ term, so we'll have to get rid of it. A way is to have have $d' = -b'x$.

$$
x^3 - b'x^2 + x^2b' - (b')^2x = x^3 - (b')^2x
$$

We can then set

$$
-(b')^2x = ax + c
$$

which is no good since then we'll have to deal with different values of $x$ separately

# Curves

# Introduction

This chapter talks in details about how curves are represented in computer graphics and about how we think of them. Most of the stuff you’ll see here will be about the parametrization of curves

# Line Parametrization

When we say ‘parametrizing a function’ we are actually describing the same function but with a different input parameter. Instead of inputting a value to get a value out of the function, you’re providing a value for the input to the function themselves.

Take for instance the following line: $f(x)=2x+1$. The points for which the function is -1 and 3 will be -1 and 1 respectively. But, what if I want the function to give -1 and 3 when the input to the function are 0 and 1 instead? Well, we could shift the values around and get what we want:

$$
f(x(t))=2t+1\\x(t)=\frac{1}{2}t-1
$$

This is an example of parametrizing which are basically composite functions. We’re literally providing a function for the input itself. From this single example, I think it’s quite clear that to parametrize a function you’ll need the input value and the output value. 

We can push this idea further. It’s a little wasteful for the fact that we already know the values that we’re using but still computing for x. Let’s just do it immediately

$$
x=\frac{1}{2}t-1\\y=2(\frac{1}{2}t-1)+1=t-1
$$

Now, at this point, y is no longer directly dependent on x but both do depend on t.

This perspective of just using a value from 0 to 1 to describe lines or function makes it seems like we don’t really care about the domain of the function itself. We really only care about certain points or interval. As if, the points themselves describe what the line is going to be. Take back that example for a second. If we ignore the line equation and just focus on the fact that I have 2 points and a value that goes from 0 to 1, it’s as if, if I specify a different set of 2 points, I would have described a different line or perhaps the same line but longer. The whole process then is parametrized by the set of points I specify.

A question then perhaps would be could we given a set of points, and the kind of polynomials we want to draw (e.g. Line, quadratic, cubics, etc.), could we get an equation that uses those points explicitly? The answer is yes but the reason to the answer is a little complicated. The linear case though is simple; just look back to how we can form linear functions from vectors. You can freely choose the starting and end points of the line and have an equation described in terms of those points.

# Bezier Curves

A natural next step from this line of thought is how can we do the same thing for stuff like quadratics, cubics, and all other higher order polynomials. There are many ways of doing it as to be shown in a little bit but one of the more very well known one is called a bezier curve.

This representation covers the whole spectrum of higher order polynomials so it has many ‘formulas’. The most commonly used of these formulas are the cubic bezier.

$$
B(t)=(1-t)^3P_1+3(1-t)^2tP_2+3(1-t)t^2P_3+t^3P_4
$$

![Screenshot 2025-10-15 at 2.52.29 PM.png](sources/Curves%202821aa5d8a0e807b9630d55e608cddaf/Screenshot_2025-10-15_at_2.52.29_PM.png)

The bezier curve formulation represents a curve as such. The points that are part of the equation are called control points. Control points can define different behaviours of the curve. Within the bezier curve representations, the end and beginning points specify the point that curve touches and the rest defines ‘bumps’ of the curve.

One thing to note is that these control points are not restricted to a 2D plane. They also work in 3D (and possibly higher dimensions whatever that means).

There are many properties, very nice properties, of the bezier curve. But seeing that we’re not dealing with the math behind these things, I find them to be quite useless at least in this very instance. So I will skip them but feel free to look them up

# Combining Curves

The higher order of bezier curves you use, the more ‘bumps’ you can make for your curves. But the computation for higher order curves also become quite expensive. A general strategy that people take then is to combine, typically we’ll take a cubic bezier function and from there stitch a bunch of them together in a fashion where the end of one cubic becomes the start of the next cubic and so on

![Screenshot 2025-10-15 at 3.00.46 PM.png](sources/Curves%202821aa5d8a0e807b9630d55e608cddaf/Screenshot_2025-10-15_at_3.00.46_PM.png)

This a curve made of 7 bezier cubics

# Partition of Unity

Now, there’s an interesting constraint that is put for these sorts of equations. That is, the coefficient of the points must all sum up to 1. This is the case as well with the different bezier equations (in fact, all of the coefficients are actually part of pascal’s triangle). This conditions as it turns out to be rather necessary. If not fulfilled, the curve will adjust and have an ‘invisible’ control points at the zero vector

![Screenshot 2025-10-15 at 3.04.14 PM.png](sources/Curves%202821aa5d8a0e807b9630d55e608cddaf/Screenshot_2025-10-15_at_3.04.14_PM.png)

This equations effectively become

$$
(1-t)^2P_0+t^2P_1+(2t-2t^2)\vec{0}
$$

In effect, the curve will bend towards the origin of the coordinate system

# General Curve Representations & B-Splines

We can generally write the rules that we have so far as the following finite sum

$$
\sum_{i}^{N}{a_i(t)P_i}\\\sum_{i}{a_i(t)} = 1
$$

where $a_i(t)$ gives out the pascal triangle coefficient. As it turns out, you can adjust $a_i(t)$ to be whatever you like so long as you satisfy the constraint. Another popular way of doing this is called B-Splines. Their definitions are omitted here, but the overall usage is kind of similar to bezier curves; both of them are defined by the order of polynomials and the most popular of these definitions are the cubics version.

Though, B-splines has a nicer property when you join them together i.e. Their joins will match in terms of 2nd derivatives i.e. $C_2$ continuous. They also differ in terms of graphics. While bezier curve guarantees the first and last control point of a segment will be touched by the curve, B-Splines avoid all points al-together. 

![Screenshot 2025-10-15 at 3.19.25 PM.png](sources/Curves%202821aa5d8a0e807b9630d55e608cddaf/Screenshot_2025-10-15_at_3.19.25_PM.png)

There are however ways to make sure the first and last points are touched (by defining extra control points). 

# Adding Weights To Control Points

There’s a neat little trick you can do with these curve representations. Consider that perhaps you want some part of curve to protrude more to a control point. It’s a little bit of a hassle to redefine the control points every-time so is there a way to do this by not changing the actual control point themselves?

A naive solution is to add weights to each of the coefficient

$$
\sum_{i}^{N}{a_i(t)w_iP_i}
$$

Since this invites the danger of having a non-unity sum of the coefficient, we can normalize the coefficients:

$$
\sum_{i}^{N}\frac{{a_i(t)w_iP_i}}{\sum_{j}^{N}{w_j}}
$$

This form is called a rationale form. Now, we can adjust, for any of the curve representations you’re using, how much bending happens at a control point.

Some note: if you make this approach with B-splines, people will call it NURBS (Non-Uniform Rational B-Splines).

# Approximating & Interpolating Curves

Curves like B-Splines, Bezier and NURBS are what we call approximating curves because they don’t actually touch all of the specified control points. However, there are situations where you’d like all of the control points to be passed through by the curve itself. These are called interpolating curves.

A very popular one is called the Catmull-Rom curves. It follows kind of similarly to the bezier curves in that it comes in order of polynomials but, instead of having the endpoints be the part that the curve touches, it’s the middle-parts instead.

![Screenshot 2025-10-15 at 3.31.02 PM.png](sources/Curves%202821aa5d8a0e807b9630d55e608cddaf/Screenshot_2025-10-15_at_3.31.02_PM.png)

This is important because we have tricks to make the endpoints to converge to the control point itself but that same trick can’t be done with intermediate control points. If you do, you can lose all the nice properties of the curve (like C1 or C2 continuity) and the curves you draw may drop in quality. Now, with the Catmull-Rom curves, since the points in-between gets the interpolation treatment, you can combine the curves together (define the edge control points to the the next segment’s intermediate control points and the current segment intermediate point as the end point of the next segment’s end control points) and still enjoy the nice properties.

Something to note about these interpolating curves, is that sometimes the way you choose the parameter at the control points may matter. For instance, with the Catmull-Rom curves, depending on how you define the parameter may make the curve intersects with itself.

![Screenshot 2025-10-15 at 3.36.30 PM.png](sources/Curves%202821aa5d8a0e807b9630d55e608cddaf/Screenshot_2025-10-15_at_3.36.30_PM.png)

- Uniform Parametrization: Equal distance, monotonically increasing parametric points. For instance, t0 happens when t = 0, t1 when t = 1, and so on
- Chordal Parametrization: Defines the parameter based on the distance between 2 control points
- Centripetal Parametrization: Based on the square root of the length

What this means and where it comes from won’t be treated right now since I’ve no clue why and the book reference is a pain in the ass to go trough. But just now that this matters

# References

https://youtu.be/6jjLSkp0Y7I?si=MXEMbU8CoO5bnhNU

https://youtu.be/l9dCeeq3X1E?si=JK823lgSnIV6b0xK
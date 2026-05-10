# Linear & Shift Invariance Systems

Linear and shift invariant systems, as the name suggests, are systems that posses the property of being linear and invariant to shifting.

Being linear here means simply that if we have an input to the system, then the output will scale linearly with the input(s). Shift invariance means that if you shift the input, the output will also be shifted by the same amount

## Ideal Lenses As An LSIS System

![alt text](<sources/LSIS and Convolution/Screenshot 2026-03-29 at 1.00.18 PM.png>)

Say the $g$ plane is the image plane and $f$ as an imaginary plane where the image formed would be in focus. In a way, we can think of $g$ as a processed version of $f$.

The relationship between these two is LSIS. If you scale $f$ i.e. makes the scene brigther, then $g$ will also gets brighter. If you move the scenes around in $f$, it will move the same amount in $g$ (assuming no magnification between the two planes). The only difference here is that $g$ would be blurry compared to $f$.

# Convolution

You can think of this as a multiplication between two function (kind of but not really). It's defined as such

$$
f(x)*h(x) = \int_{-\infin}^{\infin}f(\tau)h(x-\tau)d\tau
$$

A convolution happens at a point defined at the domains of both functions. When doing a convolution, we're taking a product of the two functions but we will first transform $h$ by flipping it (you'll see this with -$\tau$ term) and shift it to the right by $x$ amount (now you're reading the function right to left)

![alt text](<sources/LSIS and Convolution/Screenshot 2026-03-29 at 1.31.22 PM.png>)

and then you take the integral to get the convolution at $x$. Then you can imagine that if you want to get the convolution at ever possible $x$, you'll the the function $h(x-\tau)$ and slide it over $f(\tau)$ where at each $\tau$ you'll take the integral.

# Convolution Is LSIS

Let's start with linearity

$$
(af_1(x) + bf_2(x))*h(x) = \int_{-\infin}^\infin(af_1(\tau) + bf_2(\tau))h(x-\tau)d\tau\\
\,\\
= a\int_{-\infin}^\infin f_1(\tau)h(x-\tau)d\tau + b\int_{-\infin}^\infin f_2(\tau)h(x-\tau)d\tau\\
\,\\
= af_1(x)*h(x) + bf_2(x)*h(x)
$$

For shift invariance

$$
k(x)*h(x) = \int_{-\infin}^{\infin}k(\tau)h(x-\tau)d\tau\\
\,\\
= \int_{-\infin}^{\infin}f(\tau-a)h(x-\tau)d\tau\\
\,\\
= \int_{-\infin}^{\infin}f(u)h(x-a-u)du
$$

where $k(\tau) = f(\tau-a)$ and $u = \tau - a$. Here we see the output has been shifted by $a$ amount because $h$ has been shifted by that amount as well.

# Recovering $h(x)$

If we're only given the output of the convolution, say $g(x)$, would it be possible to figure out $h$? We can do so with the unit impulse function

The unit impulse function is a function that has an area of one and is infinitely thin.

![alt text](<sources/LSIS and Convolution/Screenshot 2026-03-29 at 4.43.05 PM.png>)

The idea is that if you convolve this function with $h(x)$, then as $\epsilon \rightarrow 0$, you'll get closer and closer to the actual value of $h$. This is what we call the sifting property

> Just a little terminology, since if you feed the unit impulse you'll get the function back, that function is usually called the impulse response

# References

1. https://www.youtube.com/playlist?list=PL2zRqk16wsdorCSZ5GWZQr1EMWXs2TDeu

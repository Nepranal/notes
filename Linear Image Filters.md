# Convolution With Discrete Images

For images $f$ and $h$, we can take the convolution by

$$
g[i,j] = f*h = \sum_{m=1}^M\sum_{n=1}^Nf[m,n]h[i-m, i-n]
$$

$h$ as you see is still treated as before but now is flipped in two dimensions.

To visualize things a bit, we can imagine

![alt text](<sources/Linear Image Filters/Screenshot 2026-03-29 at 10.02.10 PM.png>)

It's probably good to note here that $h$, or the 'mask', has it's center to be defined as $(0,0)$.

So what we're doing now should be quite similar to what we would do with convolutions. We shift the mask by $i$ and $j$ amount in their respective direction (which would coincide the target coordinate and the center of the mask) multiply the two images (take the non-defined cells to be 0 for the mask) and sum.

> I'm pretty sure this is not how you should implement this for a programme

## Border Problems

You probably already saw this coming. To resolve this we would pad the parts that are covered by the mask but not the image. Some options you can take is to repeat the image, set the values to 0 (or some constant value), or just straight up ignore them

# The Box Filter

Now, it should hopefully be clear that you can use convolutions to perform image processing. The next question would be what kind of processing? Here's a curious filter

![alt text](<sources/Linear Image Filters/Screenshot 2026-03-29 at 10.34.00 PM.png>)

The sum of each element within the filter is 1 and each of the grey parts are of equal value. This filter has the following effect

![alt text](<sources/Linear Image Filters/Screenshot 2026-03-29 at 10.35.31 PM.png>)

Some features of the image are preserved but you can see that image's colour got a little grayer and most importantly, the image got 'smoother'. There are less sharp / finer details on the image and the average brightness is about equal per pixel because the image got averaged out.

Box filters however could create artifacts within the image. Particulalry, it tends to create 'blocky' shapes

> I honestly dont know if I can see it myself or not but this is how it looks like
> ![alt text](<sources/Linear Image Filters/Screenshot 2026-03-29 at 10.46.29 PM.png>)

# The Fuzzy Filter

The fuzzy filter is an improvement on the box filter

![alt text](<sources/Linear Image Filters/Screenshot 2026-03-29 at 10.47.02 PM.png>)

The filter itself is very bright in the center and falls of as you go away. It also has rotational symmetry.

The concept of the fuzzy filter can be extended by the gaussian ditribution (the 2d version) where you can for example controls how quick the brigthness falls of by modifying the standard deviation of the gaussian

![alt text](<sources/Linear Image Filters/Screenshot 2026-03-29 at 10.50.39 PM.png>)

The function used here is

$$
h[i,j] = \frac{1}{2\pi\sigma^2}e^{-\frac{1}{2}\left(\frac{i^2+j^2}{\sigma^2}\right)}
$$

A note: there's some caution you need to take care of while using this and it regards with the kernel size. The fuzzy filter techincally never goes to 0 and if your image is very big, the computation becomes very expensive if you want to get 'everything'. However, the shape of the gaussian is only 'interesting' at some parts (near the center) and so a rule of thumb is if you have a $\sigma$ then the kernel size to use could be $ \approx 2\pi\sigma$

## Fuzzy Filters Efficiency

There's something interesting about the fuzzy filter. At any point, the convolution would be

$$
g[i,j] = \frac{1}{2\pi\sigma^2}\sum_m\sum_n f[m,n]e^{-\frac{1}{2}\left(\frac{(m-i)^2+(m-j)^2}{\sigma^2}\right)}\\
\,\\
= \frac{1}{2\pi\sigma^2}\sum_m e^{-\frac{1}{2}\left(\frac{(m-i)^2}{\sigma^2}\right)} \sum_n f[m,n]e^{-\frac{1}{2}\left(\frac{(m-j)^2}{\sigma^2}\right)}
$$

This form of the equation tells us that the actual convolution is actually done by two 1D gaussians each of the same variance. You can see this by imagining that you can the inner sum for each row before doing the outer part of the sum

This is important because doing with the latter method is computationally more efficient. It's basically $O(n^2)$ vs $O(n)$ operations per point

# References

1. https://www.youtube.com/watch?v=-LD9MxBUFQo&list=PL2zRqk16wsdorCSZ5GWZQr1EMWXs2TDeu&index=6

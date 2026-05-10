# Non Linear Filters

Imagine that we would like to remove noise from an image. Something we can try would be to convolve it with a smoothing kernel, say the gaussian kernel

![alt text](<sources/Non-Linear Image Filters/Screenshot 2026-03-30 at 6.34.30 AM.png>)

What you'll find is that it's not so effective; the noises are still there but kind of just distributed around.

For this kind of noise (salt & pepper noise), we can actually use a different type of kernel called the median kernel. The idea is with a $K \times K$ kernel, sort the pixels within the kernel and take the middle one. You'll end up with a nicer result

![alt text](<sources/Non-Linear Image Filters/Screenshot 2026-03-30 at 6.38.11 AM.png>)

This kernel however won't work well for other kinds of noises for example when the image is grainy (if it was taken under lowlights). Furthermore, you cannot implement it as a convolution so the computation may be a little expensive

> The median kernel cannot be implemented as a convolution probably because either it's not linear or is not shift invariant

# The Issue With Gaussian Kernels

Take this for an example

![alt text](<sources/Non-Linear Image Filters/Screenshot 2026-03-30 at 6.46.32 AM.png>)

The big problem with gaussian smoothing for removing noise is that it just takes into account all pixels regardless of it's context. Here, you'll find that the details of the image also gets distorted because of that but it will do very well for the kind of noises present

> Not so sure why the kernel is bad for salt and pepper... Maybe because of the same reason?

# Improving The Fuzzy Filter

So, here's a simple solution: we will add bias. Instead of blindly applying the kernel, we will only apply it on pixel that is similar in intensity

![alt text](<sources/Non-Linear Image Filters/Screenshot 2026-03-30 at 6.55.38 AM.png>)

We have added bias. Though, how come it works now?

The gaussian kernel is very good at smoothing an image that is making different aspect of the image more similar. Earlier when we just blindly apply the kernel, we did something like this

![alt text](<sources/Non-Linear Image Filters/Screenshot 2026-03-30 at 7.00.29 AM.png>)

You see that the image has different level of details that we just merge into one. This would, as you can imagine, destroy the finer details within our original image as the finer details are now smoothed out

When we add bias, we were doing this instead

![alt text](<sources/Non-Linear Image Filters/Screenshot 2026-03-30 at 7.06.01 AM.png>)

where only the 'relevant' surfaces are smoothed out. Mathematically we do this by introducing another term in our equation: the brightness gaussian.

$$
\frac{1}{W_b}\sum_m\sum_jf[m,n]n_\sigma[i-m,j-m]b_\sigma[f[m,n]-f[i,j]]
$$

The brightness guassian term is essentially a weighting on the pixel. If the values are similar, the gaussian will be big and small otherwise. We call this filter the bilateral filter

> $W_b$ is just the sum of the weight and so is a function of $(i,j)$

The bilateral filter is non-linear and so cannot be implemented as a convolution. It's not linear because the kernel changes depending on where you are / how intense you are.

# References

1. https://www.youtube.com/watch?v=7FP7ndMEfsc&list=PL2zRqk16wsdorCSZ5GWZQr1EMWXs2TDeu&index=5

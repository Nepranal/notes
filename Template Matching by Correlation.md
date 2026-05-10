# Matching Template

Say we have a template for which we want to find in the image, the region for which this template matches the most. One way to do so is to slide the template over the image and checks which one gives the smallest difference. Something we can do is to minimize the following:

$$
\sum_m\sum_n(f[i,j] - t[m-i, j-i])^2
$$

Or, if you expand the equation you will be maximzing this

$$
\sum_m\sum_n f[m,n]t[m-i, n-j]
$$

This quantity is what we call the cross correlation

# An Issue With Cross-Correlation

The issue has to do with unormalized values. You can have parts of the image to be the best match for your template simply because it has very high value value - higher than the parts of the image that matches exactly with your template

![alt text](<sources/Template Matching by Correlation/Screenshot 2026-03-30 at 7.27.38 AM.png>)

> I suppose this is because we're not confident wether a region that match perfectly exist. If we do, we wouldn't have these problems because those other two will give negative values

We can fix this by simply normalising the cross-correlation which makes it ignore intensity differences

$$
\frac{\sum_m\sum_n f[m,n]t[m-i, n-j]}{\sqrt{\sum_i\sum_jf[i,j]^2} \sqrt{\sum_i\sum_jt[i,j]^2}}
$$

> Related to vectors and dot products...

# References

1. https://www.youtube.com/watch?v=1_hwFc8PXVE&list=PL2zRqk16wsdorCSZ5GWZQr1EMWXs2TDeu&index=9

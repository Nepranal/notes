# Detecting Bias & Variance

Here's a rule of thumb:

- High variance: If training error is way less than test error
- High bias: Both are high

Particularly, for high variance, you may see the following

![alt text](<sources/Debugging ML Models and Error Analysis/Screenshot 2026-05-03 at 1.16.44 PM.png>)

As you increase the training set, training error will typically increase as well but of interests is how the test error keeps on decreasing as the number of training set increases. Another, an arguably stronger indicator, is how there is a big gap between the training error and the test error. These would suggests that as there are more things to learn, the model keeps on going with no signs of stopping. This could be indicative of high variance

For high bias

![alt text](<sources/Debugging ML Models and Error Analysis/Screenshot 2026-05-03 at 1.22.36 PM.png>)

This basically says that you're algorithm's not doing anything at all... The idea being that more data won't really help you here

# Optimization Algorithm Dianogstics

Turns out that the pain point may also lie in your optimisers. For instance, sometime we've no clue whether your algorithm has converged yet or not. Particularly, you find that your alrogithm's not doing very well

Let's say you have 2 algorithms, $A,B$. And you found that $A$'s accuracy's way higher than $B$'s which is your preferred algorithm to deploy. How can you find out more about what's wrong with $B$?

Something you can consider is to check the parameters of the 2. If possible, stick the weights acheived by $A$ and use it in $B$ and check the loss. If the loss is lower, then $B$ has not converged (since you found a set of weight that's better) and you probably may want to use a different optimization algorithm e.g. Newton's method.

If the loss is not smaller, that means $A$ has a better objective function and so the issue lies in your objective function.

> Of course, only if you can put $A$'s parameter in $B$.

# References

1. https://www.youtube.com/watch?v=ORrStCArmP4&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=13

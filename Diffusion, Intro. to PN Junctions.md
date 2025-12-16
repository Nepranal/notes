# Diffusion

Within a semi conductor, there are 2 mechanisms in which a charge carrier travels with: drift and diffusion. Diffusion is it's movement by going from a region of high concentration to a lower one. Let's say for instance that we have medium and a function that describes the distribution of charge carrier within the medium

![alt text](<sources/Diffusion, Intro. to PN Junctions/desmos-graph.png>)
Here's the distribution function. You can imagine the x-axis tells which slice / region you are in the solid and y-axis tells you about the concentration of electron at that point.

It may be gnawing at you right now, but the magnitude of the current density at that region would be proportional to the rate of change of the concentration i.e. Your charge carrier would move faster from region A to region B if region A has way higher concentration than in B compared to if they have the same concentration. We can say:

$$J \propto \frac{dn}{dx}$$

And as it turns out, the relationship is quite simple:

$$J_n = qD_n\frac{dn}{dx}$$

Where $D_n$ is a proportionality constant called the diffusitivity. Note that the above derivation only works for electron charge carriers. For holes:

$$J_p = -qD_p\frac{dp}{dx}$$
The negative sign is put here because the current direction will be the same as the movement of the holes and that $\frac{dp}{dx}$ should be negative. Together, the total current density at a region would be:

$$J = q\left(D_n\frac{dn}{dx} - D_p\frac{dp}{dx}\right)$$

## Disappearing Electrons?

Consider the following:
![alt text](<sources/Diffusion, Intro. to PN Junctions/Screenshot 2025-11-24 at 10.04.02 AM.png>)
We are injecting electron into our semi conductor and we graphed the distribution of the electron throughout the solid and it turns out to be some form of exponential graph. We can easily get the current density profile of our solid but there's an odd question you can ask. The electrons move to carry the current because of the difference in concentration but how come the current decreases in magnitude? Do note that current can stay constant even though the concentration changes. What would causes a decrease would be a rather dramatic decrease of the concentration.

Because the current decreases, what must happen is that the decrease of electrons must've been affected more than just by the electrons redistributing themselves. Did they just disappear? The reason is not because the electron just suddenly disappear but rather they have recombined with the holes within the n-type semiconductor.

Then, what would happen if we have very little holes if any? Well, we can work it out backwards. What we expect is for the current to stay constant this time. Having a constant linear derivative would imply the distribution of electron would be linear as well.

![alt text](<sources/Diffusion, Intro. to PN Junctions/Screenshot 2025-11-24 at 10.22.54 AM.png>)

# PN Junction

A PN junction, as the name suggests, is made up of both a P-type and N-type semiconductor. You can imagine a block of both just sown together sharing a junction. It has an interesting voltage profile and is quite different from your typical Ohmic conductor.

![alt text](<sources/Diffusion, Intro. to PN Junctions/Screenshot 2025-11-24 at 11.27.07 AM.png>)

We can study the PN junction by posing the following questions:

1. How do the charges redistribute themselves after the junction is formed?
2. How does the junction behave under equilibrium, forward, and reverse bias?

Some of the words here may be a little strange, but we'll get there.

# PN Junction In Equilibrium

This will also answer the first question we posted. So, let's imagine that we have somehow stitched together a P and N semiconductor. As we know, we have lots of opposite charge carriers in either side, so diffusion here is warranted. As a charge carrier cross over to the opposite type i.e. electrons go from n-type to p-type and vice versa with holes, it will leave ions near the junction
![alt text](<sources/Diffusion, Intro. to PN Junctions/Screenshot 2025-11-24 at 12.04.42 PM.png>)
The creation of these ions induces a en electric field in the opposite direction of the diffusion current in effect, opposing it. This region where we have ions of different signs facing one another but not quite merging is what we call the depletion region.

As more and more electrons and holes cross, the more ions created, more charge accumulated at either side, the bigger the depletion region, the bigger the electric field and the stronger is the drift current opposing the diffusion current. This goes on until diffusion is no longer possible. The junction will then be in equilibrium.

# References

[Razavi Electronics 1, Lec 3. Diffusion, Intro. to PN Junction](https://www.youtube.com/watch?v=mhtYm-USVD8&list=PLyYrySVqmyVPzvVlPW-TTzHhNWg1J_0LU&index=3&pp=iAQB)

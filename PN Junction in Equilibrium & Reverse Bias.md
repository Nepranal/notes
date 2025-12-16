# Equilibrium Conditions

Equilibrium happens when the diffusion current and the drift current caused by the depletion region is equal. You can imagine being a charge carrier in the depletion region under the effect of diffusion and drift. This condition must hold for either side of the junction. Putting it mathematically,

$$
\mu_ppEq = D_p\frac{dp}{dx}q
$$

something similar holds for electrons.

What's cool about this is that we can get a fundamental quantity about our condition like the potential difference in the depletion region. I won't do the derivation here since I'm not confident with the calculus involved. But the potential comes out to be

$$
V_0 = \frac{kT}{q}\ln{\frac{N_aN_d}{n_i^2}}
$$

## Electric Field Outside Of Depletion Region?

There can be no electric field outside of the depletion region because of Gauss's law. The depletion region has a net charge of 0 and what you can do is to draw an imaginary surface encapsulating the depletion region + a little bit of the area outside.

![alt text](<sources/PN Junction in Equilibrium and reverse bias.md/Screenshot 2025-11-30 at 6.21.41 PM.png>)

Gauss's law says that the electric field at the surface is proportional to the net charge within. No region here has any net charge. Btw, this also implies that there are no voltage drop anywhere else in the PN junction.

## Observations In Equilibrium

1. $V_0$ only exist within the depletion region
2. We can't measure $V_0$ externally

The last bit is a little weird... Why? As it turns out, if we use something like a voltmeter, we'll need to connect a metal to the PN junction which will create yet another junction. The voltages built up by the new junction will cancel out the built-in voltage by kirchoff's voltage law.

# Reverse Bias

Reverse bias happens when you connect a positive end of a battery to the N side and the negative side to the P side. What this will do is that the mobile carrier of the each junction will go to the plate of the battery because it has the opposite charge (it won't really be attracted by the charges in the depletion region because there are no electric field from that region) resulting in a wider depletion region.

What this effectively does is creating a variable capacitor from the junction. The neutral parts of the junction act as the metal plate as it is conductive (since it was doped) and the depletion region becomes the dielectric material since it's can be seen as an insulator (no charges can flow through it in equilibrium).

The characteristics of such a capacitor can be easily varied by varying the applied reverse biased voltage. This would expand the deplition region and in effect decreasing the capacitance. Overall, it has the following form:

$$
C = \frac{C_0}{\sqrt{1-\frac{V_R}{V_0}}}
$$

where $V_R$ is the applied reverse biased voltage (which must be negative by convention) and $V_0$ is the built-in potential. At $V_R=0$, the capacitance will be $C_0$. As $V_R$ gets more negative (increasing the reverse voltage), the capacitance gets smaller and smaller.

# References

https://youtu.be/6UNezo_GByE?si=T9FLk-09EDOeuFP9

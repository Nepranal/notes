# Quantum Efficiency

Quantum Efficiency is a measure of how much electron can be generated from the type of material and the wavelength of light coming in. It's defined as

$$
q(\lambda) = \frac{I}{p(\lambda)}
$$

where $I$ is the intensity of electron flux and $p(\lambda)$ is the incoming photon flux.

# Recovering $p(\lambda)$

For a monochromatic light, given we know the quantum efficiency of the material, we can figure out the outgoing electron flux:

$$
I = q(\lambda)p(\lambda)
$$

However, light typically consists of many wavelengths. That is,the incoming $p(\lambda)$ is typically a spectral distribution:

![alt text](<sources/Types of Image Sensors/Screenshot 2026-01-11 at 7.23.06 PM.png>)

In such a case, the intensity becomes

$$
I = \int_0^\infin q(\lambda)p(\lambda)d\lambda
$$

A big part as to why we want to know the outgoing electron flux and the quantum efficiency is because we want to recover $p(\lambda)$ of a particular point in the scene. Remember, the sensing device has no idea what the wavelength is and only knows about how much flux it's generating. You can imagine that if the scene is monochromatic, then our job would be easy: just take $I/q(\lambda)$ and that will be our colour (wavelength). But in realistic cases, where $p(\lambda)$ is a distribution, how can we do so?

Here's an interisting tidbit: you can put wavelength filters in front of your pixel. These filters will only filter out one (approximately) wavelength from the incoming light ray.

![alt text](<sources/Types of Image Sensors/Screenshot 2026-01-11 at 7.33.10 PM.png>)

Mathematically, the filters act as a delta function for $\lambda=\lambda_i$:

$$
I = \int \delta(\lambda)q(\lambda)p(\lambda)d\lambda
$$

$$
I = \delta(\lambda_i)q(\lambda_i)p(\lambda_i) = q(\lambda_i)p(\lambda_i)
$$

Which then you can kinda figure out how intense that particular colour is. With filters we can figure out one particular wavelength. Turns out, if we want to discover the entire distribution, we don't need an infinite amount of filters. 3 of them would suffice to give a good enough approximation

## Tristimus Values

The eyes also capture light and tries to converth them back into colours. Interestingly, the eye is, mostly, only tries to filter out 3 colours: Red, Green, Blue. As it turns out, you can reconstruct any colour with only these 3 base colours.

$$
I_r = \int \delta_r(\lambda)q(\lambda)p(\lambda)d\lambda
$$

$$
I_b = \int \delta_b(\lambda)q(\lambda)p(\lambda)d\lambda
$$

$$
I_g = \int \delta_g(\lambda)q(\lambda)p(\lambda)d\lambda
$$

However, because we're only looking at these 3 base values, it would mean that multiple different distributions are viewed to be the same just because they have the same RGB values. These are called metamers.

![alt text](<sources/Types of Image Sensors/Screenshot 2026-01-11 at 8.16.29 PM.png>)

# References

1. https://www.youtube.com/watch?v=V4y3K6zoUQs&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=14

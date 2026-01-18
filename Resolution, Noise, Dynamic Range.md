# Noises In Image Sensors

Generally, you can split types of noises into 2 categories: scene dependent or independent.

The first kind is photon shot noises. This is a scene dependent issue and its a problem about not knowing the exact count of incoming photon from a particular point of the scene. We can't really do much about it here since photons comes in at random; you'll get random counts of photons in different intervals.

The second type of noise is readout noise. This is a scene independent one and concerns with how we're processing the voltage. You can think of steps like quantization and electronic noises (coming from the ADC).

There are some other kind of source of noises as well. For instance you can consider thermal noise (heat can also create electron-hole pair) and just defective pixels (difficult to guarantee that pixels are identical).

# Dynamic Ranges

Since pixels can be a little problematic, we also define a metric to measure them. One of which is called the dynamic range defined as

$$
20\log(\frac{b_{max}}{b_{min}})
$$

$b_{max}$ is the maximum number of photons the pixel can take in while $b_{min}$ is the minimal (in presence of noise).

# References

1. https://www.youtube.com/watch?v=hb6SARVQEJQ&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=12

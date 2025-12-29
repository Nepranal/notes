# Types Of Reflection

There is an aspect of shading that has been left out before. As we know so far, surfaces come in different levels of roughness. This would also lead to different kind of reflection. There are 2 extremes of the reflection kind distinguish by the angle of the reflected light. If all light rays are reflected in a random manner, we call this diffused reflection. If all rays reflected in one particular direction, we call it specular reflection. The kind of reflection your object has determines it's roughness.

> A little tangent, apparently objects which are rather smooth typically won't be as bright under the same brightness.
>
> ![alt text](<sources/The Rendering Equation/Screenshot 2025-12-04 at 8.12.36 PM.png>)
>
> The main explanation for it is that the bottom material (the smooth one) has less diffused reflection. The way I understood it is that the part of the teapot that gets specular reflection all goes to one particular direction. This would mean that overall, the brightness of the teapot should be less because some part won't have light going to the viewing direction.

Now, the one thing we haven't considered is the fact that if a light ray is undergoing specular reflection, then it must not undergo diffusion and vice versa. A fact that our previous formulations failed to take into account.

# Bidirectional Reflectance Distribution Function

We'll refer to it as BRDF, is something that can help us. The BRDF is a function that given an incoming direction $\omega_i$, and viewing direction $\omega_o$, describes how the reflection will behave e.g. Which portion undergoes specular, and which undergoes diffusion.

Just a little convention:

$$
f_r(\omega_i, \omega_o)
$$

![alt text](<sources/The Rendering Equation/Screenshot 2025-12-04 at 8.21.59 PM.png>)

The idea is that it describes, for an incoming light ray, the intensity of the light ray on a given reflection direction

![alt text](<sources/The Rendering Equation/Screenshot 2025-12-04 at 8.26.16 PM.png>)

# The Rendering Equation

Neat, but how do we compute $L_o$? It's actually quite straightforward:

$$
L_o(\omega_o) = L_i(\omega_i)\cos(\theta)f_r(\omega_i, \omega_o)
$$

The $\cos(\theta)$ is the geometric term determining intensity based on the geometry of the plane and $f_r(\omega_i, \omega_o)$ provides weithage purely based on the material.

In the event of incoming light from all direction, we can write more generally:

$$
\int_\Omega L_i(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o) d\omega_i
$$

We have actually seen some examples of a BRDF before. Take Phong / Blinn:

$$
f_r(\omega_i, \omega_o) = K_d + K_s\frac{\cos(\phi_i)^\alpha}{\cos(\theta_i)}
$$

![alt text](<sources/The Rendering Equation/Screenshot 2025-12-04 at 9.06.19 PM.png>)

# Reference

https://www.youtube.com/watch?v=GOfzX7kRwys&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=18

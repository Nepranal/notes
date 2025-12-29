# Dealing With Brightness - Lambertian Material

At a very global view, shading just deal with how bright the surface can be exposed in varying light intensity. The more light per area, the brighter the colour on that surface. We can quantify that with the following model:

![alt text](<sources/shading/Screenshot 2025-11-21 at 7.18.41 PM.png>)

This says that as you vary the angle of the surface with respect to a light source, the intensity of light per unit area will be a component of that light source.

We can interpret this to the colour, or material, of the surface and say that the actual colour will depend be a component based on the angle of the incoming light ray

![alt text](<sources/shading/Screenshot 2025-11-21 at 11.57.58 PM.png>)
And of course, this will work on textures in general.

The equation is not complete however since we haven't consider the intensity of the incoming light. Putting it all together, we will get

$$C=IK_dcos(\theta)$$
Where $C$ is the final colour and $I$ is the intensity of the light.

Materials that have this kind shading is what we call a Lambertian (diffused) material. If you actually try this formulation on a model, you'll find that it will make the texture seems rough because there are no what we call "specular" reflections.

# Phong Material Model

The issue with the lambertian diffusion is that it doesn't take into account the viewing direction. On a mor general term, depending on the roughness of the surface, and where we look, we might be able to see the light source itself on the object. Here's an updated model:

![alt text](<sources/shading/Screenshot 2025-11-22 at 8.20.51 AM.png>)
You can imagine that, depending on $cos(\phi)$, you may be able to see the light source clearly, e.g. you're viewing directly with $r$; the perfect reflection direction.

Putting this in terms of equation:
$$C = IK_scos(\phi)^\alpha$$
Where $\alpha$ is a free parameter used to determined roughness of the surface and $K_s$ gives out how shiny the surface is.

---

I thought that it's kind of weird that we already have $\alpha$ so why do we need a $K_s$ as well. The idea is that we have a base 'shininess' which will give the maximum shininess the surface can give which then can controll the $(cos(\phi))^\alpha$ term. Note that you can't control the shininess of the surface if you get rid of $K_s$. Your surface will just be blinding white if you look at it at angle $\theta$

---

We can then put together our diffused model and the specular reflection to get the phong specular model. You can think of it as we're adding extra effects on top of one another:

$$C = IK_dcos(\theta) + IK_s(cos(\phi))^\alpha$$
$$C=Icos(\theta)(K_d+\frac{K_scos(\phi)^\alpha}{cos(\theta)})\tag{1}$$

$(1)$ is how it's usually written. since the incident angle should also affect the specular reflection.

For more practicality however, since we the cosine function can be negative, etc. (it wouldn't make sense if we get some colour when the light is shining from behind the surface) We usually use this form instead:

$$C = I \max(0, cos(\theta))\left(K_s+K_d\frac{\max(0, cos(\phi)^\alpha)}{cos(\theta)}\right)$$

# Blinn Material Model

Phong's way is just one way of dealing with brightness. Particularly, the term inside the bracket determines the 'model'. As a matter of fact, Blinn's material model follows the exact same formula just different definition of $\phi$:

![alt text](<sources/shading/Screenshot 2025-11-22 at 10.34.29 AM.png>)

# Ambient Lights

The Blinn/Phong material model works well however, the environment in which the object is rendered, they typically won't obey the law of physics especially for bouncing light. This of course depends on how you actually do rendering, but for your typical render, this won't happen. Using the material model would create some rather odd scene due to the absence of light:

![alt text](<sources/shading/Screenshot 2025-11-22 at 10.49.03 AM.png>)
as you can see, the bottom half of this ball is just gone. There's of course some situation where this is valid, but in a typical room with a light source, this is not so realistic.

To avoid simulating the physics of light bouncing, we can just sneakily add a little brightness of our own. This will work well in environments where you would have some light here and there. In terms of equation this is quite simple:

$$C = I \max(0, cos(\theta))\left(K_s+K_d\frac{\max(0, cos(\phi)^\alpha)}{cos(\theta)}\right) + I_aK_a$$

where $I_a$ is the intensity of the ambient light. Here, typically $K_a = K_d$ since it won't really make sense for the ambient light to be of a different colour than the surface's

# Image-Based Lighting

So far, within the discussion, we've only considered the light coming from one direction. This kind of lighting is one of many different kinds. We call it 'directional' lighting since we only care about it's direction. There are however many more like point light, spotlight, area light, etc. To compute shading based on this different kind of lights can also be cumbersome because of their shape. The more irregular the shape, the more difficult it would become (you'll have to take integrals, etc.)

![alt text](<sources/shading/Screenshot 2025-11-22 at 11.14.35 AM.png>)

One particular type of lighting is called environmental lighting. It's somewhat like directional lighting, but the idea is that light will come from every single direction. Furthermore, you'd also at times consider that there are different intensity as a function of the direction.

What's interesting about environmental lighting, is that you can treat the properties of the incoming light, the intensity, colour, etc. The same as querying from a texture. In fact, you can make a digitally inserted object to a real life scenery, by taking measurements of light in the scene, and shade the digital object the same as any other objects in the scene. The idea here is to turn that measured light from the environment into a texture.

# Reference

https://youtu.be/Xg6KEmhqHCY?si=I1xPECaQ6qbI8hME

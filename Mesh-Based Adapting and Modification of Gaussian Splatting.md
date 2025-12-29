# Introduction

The main idea of the paper is to propose a new method in editing a 3DGS model. Particularly, it will be a mesh-based approach similar to previous works like SuGaR (Surface Aligned Gaussian Splatting). GaMeS, as the authors claim, is faster than SuGaR because it avoids an expensive training used to predict surface normals. It also can just omit any requirement to train for such a thing if you can provide the mesh already. It does this by reparametrizing the Gaussians with the mesh.

When no mesh is provided, GaMeS will produce what they call a triangle-soup. It's basically a collection of triangle faces that are not necessarily physically connected. Once created, the idea of reparametrizing is used similarly.

# Definiing Gaussian Splats

Gaussian Splats $G$ are defined as:

$$
G(N(\bold{m}, \Sigma), \sigma, c)
$$

$\bold{m}, \Sigma$ are defined as you would with the normal distribution. $\sigma, c$ is the spherical harmonics and the colour of the gaussian splats both used to render the gaussian splats. $\bold{m}$ and $\Sigma$ are the 2 things that this paper will manipulate

# With Provided Mesh

When a mesh is provided, with triangular faces, some number of 3D Gaussians (fixed number) will be placed/initialized on each face. Say we have $V = (v_1,v_2,v_3)$ representing the vertices of the primitive. We can, using something like barycentric coordinate, to rewrite the median of the gaussian:

$$
\bold{m} = \alpha_1v_1 + \alpha_2v_2 + \alpha_3v_3
$$

where $\alpha_1 + \alpha_2 + \alpha_3 = 1$ and also that they are trainable parameters.

We won't actually touch on the spherical harmonics and the colour much and we don't really have to (since, moving the gaussians around won't affect their intrisic values). The other part that we do have to deal with is $\Sigma$. I will admit, I have no idea how the mathematics work in this paper to deal with it. But, to oversimplify,

1. You can add a constraint such that $\Sigma = RSS^TR^T$ where $R, S$ are a rotational and scale matrix respetively. We do this because some values of $\Sigma$ won't make sense physically. By having the factorization, we can guarantee something that makes 'sense' (whatever that means)
2. The job to figure out how to compute these things come from the original definition of the matrices from the 3DGS paper (Frankly, right now I don't know why). You can look at the details in the paper as to how they compute them

> **note:** $\Sigma$ affects the 'flatness' of the 3D gaussian

So now, we parametrized the gaussian as:

$$
N(\bold{m}(\alpha_1, \alpha_2, \alpha_3), \rho\Sigma)
$$

where $\alpha_1, \alpha_2, \alpha_3, \rho$ are trainable paremeters. $\rho$ is a scale (notice that $\Sigma$ is a constant). Doing this, we can train the gaussians as well as relate them to a face.

But why? The big idea is so that we can perform transformation on the faces of the mesh only without worrying about the gaussians. Once we've done that, to now about the physical parameters of the gaussians, we can compute them from the vertices of the triangle using the same $\alpha$s.

# With No Provided Mesh

The solution with no mesh is to just spawn a triangle for each gaussian splats. The idea is to first train a 3DGS model and then for each 3DGS, we'll spawn a triangle. Then, we use the vertices of the triangle to parameterise the used gaussian. It's kinda like gaussian -> triangle -> parametrized gaussian. The reparametrization process is similar and as before, I will defer it since I don't really get it

The nice thing about this is that you don't have to retrain again. The whole idea is to simply have so mesh to manipulate and relate it the gaussian. The gaussians will be the same ones that you used to produce the triangle soup just reparametrized. I believe this is why you don't see any trainable parameters in the re-parametrization unlike before.

# Reference

1. https://arxiv.org/abs/2402.01459

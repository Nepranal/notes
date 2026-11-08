# Introduction

The idea of this project is to convert a large scene reconstruction and convert it into a mesh. There are already some work being done here though they all kinda complicated and some are not scallable. Additionally, moast of them requires you to have a heavy duty computation machine. Here, I'm going to propose a simple way to perform such a task that can be done by a reasonably strong computer.

A lot of the work here builds from a previous project of mine where I did something similar but, instead of a mesh, I produced point clouds. I argue that the same exact methodology can indeed be applied.

# Tool

Obviously I'm not going to work everything from scratch (idk how). We only need something that will allow me to get a reconstruction. Considering the need for speed, there's one piece of work called GaMeS (Gaussian Mesh Splatting). What it does is that it convert a set of images into a mesh by first converting them into 3DGS. It will hopefully be sufficient for this project.

To do this, there are 2 mechanisms we need from GaMeS. It will essentially be similar to what I did with COLMAP: find a way to merge 2 scenes together but I should have the choice to optimize a mesh for only 1 of the scene & a way to find out if a particular face of the mesh belongs to particular scene. Additionally, another important part is how to split a singular mesh into different parts. This may not be related to GaMeS, but we'll need to figure it out anyways

# Partial Optimization

# Mesh Faces Grouping

# Splitting A Mesh

# References

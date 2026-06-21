# Forward Kinematics

Forward Kinematics is the process for which we discover find the position of the end affecter $M$ w.r.t the fixed frame $F$. Here is the convention we'll be using

![alt text](<src/Forward-Backward Kinematics/Screenshot 2026-06-10 at 11.04.11 PM.png>)

Here you'll see that each joints is labelled to be an end affecter: $M_1,M_2,M_3$. The final goal is to figure out what $X$ is given you know $x,L_1,L_2,\theta_1,\theta_2,\theta_3$ and to do that we'll transform $x$ from $M_3$ to $M_2$ and so on until we reach $F$.

Before going forward, it's good to note of the transformation matrix

$$
R = \begin{bmatrix}
\cos(\theta) && -\sin(\theta) && x\\
\sin(\theta) && \cos(\theta) && y\\
0 && 0 && 1
\end{bmatrix}
$$

This will transform a homogenous vector by an angle $\theta$ anti-clockwise (hopefully you'll see how this relates to why we define all angles to be anti-clockwise) and translate it by $x$ in the x-axis and similarly in the y.

To go from $M_3$ to $M_2$ we'll need to figure out the translations as well. We would have moved by $L_2$ only in $M_2$'s x-axis. In general then, to move from one frame to another we will have

$$
R_{M_j,M_i} = \begin{bmatrix}
\cos(\theta_j) && -\sin(\theta_j) && L_i\\
\sin(\theta_j) && \cos(\theta_j) && 0\\
0 && 0 && 1
\end{bmatrix}
$$

To transform $x$ we can then do

$$
X = R_{M_1,F}R_{M_2,M_1}R_{M_3,M_2}x = Rx
$$

In general, the forward kinematics equation can be written as:

$$
X = \begin{bmatrix}
\cos(\Theta) && -\sin(\Theta) && \sum_iL_i\cos(\sum^i_k\theta_k)\\
\sin(\Theta) && \cos(\Theta) && \sum_iL_i\sin(\sum^i_k\theta_k)\\
0 && 0 && 1
\end{bmatrix}x
$$

where $\Theta = \sum_i\theta_i$

# Inverse Kinematics

Inverse Kinematics deal with finding the reverse problem. Given that you know $X$, how can you figure out the other parameters? Within the field of robotics, we're particularly interested in the case for which $x = \bold{0}$. That is

$$
x = R^{-1}X = \bold{0}
$$

In the case for which you have 2 links, you can somewhat manage and solve this equation. It however gets a little more complicated as you have more links.

# References

1. https://www.youtube.com/watch?v=_8T7RjXL07M
2. https://www.youtube.com/watch?v=8D0sO8mymQ8

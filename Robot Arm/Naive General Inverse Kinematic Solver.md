# Naive Solver

There is a set of formula we can use to solve inverse kinematics. The issue is, they're mostly for a system of 2 links and 3 joints. Maybe we can extend it a bit

The idea is to assume the arm is made up of at most 2 links

![alt text](<src/Naive General Inverse Kinematic Solver/IMG_1429.jpg>)

We then solve for links $L_1, L_2'$. After solving, we recursively go deeper into the components that make up $L_2'$ e.g. $L_2$ which will become the next $L_1$. $L_2'$ is recomputed based on $L_2$. Do this until we have no more links to process

$L_2'$'s parameters must be adjusted per iteration. Essentially, we will have to transform the fix frame $F$ as we move to the next link. Say we solve $L_1, L_2'$. We then transform $F$ to be at the base of $L_2$ before we process the next set of links. The moving frame $m$ will also be transformed w.r.t $F$.

Since we solve link-by-link, the result can also be sent early for each links though we need to be careful not to violate their constraints (will need some sort of mechanism here) and run into an impossible case

# References

1. https://www.youtube.com/watch?v=_8T7RjXL07M

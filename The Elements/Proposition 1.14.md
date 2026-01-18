# Proposition

> If two straight-lines, not lying on the same side, make adjacent angles (whose sum is) equal to two right-angles with some straight-line, at a point on it, then the two straight-lines will be straight-on (with respect) to one an-other.

This is like the sufficient condition that if two adjacent angles sums to $\pi$ then it must be on a straight line

# Proof

![alt text](<../sources/The Elements/Screenshot 2025-12-23 at 4.59.12 AM.png>)

The lines are $CB, BD$. The incoming line is $AB$. Let $\angle ABC = \alpha, \angle ABD = \beta$. Assume $\alpha + \beta = \pi$. Show that $CD$ is a straight line.

Let's assume it's not and say another line $BE$ is the straight continuity of $CB$. Let $\angle ABE = \gamma$. This would mean $\alpha + \gamma = \pi = \alpha + \beta$.

But there's an issue. The line $BE$ must be drawn in such a way that it takes portions of the adjacent angle. Or in other words, $\beta > \gamma$. However, the equation implies $\gamma = \beta$. This is a contradiction.

# Alternative

An alternative take is to also show it by contradiction but this time, I assume $BD$ is at angle $\gamma > 0$ to $CB$ i.e. If I rotate $CB$ by $\gamma$ clockwise, then I can get a straight line. So, what we get is $\alpha + \beta + \gamma = \pi$. However, since $\alpha + \beta = \pi$, this would imply $\gamma = 0$. A contradiction.

# Problem Statement

> A detachment of 25 soldiers must cross a wide and deep river with no bridge in sight. They notice two 12-year-old boys playing in a rowboat by the shore. The boat is so tiny, however, that it can only hold two boys or one soldier. How can the soldiers get across the river and leave the boys in joint possession of the boat? How many times does the boat pass from shore to shore in your algorithm?

# Solution

This puzzel's a little tricky because you can't have a solider and the boy ride the boat together: when the boat is transferring a soldier and reaches to the other part of the crossing, how can the boat be rowed back? A possible way is to already have someone be at the end of the crossing.

It wouldn't make much sense for a soldier to be the one at the end, so lets have the boys be at the end. With this in mind, a possible algorithm we could come up with would be to first have the 2 boys row the boat accross (+1), have one of the boys stay and the other one row the boat back (+1). With this, the soldier can start moving across with a boy waiting (+1) at the end of whom can row the boat back (+1). We have one soldier be rowed across after $4$ times going back and forth and we're back to our original state of problem. The total number of times then would be $100$ or in general $4n$.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

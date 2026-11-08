# Problem

> There are 20 gloves in a drawer: 5 pairs of black gloves, 3 pairs of brown, and 2 pairs of gray. You select the gloves in the dark and can check them only after a selection has been made. What is the smallest number of gloves you need to select to guarantee getting the following?
> (a) At least one matching pair
> (b) At least one matching pair of each color

# Solution

This is a pidgeon-hole-esqe problem. Though one thing that you need to know is that the gloves come in pair: left and right.

For part (a), it's possible you get all left pair or right pair if you take less than or equal to 5 + 3 + 2 = 10 gloves. So you'll have to take 11 to have a pair of left and right. Note that taking 11 would also force the pair to have matching colour as well. To see this, you can imagine this construction

![alt text](<sources/Glove Selection/IMG_123D8B622319-1.jpeg>)

There are $10$ holes each represents the different colours. We only have $3$ distinct colour groups but each has a maximum count. I'm just expanding that collection here. The L and R represents left and right glove. You can see that in some configuration, you're basically drawing edges from the vertex to either L or R or both. Since we only have $10$ of these holes, and $11$ edges to draw, and each edge must come from a vertex, there must be an edge that shares $2$ edges by the pidgeon hole principle. And, since you cannot have two edge going into the same side (R or L), this edge must go to both L and R and hence a pair with the same colour.

With part (b), you'll need to take at least 19 gloves. This would make the case where you only have matching pair of 2 colours impossible. If you only pick 16, then what you grabbed could be only black and brown gloves of matching pair. If you take 17, then you can imagine the leftover 3 gloves. If all 3 gloves are gray, then you're missing out on the gray matching pair. For 18 gloves, consider that the last 2 are both right grey gloves. 19 is the nice spot.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

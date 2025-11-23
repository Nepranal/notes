# Problem

> There are n sticks with some lengths. Your task is to modify the sticks so that each stick has the same length.
>
> You can either lengthen and shorten each stick. Both operations cost x where x is the difference between the new and original length.
>
> What is the minimum total cost?

# Model

You can literally think of just putting a bunch of sticks next to one another. Now, draw a line that is perpendicular to all of the sticks. The total cost to where you put that line, would be the total cost to incurr. The job is to find the best position for this line. The best being a line position that will create minimal changes to the sticks in total.

In your mind, you can move this imaginary line up and down. Let's start at the very top and let's keep in mind of the tall and short sticks. At the highest point, all of the shorter sticks will be extended. Let's move it down a unit. The cost will add from the tallest sticks but it will also reduce the cost from the other shorter sticks which leads to an overall decrease (assuming you imagined that there are more shorter sticks then there are taller sticks). If you keep going, you'll find that the cost will keep decreasing as long as majority of the sticks are shorter than our imaginary line. The moment we cross that majority, you'll find the cost increasing again because more sticks are being cut down.

The final part of this idea is to ensure that the line is indeed optimal. Let's assume its not. Then either the optimal line is above or below. The line cannot be above because as we've shown that as long as majority are below, we can still lower the line. Similarly with if the optimal line is below our line.

# Implementation

# Reference

http://cses.fi/problemset/task/1074

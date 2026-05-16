# Objective

The overall problem that the software tries to solve is how do we, given that the arm is located at $p_0$, move it to another point $p_1$.

# Assumption

Of course, we first need to define the notion of what it means for the robot to be at any particular point. This will be described later. So, let's first assume that there is a way and it makes sense for the position of the arm to be represented in such a way

# Initial Ideas

A naive approach in moving would be to take the shortest path always. So, what we can do is make something that will take as its input a curve, or some generic way to specify motion, and break it down into an ordered set of points. The arm will then follow those sets of points, each time taking the shortest path

This design would imply that the software has a component that can:

1. **break down a curve into a set of points**
2. **another to move the arms accordingly**

The design makes an assumption that the arm knows where it's located at any given time. This is a state of the arm

I actually have no idea how the 2nd component is going to work. We can make a couple of guesses. First, we know that an arm is split into different segments. Each segment can be seen to move w.r.t the other segment it is connected to

This then can go a couple of ways. Given a point to move into, the arm can process it segment by segment or all segments can be moved at once.

For example, say our arm has $N$ segment labelled as $s_1,s_2,...,s_n$. The sequential approach would be that a particular segment is told to bend in a particular way and every other segment will move accordingly in such a way such that that segment can bend that way. The number of messages we send out will be small in this case. Though, this would mean that we need to handle cases for when the request is impossible e.g. Bending your arm $90\degree$ upwards (maybe some human can, but i for sure cannot). Since the bending is figured out in a greedy manner, we won't know we have fallen into this state until it happens

The latter option won't suffer from such a predicament because we would need to have computed how each segment would need to bend. If it's impossible, we simply just spits out it's impossible. This however may be a bit more expensive.

Regardless, we would need a component that can manage the **bending of the segment** and **a way to specify the bending**.

As it currently stands, I'm in favour of the latter one because we won't need to hassle ourselves with what could happen if we run into such a case

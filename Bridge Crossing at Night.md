# Problem

> Four people need to cross a rickety footbridge; they all begin on the same side. It is dark, and they have one flashlight. A maximum of two people can cross the bridge at one time. Any party that crosses, either one or two people, must have the flashlight with them. The flashlight must be walked back and forth; it cannot be thrown, for example. Person 1 takes 1 minute to cross the bridge, person 2 takes 2 minutes, person 3 takes 5 minutes, and person 4 takes 10 minutes. A pair must walk together at the rate of the slower person’s pace. For example, if person 1 and person 4 walk together, it will take them 10 minutes to get to the other side. If person 4 returns the flashlight, a total of 20 minutes have passed. Can they cross the bridge in 17 minutes?

# Solution

A naive solution for this is to have the fastest person accompany everyone across. If you add up the time, you'll find that it would be $2 + 1 + 5 + 1 + 10 = 19$ minutes. Can we do better?

A part of this problem is that someone has to go back $2$ times. The idea being that each going forward can only take 1 person and someone has to go back. This would mean with 4 people, you'll have a back and forth 5 times.

If the fastest person is always involved, you'll find that the backward cost is $1+1 = 2$ minutes and the forward will always be $2+5+10 = 17$ minutes. We can't have the fastest person be the one to go back always which also means it cannot always be the person that goes forward as well.

Let's say the 2nd person is involved in the going back and forth. The cost of going back would be $1+2 = 3$ minutes. This would mean that any solution must have a forward time of lesser than 15 minutes. Notice that we have one person with 10 and another with 5. We can't have these two cross individually so let's have them cross together.

A possible solution would then be person 1 first bring person 2 accross (+2) and comes back (+1). Then Person 3 takes person 4 across (+10) and instead of person 3 coming back, we'll have person 2 (+2) since we want it to be part of the going back cost. Then person 2 carries person 1 (+2). We'll then have a total of $2+1+10+2+2 = 17$ minutes.

# 17 Minutes Is The Minimum

We can actually show that $17$ minutes is the actual minimum. If fastest person is not always involved in the going back, the going back time would be at least $1+2= 3$ minutes. The time spent going forward would at least be $10+2+2 = 14$ minutes. The $10$ is because someone has to bare the slowest person. The other 3 parts are at least 2 since 2 is the 2nd slowest time. This would mean the optimal time will take at least $17$ minutes.

> We know this minimum since we know that if the time to back is $2$ minutes, you will definitely get $19$ minutes.

# References

1. Levitin, A., & Levitin, M. (2011). Algorithmic puzzles. Oxford University Press.

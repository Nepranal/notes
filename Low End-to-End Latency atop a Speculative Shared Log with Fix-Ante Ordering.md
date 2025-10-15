# Low End-to-End Latency atop a Speculative Shared Log with Fix-Ante Ordering

# Introduction

The paper talks about shared logs. A shared log provides global ordering of records. Sounds kind of trivial but you’ll have to think big. Imagine millions of applications passing in data to your log and all you have is a centralised database system. Maybe it is possible but it would be very difficult to achieve. 

What have been done is to scale horizontally and split data / records into shards; partitioning them into disjoint subsets. One aspect however is desirable: ordering. I think the reason why is quite dependent on the application but it’s quite important to have especially total global ordering. The paper will deal with this problem.

Previous works has done what’s called an order-first approach where before inserting a record in to a shard, a sequencer must first be contacted to get the appropriate sequence. This approach is problematic:

1. If the process fails, that sequence number is gone forever
2. Bottleneck
3. Difficult to move the record around between shards

An alternative to this is called the durability-first approach where the client can choose which shard the record is to be inserted and then delegate the task of getting the sequence to the shard. Depending on the implementation, the shard then may contact the sequencer by batches of records to be sequenced. 

The durability-first approach sounds nice and it does deal with some of the points mentioned before but it is still inefficient. The issue lies in the fact that both implementation requires a global ordering of record before it can be inserted into the shared log. This leads to what’s called a ‘high delivery latency’ because of the bottleneck.

---

The paper proposes a new approach to help remedy the bottleneck by speculating the order. Basically, lots of applications wait for the total ordering to come up and then starts the downstream process. The authors show that if we simply guess what the ordering will be, let the application believe in that ordering, and in the event the ordering is false we simply restart the downstream process, the whole thing can be more efficient.

Of course, if the speculation is wrong lots of the time, then might as well just wait for the incoming ordering. To deal with this, the paper proposes the ‘fix-ante’ ordering. It works by assigning ‘quotas’ to different shards to fill. If we know the number of records will be in each shard, the total global ordering can be more easily computed. 

- In the event that the shard is in a deficit, we can just put in no-ops or essentially blank records
- In the event there’s an overflow, just pass it to the next round of reports
    - Shards can choose how many of their records they want to report

This ordering system can give accurate order most of the time and fails in rare cases such as entire failure of a shard or sequencer if uncontactable, etc. 

However, there are some intricacies to think about within the system.

1. How big should the quotas be? Too large and you’ll have too many no-ops. Too small and there will be too many rounds of processing - Set quotas by ingestion rate
2.  What happens when there are bursts of data? - Lag-fix mechanisms
3. Longer term rate change? - Speculation Lease Window
4. Stragglers / Downed shards - Mechanisms to add and remove shards

> Technically, there are 2 things introduced in this paper: The log abstraction and the application that uses it. The shared log abstraction is called SpecLog and the application is called Belfast.
> 

# SpecLog Abstraction

SpecLog’s model is as follows:

![Screenshot 2025-09-12 at 8.55.29 AM.png](sources/Low%20End-to-End%20Latency%20atop%20a%20Speculative%20Shared%20L%202611aa5d8a0e80dc83b9e7000fb90600/Screenshot_2025-09-12_at_8.55.29_AM.png)

As it follows a durability-first approach:

1. Upcoming records from the upstream is first placed into a shard to be made durable
2. When data is to be consume, each shard produces for their batch an ordering that makes up a global ordering for all records. These are labelled as speculative
3. The downstream application that received this can immediately consume the data BUT it cannot ‘use’ it yet. It has to wait for confirmation about the global ordering it received. 

And that’s it, quite straightforward. The real meat is on how this speculation can be made in a not so haphazard way.

# SpecLog’s Fix-Ante Ordering

As mentioned, the idea is to give the shards quotas. In this context, the collection of all quotas is called a global cut; fix-ante ordering is a series of this global cut.  Say for example we have 3 shards: $S_0,S_1,S_2$. The first global cut could be (1, 2, 1). This would mean the S_0 must report 1 record, S_1 2 records, and S_2 1 record. From here we can give an order that everyone can figure out:

S_0: 1

S_1: 2, 3

S_2: 4

In the next round, if the quotas stay constant, the global cut will be (2, 4, 2). The idea of the computation lies in remembering the total sum of the previous global cut. The distribution of value is still the same but for each value, add the previous cut’s sum.

previous sum: 4

S_0: 4 + 1 ⇒ 5

S_1: 4 + 2, 4 + 3 ⇒ 6, 7

S_2: 4 + 4 ⇒ 8

As you’d imagine, the sum of this cut would be 8.

---

The paper also laid down some equations:

$$
S=\sum_{k=1}^{n}{d_{(i-1)k}} + \sum_{k<j}{q_{ik}}\tag{1}
$$

$$
E=S+q_{ij}\tag{2}
$$

This will compute the order for the jth shard in the ith cut. Particularly, it will have [S + 1, …, E]

- You’ll find that the first term of (1) is the previous sum and the 2nd term is all the preceding cuts.
- E is the last term in the series which is just the size + the initial offset

This is more or less similar to the calculations I described before

---

# SpecLog’s Error Handling & Linearizability

In the event of error, i.e. A shard’s death (which can be detected using heartbeats), the predicted global cut can be invalidated. When a shard fail, the sequencer wouldn’t be able to determine how many records it reported. Furthermore, a shard only report to the sequences once it hits it’s quota in the speculative cut. This means that if a shard dies it will report 0 records. The sequences then would return a global cut with all entries incremented except for the cut of the failed shard.

One more issue deals with linearizability. Consider for a moment 2 transaction and 2 shards S_0 and S_1. Say T2 is made durable in S_1 and T_1 following T_2 is made durable in S_1. The total ordering would be:

1. T1
2. T2

Which won’t make sense since T2 is made durable first. This deals with the append action to a shard. Basically, it means that the shard won’t tell the client the record has been made durable until there’s a reply from the sequencer which will preserve linearizability of the transactions.

> I actually have no real idea on how they would do this. It’s most likely not by moving T1 into a different shards. What I imagine happening is that the sequencer will just make S_0 has 0 cut and forces T1 to be deferred to next round of reporting. Since due to the numbering convention used, the records in lower shards will always have an earlier ordering compared to shards within the same round.
> 

# Belfast Architecture

![Screenshot 2025-09-13 at 10.45.13 PM.png](sources/Low%20End-to-End%20Latency%20atop%20a%20Speculative%20Shared%20L%202611aa5d8a0e80dc83b9e7000fb90600/Screenshot_2025-09-13_at_10.45.13_PM.png)

This is overall what the system that uses SpecLog looks like. It should be quite simple to understand other than the little surprise that each shard has a primary and a back up storage. What is interesting about Belfast I’d say is the numerous optimisation they made for SpecLog.

# Varying Shard Leader’s Quotas

As mentioned before, dealing with quota size can be tricky and it may cause some adverse effect on performance. Adding too big of a quota means a lot of no-ops and too small of a quota means a lot of records would be delayed to the next round of reporting. Both of these can increase the end-to-end latency.

## Rate-Based Quotas & Lag-Fix

Simply enough, we can adjust the quota based on what the shard leader typically give back. However, doing so won’t work so well in the presence of bursty ingestion. The problem lies with the fact that a in bursty scenarios, the records that goes beyond the current quota will be deferred to later rounds; the sequencer won’t even see them.

That’s fine, in Belfast we actually report to the sequencer as soon as our speculative quota is fulfilled. This would solve half the problem.

The other half is the fact that we still have to oblige to the quota else we risk miscalculation. Here’s an example: imagine there are 2 shard leaders A and B. Both of these has a quota of 1. It just so happen for a particular round, A received 5 records in total. A then report this to the sequencer immediately. And say, B also reported 1. One of A’s record and B’s record will be considered for the current global cut but the rest of A’s transaction will still be deferred since they goes the current quota for A. The best we can do with what has been considered so far is to adjust A’s quota for next round.

But, we can do better. Let’s just ask B to give the sequencer 4 no-op records. This means the sequencer can immediately compute the global cuts for the next 4 rounds. This is what’s meant by lag-fix.

## Speculation Lease Windows

The lag-fix works great but it can’t really handle a more permanent change in ingestion rate. That is, imagine if the reporting rate fluctuates a little bit but overall has an expected value. The mechanism of detecting and making decision based on the rate on such a small interval won’t work as well. And, with the lag-fix mechanism, there would be a lot of no-ops inserted. Since the issue is on the observable rate, we can just increase it. Belfast cut discrete time intervals into ‘windows’.

Something to note here is that Belfast doesn’t use a time-based window. The sequencer will tell the shards the cut and let them know how many rounds that cut will be used. At the end of a lease window, the sequencer will let the shards know the next set of quotas. During a window, the shard will communicate information on it’s ingestion rate. This helps the sequencer to figure out the next set of quotas.

## Stragglers & Many Shards

Because of the window, we can play a sneaky game to deal with struggler shards. Particularly, if a shard is detected to be super slow, the sequencer can just simply ignore them by assigning them a quota of 0.

This idea of ignoring shards also has further uses. Particularly, in a situation where there are a lot of shards (millions, lets say). The thing to note here is that not everybody has to participate in the downstream application (this is part of the fix-ante framework). So what can be done is to accept reports from a subset of a the shards at a time. Belfast does this by counting the round number and taking a modulus fixed to a number. So, we can technically do:

$$
S_{active}=\{i | i\%2=0\}
$$

This would mean that the even and numbered shards take turn every round. You can of course increase the modulus number to make the group even smaller.

# References

https://www.usenix.org/conference/osdi25/presentation/bhat
# MAKO: Speculative Distributed Transactions with Geo-Replication

# Introduction

MAKO comes from the question of whether or not it’s possible to make the efficiency of a distributed system to be the same as if there’s only one machine in that system in a geo-replicated settings. If the answer to this question is yes, then:

1. Systems can decrease the number of machines they use to meet their demand
2. Some applications that require high throughput in a geo setting would become possible

The authors of the paper finds that the tight linking between the coordination and replication process may be some of the reason why the performance of distributed systems are not as good compared to single machine systems. Others argued that coordination and replication should be tightly linked to avoid duplicated functionalities. However, in a WAN context, the network overhead is too big and the system can greatly benefit if those two quantities are disjointed.

How? By speculative execution which can mask that big network overhead. In distributed systems, when you have a lot of data, you would typically split them into shards. Each shard has a leader that has authority over it. If someone sends in instruction that uses data only within that shard, then it’s fine. Problem comes when that operation uses data across shards. You’ll have to make sure that operation is consistent amongst the shards. What has been done in this regard is to use 2-Phase Commit (2PC) and record every leader’s decision in case of failures (so the system can recover and be consistent). Problem with this is that 2PC requires **everyone** to participate. It doesn’t support a certain subset, even with majority, of the leaders for it to work.

The improvements being proposed by MAKO is to use 2PC in a speculative manner i.e. Instead of waiting for everybody to vote, just execute the transaction. If there is something bad, simply rollback the affected transactions. This of course sounds simple but finding which transactions are affected and how to manage them in an efficient way is a difficult task though it is another improvement MAKO promises to make.

> Overall, MAKO is going to:
1. Introduce an architecture that allows it to safely speculate without having to wait for the results of geo-replication
2. Keep track of very little information that is efficient to collect and
yet sufficient to avoid unbounded cascading aborts.
> 

# Main Mechanisms

![The basic setup](sources/MAKO%20Speculative%20Distributed%20Transactions%20with%20Geo%202601aa5d8a0e807995affc59d1dc4969/Untitled_Diagram.drawio_(3).png)

The basic setup

When a transaction arrives:

1. Execution: The transaction is appointed to a shard leader which will execute it
2. Certification: After the shard leader executes the transaction, it shares it with the other leaders and waits for their approval. Once everybody agrees that the transaction is good, it would be labelled as certified. Future transaction that wants to read value related to this transaction will read it from the transaction (this will play a part later in the abort process).
3. Replication: Once certified, the shard cores will start a replication process to their replicas by some consensus protocol. Once replicated, the client is notified. If replication fails, the transaction, and other transactions that depends on it will be aborted.
4. Replay: The replication stage for a particular shard is actually split into multiple cores for more efficient replication. This means how the replicas execute must be given care less you risk them to do operations with missing dependencies

# Speculative Execution

How come a simple execution deserves it’s own section? Because it provides a basis for later uses.

A shard leader in MAKO can accept 3 kinds of request:

- one-shot transaction: contains both read and write commands
- read transaction: purely read commands
- write transaction: purely write commands

Based on the descriptions so far, it shouldn’t be a surprise that these leaders would need to keep processed transactions for quite some time (in case need to rollback). As such, records that have been read and written to are stored. Particularly, they’re organised into 2 sets:

- Read set: stores records that has been read along with their version
- Write set: stores records that has been written to. These are not installed until the transaction is certified.

A One-shot transactions would update both sets.

---

**But why split them into a read set and a write set?** The problem lies with concurrency. 

For reads, you’d need a sort of anchor where you can assert your current transaction used so and so’s data. You need to store this information. Even if you don’t use a set, logically you’ll still need to store them somehow

For writes, the writes themselves are not used until the transaction is certified i.e. Everyone is okay with it. So in this case it acts as a buffer.

But how can we represent versions and how can we be sure that concurrent transaction won’t kill one another? Details described below

---

## Versioning Reads - Vector Clock

To version a read, something called a vector clock is used. Sounds fancy but it’s honestly is just a vector. if you have $N$ leaders, your vector clock would have $N$ entries:

$$
C=\begin{pmatrix}c_1\\c_2\\...\\c_N\end{pmatrix}
$$

the $i^{th}$ entry represent the clock of the $i^{th}$ shard leader.

Hopefully now you can see how versioning can be done. When a record is pulled from a shard leader, the current count for that leader is fetched and stamped. To avoid race conditions (because the shard may run with multiple threads), an atomic operation like fetch-and-add is used.

You can see where the versioning works with the 2PC approach in the execution stage:

1. Lock: For all records in the WriteSet, request for locks from all relevant shards. Transaction abort if cannot get all locks.
2. GetClock: Each leader will then increment their clock and shares it the the coordinator. The coordinator then combines the vector clock and all the clock versions in the read set to create a version of the commit. The maximum known clock value will be used for each entry
3. Validate: Make sure the versions of the ReadSet did not change (we only locked the Write set). If change, abort transaction
4. Install: Tell everyone to install the new writes speculatively

---

Maybe you’d think “how come we need to combine the shard’s clock with the read set and take the maximum? Won’t the shard’s clock always be the max?”. Well, I have no idea as well. It should be that the clock can be reduced otherwise the leader’s clock will always be the max. The paper however never mentions this. This is only my speculation: maybe the clock is reduced as the transaction is rolled back OR the clock is simply fetched and incremented. It never gets incremented at the leader unless the leader is the coordinator. I’m championing the latter

---

## Dependency Management With Vector Clock

MAKO preserves a property with 2 transactions vector clock if they depend on each other:

- If T2 depends on T1, then T1’s clock will be bigger than equal to T2 pair-wise
- If T3 depends on T2 and T2 depends on T1, then T3 depends on T1

# Replication & Record Replay

Replication’s stage goes as you typically would with any consensus protocol. Just something that I would like to emphasise again is on the fact that the shards are split into multiple different cores. This means mako got 2 kinds of log:

- Transaction log
- Per-core replication log - Stream

The transaction log belongs to the leader. 

The per-core application log is a partitioned transaction log where each partition is handled by a core of the shard. Each partition is completely independent from one another. This is good for scalability but bad for consistency.

Now, the replay stage. Before getting knees deep, lets consider why care must be taken with dependencies. There are 2 reasons and both of them involves shard failures:

- Across shard inconsistency
- Across Transaction inconsistency
    
    ![Screenshot 2025-09-12 at 2.14.52 AM.png](sources/MAKO%20Speculative%20Distributed%20Transactions%20with%20Geo%202601aa5d8a0e807995affc59d1dc4969/Screenshot_2025-09-12_at_2.14.52_AM.png)
    

Different naming aside, they basically mean that there could be problem if let’s say op#1 never executes because of shard failure by op#2 is successfully replicated if op#2 depends on op#1. Another case is when T2 (op#3, op#4) are successful and T1 fails (either op#1 or op#2 fails) and that stuff in T2 depends on stuff in T1. If the different cores are just blindly replicating, such situations could happen.

How to deal with this? Well, they’re dependencies related and luckily we have something that works within that domain we can try; the vector clock. What we need here is to make sure the dependencies of the replicated transaction is present. From the properties this means the vector clock smaller than the current clock must already be replicated. For this, let me introduce: vector watermark. 

A vector watermark is essentially the same thing as a vector clock (both a vector with $N$ entries) but the computation of a vector watermark is a little different: 

![Screenshot 2025-09-12 at 2.22.26 AM.png](sources/MAKO%20Speculative%20Distributed%20Transactions%20with%20Geo%202601aa5d8a0e807995affc59d1dc4969/Screenshot_2025-09-12_at_2.22.26_AM.png)

Basically, for each entry, you take the minimum of the latest transaction that has been replicated; this is the shard watermark. You do this for each shard to get $N$ values in total making up the vector watermark.

The vector watermark is used to test whether or not the current transaction can be marked as replicated. Once the vector watermark is bigger than the current transaction (and the fact that the transaction has been replicated) only then can we say the transaction is replicated i.e. Tell the client about it. The logic behind this is because of the property hold. If the vector watermark is bigger than the currently replicated transaction, then it must be that all of that transaction’s dependency has already been replicated ⇒ That transaction can also be labelled as replicated as well.

---

The non-replicated transactions are free to be in the replication process. Take for example say $S_0$ (9,*,*) and (7,*,*) are replicated. The vector stream now updates to $\begin{pmatrix}7&2&4\end{pmatrix}$. Even if we so, the other shards will not be affected until the preceding transactions of $S_0$ are committed since the comparison of bigger and smaller is pair-wise.

---

# Failure Management

Execution are split into discrete time interval known as epoch…

# References

https://www.usenix.org/conference/osdi25/presentation/shen-weihai
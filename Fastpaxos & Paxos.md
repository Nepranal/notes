# Introduction

Fastpaxos, is an improvement on Paxos as a consensus protocol. Though an improvement, it works very similarly to Paxos. As such, this note is going to encompass both similarly with the original paper for Fastpaxos.

# Paxos's Problem Statement

The problem that paxos's solving regards with choosing a value from a set of proposed values. To put it in perspective, consider a scenario where you would like to store an action done by a client. These usually comes in the form of transactions. The problem is centered around storing these transactions. How would one go about designing such a system?

You can have a central data center that stores every single transactions made. However, this won't be so scalable as the number of your client increases as this central computing node will become a bottleneck in your system.

Since the issue is with one centralized node, a solution would be to have multiple copies of these nodes. Now your system can scale better due to the absence of a bottleneck but, these replicas of the data center may record different transactions. This is where Paxos is supposed to be use; how can we sync the transaction logs of the replica? Hopefully the problem of choosing a value from a subset of them makes sense now

# System Composition

Paxos assign roles to the processes involved. These are:

<ol>
    <li>Proposer: Proposes values</li>
    <li>Learner: Learned chosen value</li>
    <li>Acceptors: Accept a single value</li>
    <li>Round Coordinator: Decides on a value</li>
</ol>
'Round Coordinator' probably won't make sense for now but you'll see what it's about soon enough.
<br>
<br>
The overall protocol may follow a simple paradigm like client-server. It is also not necessary that a single process is only assigned a single role. For instance, the client can both be the proposer and learner and a replica may also be learner while being an acceptor.

# Paxos Protocol

Now I'm going to go into the protocol. Note that the way this was presented in the Fastpaxos paper, was to introduce the features of the protocol incrementally. I will do something similar here.

## The Setup

Paxos runs in multiple rounds. A round is labelled with an integer. Round execution doesn't have to be in-order, and multiple rounds can execute concurrently. It's important to note that the round here doesn't really mean anything other than being a mean to be something the value can be related to. This is important as we'll see soon.

I think it'd be nice to know what Paxos is trying to do with it's protocol and just to see how simple Paxos is compared to current protocols available in the literature. Paxos tries to achieve 2 things:

<ol>
    <li>Consistency: Once a value is chosen, no other values can be picked.</li>
    <li>Nontriviality: The chosen value can only come from proposed values</li>
</ol>
Paxos also only deals with non-byzantine failures. What this mean is that the proposers can't 'lie'. The only failure it handles is when a replica suddenly fails (crashed, power outage, etc. Essentially becoming uncontactable).

## Nontriviality

Part of the system is to have acceptors accept values from proposers. The way this is done is to accept a majority of the incoming values which would enforce only a single value can be chosen. With this, the nontriviality property can be fulfilled nicely.

## Consistency

Before getting into it, let's review what kind of information the key players in the protocol knows.

The acceptor knows:

<ul>
    <li>rnd[a]: Highest number in which acceptor 'a' has participated</li>
    <li>vrnd[a]: Highest round number in which 'a' has casted a vote</li>
    <li>vval[a]: The value a chose in vrnd[a]</li>
</ul>

The round coordinator knows:

<ul>
    <li>crnd[c]: Highest round number in which coordinator 'c' has begun</li>
    <li>cval[c]: The value picked by the coordinator in crnd[c] (none if c hasn't picked a value which is possible under when no majority can be established; a 'split vote')</li>
</ul>

Then, for any round i:

<ol>
    <li>
        <p>(a). The round coordinator checks if crnd[c] < i. If it is, set crnd[c] = i and contact the acceptors within the system to ask them to participate in this round</p>
        <p>(b). If the rnd[a] < i then the acceptor aggrees to participate in the round and responds with vrnd[a] and vval[a]. Otherwise the request is ignored.</p>
    </li>
    <li>
        <p>(a). If crnd[c] = i, and that the coordinator has received a majority of 1(a) messages, then by using some scheme, c picks out a value v from the acceptors. After picking out a value, the coordinator asks the acceptors to accept the value</p>
        <p>(b). When the acceptor receives a request to vote, it will vote for a value v and store it (amongst doing other updates). The acceptor will also send messages to learners. Once a learner receives a majority amount of 2b messages, the value v will be 'chosen'. Do note that if the incoming message has round i < rnd[a] or vrnd[a] = i, the message will be ignored</p>
    </li>
</ol>

## Picking A Value in 2a

The real meat of the issue is how Paxos picks a value in after it asked everybody to vote. It's actually quite simple: pick a value from the highest round that some people has voted for.

Why? Lets note the consistency property:

> CP: For any round $i, j$ where $j>i$, if a value $v$ has been chosen or is yet to be chosen at round $j$, then no other value, except $v$, can be chosen at round $i$.

or put it another way, if a value $v$ has already been chosen, then no other value can be chosen, except $v$, at a later round which implies that no different values can be chosen in different rounds.

The values that can be picked by the acceptor is only the one sent out by the coordinator. This means that if at a particular round, the value $v$ is such that it's the only value ever seen by the acceptor, we can satisfy CP.

> $CP(v,i)$: For any round j<i, no value other than $v$ can be or may yet to be chosen.

To find a value that fits this description, and to draw the proof, let's go through some observation that can help us.

> Observation 1: A value $v$ has been or might be chosen in round j only if there is a majority set Q s.t. each acceptor $a$ in Q has $rnd[a] \leq j$ or has voted for $v$ in $j$.

A majority set is just a set that contains majority of acceptor. You can imagine that if we have a set of acceptors, the majority set is just any one of all possible majority you can create. The first observation basically says that for a value $v$ to be picked at a round $j$, it's necessary that we have enough people who can vote for it or has already voted for it.

Observation 1 leads to observation 2 & 3 because there must be some sort of overlap between majority.

> Observation 2: If there exist a majority Q in round $j$ where every acceptor has $rnd[a]>j$ and has not voted in round $j$, then no value can be chosen or will ever be chosen in round $j$

> Observation 3: If there is a majority Q and a value $v$ s.t. every acceptor $a$ in Q has $rnd[a]>j$ and either has voted for $v$ or has not voted, then at that round $k$ either a value v has been picked or none at all.

Observation 2 and 3 are quite similar.

We can now start the proof. Lets define some values

$$
vr(a) = vrnd[a]\\
vv(a) = vval[a]
$$

Basically, $vr(a)$ is the latest round the someone in the acceptor group has voted in and $vval(a)$ is the corresponding value. Let $k$ be the maximum possible $vr(a)$. By the protocol, we know that $k < i$.

2 possibilites: for $k=0$ and $k>0$. At $k=0$, the coordinator can pick any value $v$ and it will always satisfy $CP(v, 0)$ so long as $v$ comes from the set of proposed value.

If $k>0$ then let's pick a value $v$ that comes from one of the acceptor with who voted at round $k$. We have to show that this value satisfies $CP(v,i)$. The round $j<i$ can be considered into different cases.

- When $k<j<i$, in this case, no one has voted yet. So we're good by observation 2
- When $k = j$ it must be that the value voted comes from the coordinator so it must be $v$. By observation 3, we're still good
- When $k<j$ we can assume by induction that these previous cases were handled.

## Personal Remark

All of these sounds kinda complicated but the main idea of the proof is to make sure, once a value $v$ is picked, there can no longer be any other value. The only chance a value can be picked is when the protocol is about to start. Then you can just see that the subsequent rounds is just an effort in making sure everyone votes for $v$ and that's it.

In the paper, in this section, they actually talk about how there's only one value in any subsequent round. You can check 'Figure 1: The coordinator’s rule for picking value v in phase 2a of round i'

# Progress Requirements

In order to make progress, Paxos makes use of a 'leader'. It means that, each round, there is a single node that everyone sends messages to which I believe basically means that each round should have only a single coordinator. You must then have an algorithm to choose a leader for some particular rounds. This however won't be discussed in the paper.

A notion that we need to define progress is what's referred to as a 'good set'. Basically, in this set, the nodes will behave as they were defined to do for example responding to a message within reasonable time. In another word, we call the nodes in the good set to be 'non-faulty'. So, if the nodes fail, or is experiencing non-reasonable delays, then they are not part of the good set. Nodes can come in and out of the good set as as their environment change.

> Being nonfaulty and being good are temporal properties that depend on future behavior

There can be more than 1 good set in your system. For instance, imagine that the network in your system is partitioned. Additionally, the union of two good sets will not imply another good set but a subset of a good set will always be a good set.

Given a learner `l`, proposer `p`, coordinator `c`, and a set of acceptors `Q` we define the following conditions as `LA`:

1. $\set{p,l,c} \cup Q$ is a good set
2. `p` has proposed a value
3. `c` is the one and only coordinator that believes itself to be the leader

If `LA(p,l,c,Q)` holds forever, `l` must eventually learn a value.

The current algorithm requires some tweaking in order to achieve this particularly with LA3 (since not much you can do with LA1 and LA2). How can we make sure that there can only be one round coordinator?

# The Complete Algorithm

Here are the following modifications

> CA1. If an acceptor `a` receives a 1a or 2a message from a coordinator for round j and $rnd[a] = i$ and $i>j$, then the acceptor responds with a message saying round i has begun. (This is only sent if coordinator of round i and j are different. Otherwise the message is just ignored)

> CA2. A coordinator c performs an action only if it believes itself to be the current leader. It begins a new round i only if either crnd[c] = 0 or it has learned that a round j has been started, for some j with crnd[c] < j < i.

Both of these is so that LA3 can be satisfied.

During these whole exchanges however, there is a possibility of a message being lost. To account for it, each agents will keep on sending messages it has last sent (this is CA3). This part is not so necessary and the paper later on talks about how this can be optimised (so you don't need to send an infinite amount of messages).

There's also one more thing that's labelled as CA4:

> A nonfaulty agent eventually performs any action that it should.

# Proof

Say the time T_0 is when LA start to holds and the it holds for a long time

1. From time T_0 onwards, there will be no other coordinator
   - By CA2 and LA3
2. By some time $T_1 \ge T_0$, there is a round number i such that from $T_1$ on, $crnd[c] = i$
   - By step 1, we know that no more coordinators, so for all acceptors, they will have one particular round. The coordinator eventually will not learn of a round number higher than $i$.
3. From $T_1$ on, $rnd[a] \le i$ for all acceptors $a$ in Q.
   - Assume otherwise. By CA3 and LA1, we know that $c$ will continuously send a message to the acceptors. By CA4 and the assumption, eventually there's an $a$ that gives out a round higher than $i$ which contradicts step 2
4. By some time $T_2 \ge T_1$, coordinator c will have started phase 2 of round i
   - Assume it never does. By LA1, CA3, CA4, and step 3, we know that all $a$ will send 1b messages infinitely to $c$ because, by the same rules along with LA3, c keeps sending 1a messages.
     By LA1, LA2, and CA3, we know that $p$ keeps sending a proposed value to $c$ which it must eventually receives by LA1.
     $c$ must eventually recevied the messages from both the acceptors and proposers by LA1. By this point, the 2nd phase must've started which is a contradiction.
5. Eventually l learns a value.
   - The proof is kinda similar to previous steps

# FastPaxos

# Reference

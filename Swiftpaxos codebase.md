# Introduction

The following article attempts to describe the code structure of the swiftpaxos codebase.

# Client

The client code is split into 2 fundamental parts:

1. `buffer.go` - Higher level functionalities
2. `client.go` - Lower level functionalities: Contact replicas, Contact master, etc.

As you’d expect, a buffer object has a client object in it

```go
// main.go
cl := client.NewClientLog(...)
// ...
b := client.NewBufferClient(cl, ...)
```

Let’s ignore all of the important functionalities for now, and just see how the client is being used.

As you may have expected, the client may behave differently depending on the protocol that you’re using. For instance, in Swiftpaxos, the client send messages to multiple replicas at one while in EPaxos, the client will only send a single to message to the closest replica. This leads to the following code block

```go
if err := b.Connect(); err != nil {
		log.Fatal(err)
}
if p := strings.ToLower(c.Protocol); p == "swiftpaxos" {
	cl := swift.NewClient(b, len(c.ReplicaAddrs))
	// ...
	cl.Loop()
} else if p == "curp" {
	// ...
	cl := curp.NewClient(b, len(c.ReplicaAddrs), c.Reqs, pclients)
	// ...
	cl.Loop()
} else {
	waitFrom := b.LeaderId
	if b.Fast || b.Leaderless || c.WaitClosest {
		waitFrom = b.ClosestId
	}
	b.WaitReplies(waitFrom)
	b.Loop()
}
```

If we just ignore the stuff like `connect()` and `NewClient()` (which are hopefully clear what they’re meant to do), the next code blocks of interests will be `Loop()`.

`Loop()` is where, if you had a hunch as to how the codebase works, the main actions of the client code happens. This is the code block where the messages are being sent, etc. While the previous lines of codes were used primarily for setting up the client. Let’s have an overview at what this block does.

By how it’s written, Loop() does both the sending and the receiving part of the client. When you read it, you’ll find that the asynchronous part is run asynchronously. The overall logic I think presents itself quite clearly. For the receiving part, it will wait until something is received:

```go
go func() {
		for i := 0; i <= c.reqNum; i++ {
			r := <-c.Reply
			// ...
	}()
```

When something came, will just record how long it took and log it

```go
if i != 0 {
	d := r.Time.Sub(c.reqTime[r.Seqnum])
	m := float64(d.Nanoseconds()) / float64(time.Millisecond)
	c.PrintDebug("Returning:", r.Val.String())
}
```

And we repeat. The codebase though gives a little more flexibility: windows and sequential. Windows limit how many messages can be ‘on-flight’ concurrently, while sequential determines whether to process a transaction one-by-one. As you may have expected, the sequential code is quite simple, since it just marks that a command is done:

```
// receiving side
if c.seq || (c.syncFreq > 0 && i%c.syncFreq == 0) {
	wait <- struct{}{}
}
```

```go
// sending side
if c.seq || (c.syncFreq > 0 && i%c.syncFreq == 0) {
	<-wait
}
```

Within go’s syntax, `wait` is a channel. having `←wait` means reading from a channel and you can guess what the other thing is. With go, a code will block until they receive something out of that channel.

As for the window code

```go
// Receiving
if c.window > 0 {
	cmdM.Lock()
	if cmdNum == c.window {
		cmdNum--
		cmdM.Unlock()
		wait <- struct{}{}
	} else {
		cmdNum--
		cmdM.Unlock()
	}
}
```

```go
// Sending
if c.window > 0 {
	cmdM.Lock()
	if cmdNum == c.window-1 {
		cmdNum++
		cmdM.Unlock()
		<-wait
	} else {
		cmdNum++
		cmdM.Unlock()
	}
}
```

The code here basically says that if you hit the max, from sending side, it will lock up until released by the receiving side.

So that’s nice and all, but remember earlier I mentioned that on top of having to deal with buffer.go, there’s one more layer: the protocol’s client. Where does that play a role here? Well, it doesn’t really play that much of a role here at all. Most of it is handled within the sending code itself. However, the receiving does play a part here. Notice that we receive messages from the Reply channel. There’s a layer put before it, that decides whether a transaction is done. This one is quite explicit, so I will defer it until after the replica section. But, just for reference, here’s some code from the Swiftpaxos client:

```
// swift/client.go
func (c *Client) handleFastAndSlowAcks(leaderMsg interface{}, msgs []interface{}) {
	// ...
	c.RegisterReply(c.val, cmdId.SeqNum)
	// ...
}
```

```go
// client/buffer.go
func (c *BufferClient) RegisterReply(val state.Value, seqnum int32) {
	t := time.Now()
	c.Reply <- &ReqReply{
		Val:    val,
		Seqnum: int(seqnum),
		Time:   t,
	}
}
```

# Master

The job of the master in these sorts of protocol is to hold some meta information and provide configuration information e.g. The set of nodes set as replicas. So, you can imagine it being use when the replicas and the clients are connecting. Additionally, it also helps with recovery procedures. The main block of code to pay attention here is run() within master.go

The master will wait until all registered nodes are connected

```go
for {
	master.lock.Lock()
	if len(master.nodeList) == master.N {
		master.lock.Unlock()
		break
	}
	master.lock.Unlock()
	time.Sleep(time.Second)
}
time.Sleep(2 * time.Second)
```

Choose the leader

```go
for i := 0; i < master.N; {
	var err error
	addr := fmt.Sprintf("%s:%d", master.addrList[i], master.portList[i]+1000)
	master.nodes[i], err = rpc.DialHTTP("tcp", addr)
	if err != nil {
		master.Printf("error connecting to replica %d (%v), retrying...", i, addr)
		time.Sleep(time.Second)
	} else {
		btlReply := defs.NewBeTheLeaderReply()
		if master.leader[i] {
			err = master.nodes[i].Call("Replica.BeTheLeader", &defs.BeTheLeaderArgs{}, btlReply)
			if err != nil {
				master.Fatal("Not today Zurg!")
			}
			defs.UpdateBeTheLeaderReply(btlReply)
			if btlReply.Leader != -1 && btlReply.Leader != int32(i) {
				master.leader[i] = false
				master.leader[int(btlReply.Leader)] = true
			}
			master.nextLeader = int(btlReply.NextLeader)
		}
		i++
	}
}
```

Looping to see if need to choose new leader

```go
for {
	time.Sleep(3 * time.Second)
	new_leader = false
	for i, node := range master.nodes {
		pingNode(i, node)
	}

	if !new_leader {
		continue
	}
	if master.nextLeader != -1 {
		if beTheLeader(master.nextLeader) == nil {
			continue
		}
	}
	for i := range master.nodes {
		if beTheLeader(i) == nil {
			break
		}
	}
}
```

If you follow the details of the code you may find it weird how the variables used as the reference in the functionalities were never even touched yet they were immediately used. Kinda feels like magic for a little bit. This is made possible by copious amount of RPC calls being used which then update those variables. The main functionality I’m showing here is the main body of the master code. For example, take master.NodeList. At no point was it initialised but it’s value is modified in Register(). And if you check some of the client code I skipped, you’ll find that the client actually does make an RPC calls for these functions. Personally, these sorts of aspect is what makes understanding the codebase a nightmare. But hopefully, by tearing it down little by little, it can become clearer. For now: ignore implementation details and focus on higher level ideas

# Replica

Here comes the big part. Surprisingly enough the setup is quite minimal from outer pieces of code. The initialisation part is quite similar to clients:

```go
// ...
replicaId, nodeList, isLeader := registerWithMaster(addr, maddr, port, c)
//...

switch strings.ToLower(c.Protocol) {
case "eppaxos":
	log.Println("Starting eppaxos replica...")
	rep := eppaxos.New(c.Alias, replicaId, nodeList, !c.Noop, false, false, 0, false, f, c, logger)
	rpc.Register(rep)
case "swiftpaxos":
	log.Println("Starting SwiftPaxos replica...")
	swift.MaxDescRoutines = 100
	rep := swift.New(c.Alias, replicaId, nodeList, !c.Noop,
		c.Optread, true, false, 1, f, c, logger, nil)
	rpc.Register(rep)
case "curp":
	log.Println("Starting optimized CURP replica...")
	curp.MaxDescRoutines = 100
	rep := curp.New(c.Alias, replicaId, nodeList, !c.Noop,
		1, f, true, c, logger)
	rpc.Register(rep)
case "fastpaxos":
	log.Println("Starting Fast Paxos replica...")
	rep := fastpaxos.New(c.Alias, replicaId, nodeList, !c.Noop, f, c, logger)
	rpc.Register(rep)
case "n2paxos":
	log.Println("Starting N²Paxos replica...")
	rep := n2paxos.New(c.Alias, replicaId, nodeList, !c.Noop, 1, f, c, logger)
	rpc.Register(rep)
case "paxos":
	log.Println("Starting Paxos replica...")
	rep := paxos.New(c.Alias, replicaId, nodeList, isLeader, f, c, logger)
	rpc.Register(rep)
case "epaxos":
	log.Println("Starting EPaxos replica...")
	rep := epaxos.New(c.Alias, replicaId, nodeList, !c.Noop, false, false, 0, false, f, c, logger)
	rpc.Register(rep)
}

rpc.HandleHTTP()
l, err := net.Listen("tcp", fmt.Sprintf(":%d", port+1000))
if err != nil {
	log.Fatal("listen error:", err)
}
http.Serve(l, nil)
```

But, the initialisation of the object itself is quite complicated. It depends entirely on which protocol you’re using as well. This setup hopefully makes some sense. During the operation, the initiators are the clients. The clients interact with the replicas and masters through RPC calls. So, the replicas and master will just be on standby as you’ll see shortly.

---

Just a little side note, you’ll find that within registerWithMaster(), the Register() RPC is called

---

Let’s just simplify a bit and take Paxos’s initialisation.

```go
if r.IsLeader {
	r.BeTheLeader(nil, nil)
}

for i := 0; i < len(r.defaultBallot); i++ {
	r.defaultBallot[i] = -1
}

r.prepareRPC = r.RPC.Register(new(Prepare), r.prepareChan)
r.acceptRPC = r.RPC.Register(new(Accept), r.acceptChan)
r.commitRPC = r.RPC.Register(new(Commit), r.commitChan)
r.commitShortRPC = r.RPC.Register(new(CommitShort), r.commitShortChan)
r.prepareReplyRPC = r.RPC.Register(new(PrepareReply), r.prepareReplyChan)
r.acceptReplyRPC = r.RPC.Register(new(AcceptReply), r.acceptReplyChan)

go r.run()
```

The others, at least swiftpaxos, also shares similar logic of course with extra stuff here and there. The important parts here is the creation of the RPC and run(). I think the name maybe explains a little bit to how these protocols work, but to my experience they can at times be confusing (at least with swiftpaxos).

The run code, is hopefully up to your expectations. It just waiting on some of the channels the replica initialises. I’d show you what it looks like but at this point, it’s kinda useless. I think it’s much better to go through it if you have an understanding of the protocol themselves else you may be wasting your time

# Reference

https://github.com/imdea-software/swiftpaxos

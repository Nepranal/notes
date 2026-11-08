# Data Structures That Power Your Database

We can come up with a simple database using some bash scripting

```bash
#!/bin/bash


db_set () {
echo "$1,$2" >> database
}

db_get () {
grep "^$1," database | sed -e "s/^$1,//" | tail -n 1
}
```

Surprisingly, this is not bad for something so simple. This database system will append data to the very end of a file called `database` as a key-value pair. The implementation doesn't really handle duplicate keys however and will just simply read the latest value with that key

This implementation is particularly good in terms of writing data (`db_set()`) because it simply appends to the end of the file. This is also something that is commonly used by other database systems which are usually called logs, particularly, an append-only log (with more features, of course)

The reading performance (`db_get()`) however is not as good because you may have to go through the entire file just to find your key; an $O(n)$ complexity operation.

To improve our `db_get()` performance, what we can do is to add information on the whereabouts of the data or some information when we're at a particular point of processing the file. We can do this by using data structures or also known as indexes.

Adding an index however may affect other performances of the database particularly, your write performance. This is because adding index means deriving another structure with the same underlying data. If you want to say insert new records, the operation may no longer be as simple as appending to the end of the logs and may instead involve modifying the index's current structure; this can be costly. Though, it has the potential to speed up your queries.

# Hash Indexes

This one sounds as it is, we're going to use a hashmap. The idea is to store the location of the key (e.g. Byte offset) with an in memory hashmap. Then, as you specify a particular key, and then that key exists, you can immediately seek to that part of the file

> Thankfully we don't update data in place. If we do, this won't work as you may need to update every other keys as you update your data. With an append only log, you only need to update the byte offset of your keys in the hashmap

This index is pretty useful for tasks that doesn't have a lot of keys in it. Usually, a requirement would be that all keys must fit in the hashmap stored in memory

# Log Compaction

So far we've only discuss appending key-value pair to files. We however can't go on forever especially if a lot of the new keys are simply duplicates. An extreme example of this is if we only have one key but we're forever updating it. Our logs will grow an eventually we will run out of space. This should not be. A solution to the above problem is to compact the logs that we have i.e. Removing old records from our file and keeping the latest version

We do this by splitting the logs into small segments that has a maximum size. Once it's bigger than it, we "close" the segment by writing to a new segment file keeping only the latest entry of a particular key. We can push this idea further by compacting multiple segments at the same time and merging them together

We also keep the old/frozen segments for some time. During this compaction process, they will be used for any read-write operations. They'll be discarded once incoming queries are directed to use the new segments instead.

Each segment has their own hashtable and segments are ordered. When a read query comes, we will first check the most recent segment. If doesn't exist, we check 2nd most recent and so on. The assumption here is that compaction and merging will keep segments relatively small so you don't have to go through many segments to find your data. In a way, we kinda have a series of the `database` file now

> Some questions here would then be, how big should a segment be?

Some specific implementations details we can consider in practice:

1. File format: Better to use binary formats, faster to parse
2. Record deletion: We can append a special record that indicates
3. Crash recovery: During a crash, the thing that will take a hit would be the segment tables. You'll have to re-read every single record in the different segments in order to build up the hashtable again and store them in memory. This can take a while if you have lots of data. This can be avoided (or minimising the damage) by taking periodic snapshot of the hashmap and store them in persistent storage
4. Partial records: The crashing may also leave records partially written. We can add a special thing to each row to indicate whether it's done or not e.g. Adding a checksum

This design of merging and append only is quite good due to it being simple yet efficient. Both log appending and mergin are sequential operations which are very quick with most storage systems. Read operations are also pretty good by the simple addition of hashmaps.

However, it does have it's limitations including the fact that the hashtable has to fit in memory (which contains all keys for a particular segment) AND doing range queries not really efficient since those values may be at very far places in the disk (e.g. Different segments) and that you need to look up each keys in the hashmap

# SSTables and LSM-Trees

We will add a new requirement for the segment: records in the segment must be ordered by keys and that a key can only exist once in a segment. We also got rid of the hashtable per segment thing. This method is called SSTable / LSM-Trees.

SSTables have several advantages

1. Because we no longer have hash tables, the compaction and merge process is quite efficient. We simply go trough each segment taking the lowest ordered key from the first of each. Then just simply append to the new merged segment without having to create a hashmap for that segment. For keys that exists at multiple segments, we'll keep only the one from the latest segment. With this, we don't have memory limitations anymore (well, for the most part).

2. The hashtable you keep now can be sparse and a global one. Instead of keeping an entry for each key, you keep an entry for every Kb or so. You can then do a kind of binary search where say if you're looking for `k1` and you know that `k2` and `k3` exist as an entry, and that `k2<=k1<=k3`, then you can go to `k2`'s byte offset and do a linear search from there which is pretty quick
   - As it turns out, if your key and values have the same sizes, then you can avoid checking the memory entirely as you can just do binary search on a segment file.

   > The number of segments are again not expected to be big but I guess if they are, then a little hashmap could help to know the ranges of values that exists within a particular segment

3. You can also compress data before writing it to disk. This is because you need to read some extra couple of records anyways if you want to perform a read so might as well. This compression allows for quicker write and consumes less I/O resources (because less thing to write)
   > I guess that to read the compressed data, you'll need to decompress first which essentially mean you need to reprocess all of the record in the compressed block

# Constructing and Maintaining SSTables

The way this is approached is typically to store a structure in memory something we usually call the 'memtable'. The memtable is basically a log in memory that is capable of sorting incoming data. The memtable then from time to time will be compacted and written into disk

So, when a new write comes, you'll simply insert it first into the memtable. During a read, you'll read the memtable first and then most recent segment, and so on

There is one issue however: during a crash, the memtable would disappear which means the most recent writes won't survive. To deal with this, we don't just add recent writes to the memtable instead we also add it to a temporary log used to restore the memtable. This log is not sorted but it's fine since it won't be use for compaction and merging but rather only for recovering the memtable

# Size-Tiered and Level-Tiered Compaction

There are also different ways in which we can merge and compact the tables. 2 of the most common ones would be size and level tiered. Size tiered is similar to what we've described before but this time we merge toward an older, bigger segment instead of merging into a new segment.

Level tiered split segments into smaller SSTables based on their key values; each smaller SSTables hold keys within a particular range. Data are also separated into levels allowing for a more incremental process of compaction

# Making B-Trees Reliable

B-Trees are a bit trickier to handle than SSTables because they rely on referencing the storage location of a particular page. The fundamental writing operation is overwriting a particular page. It is assumed that this operation won't move the page's memory location so that previous reference to the page will still work.

This means, every write operation, you can think of it as waiting for the disk getting to the right position, and only then can you overwrite the data. This is in contrast with SSTables which can just keep on creating new entries in any write events

This aspect of B-Trees make writes a bit more involved since of the machine suddenly dies during a write, it could leave the tree to be in an inconsistent state. An example of this would be during a write operation in which we have to split a page into 2 (because it was full) and update the parent pages to refer to both pages. If say after the splitting the machine crashed, then you have an orphaned page (and data lost)

To handle this, we use something called a Write-Ahead Log (WAL). We basically describe the changes in this log first, and then make the actual changes. This way, if something crashed, the DB can refer to the WAL, and recover.

> The WAL I guess typically store things like "INSERT X into Table Y" instead of describing the operations to be done. So I guess if the index gets corrupted, we simply rebuild from the very beginning (or keep 2 copies of the B-trees and apply the changes to only one of them sequentially at a time)

There is also a concurrency concern: during such write operations discussed, it's completely possible for a read to happen. But as we've described, the tree could still be in an inconsistent state. To deal with this we use latches (a kind of lock) to stop concurrent operation

> I think latches are used to protect the region where the modification is happening only

# Copy-On-Write B-Trees

Instead of waiting for a disk seek to happen during a write, we can make it so that it's more copy-on-write; when a write happens, we make a copy of it at another location and then change the pointer of the parents once we're done. This way we don't need to keep a WAL

# Advantages Of LSM-Trees

In comparison, LMS-Trees are much more better suited for write operations than B-trees. One aspect we can consider is the amount of writes produced due to a single write operation to the database over the database's lifetime or also known as write amplification.

LSM-Trees, would typically have a lower write amplification than B-Trees (though this depends on the storage configuration) because B-trees may need to deal with splitting pages, writing to WAL, and connecting parent and children pages all coming from a single write. LSM-Trees on the other hand only need to write to a memory during the first write (and the temporary log) and handling repeated compactions and merging (again, this is why write amplification depends on the LSM-Trees storage configuration). Such a performance matters most when the underlying hardware is bottleneck-ed by it's write throughput or if it has a limited number of writes e.g. an SSD card.

> There is also overhead in writing an entire page just because you'd like to update certain bytes in the page because you need to fit in that new value

Because of this, and the fact that LSM-Trees only writes compact SSTables to the hard disk, which is important if the underlying storage is faster with sequential operations (which they usually are), LSM-trees usually have a higher bandwidth than B-Trees

Another aspect we can consider is fragmentation: LSM-trees would have a lower degree of this compared to B-Trees because when you split a page in B-Trees, no one can use that space unless you got a key that falls in that region. LSM-Trees on the other hand will simply put entries sequentially next to one another. The repeated compaction and merging process also helps with disk fragmentation since they produce a new segment and put them sequentially next to one another. Overall LSM-Trees have lower storage overheads than B-Trees

Something to note though, if you're using things like SSD cards, they usually convert the "random writes" to be sequential ones. But this doesn't mean it would invalidate the previous points: LSM-Trees still have lower writes and a more efficient and compact IO operations overall in comparison to B-Trees

# Disadvantages of LSM-Trees

Read operations however are not completely free from compaction operations. Sometimes you have to wait until the compaction operation finish before the read can proceed. In this regards, B-Trees are more predictable in terms of it's responses

The compaction operation also needs to be considered as the database gets bigger. The more data there are, the compaction operations could require more IO resources e.g. If you're using more threads to quicker compaction which is quite natural if you're using leveled compaction. The issue here is that you're now competing with incoming writes (writes requests) and background writes (compactions and merging) for a single hard disk.

This issue may also lead to the compaction rate being slower than incoming writes. The problem here is redundancy. If compaction is slow, we will have many segments lying around slowing down read operations since that one can only go through segments sequentially. Your database may also grow indefinitely and you'll run out of space very quickly. Typically though we don't throttle write speed rather we'd monitor this and take action accordingly

# Secondary Indexes

So far, we have seen how we can index our key-value pair data based on it's key and we assume that key is unique. This type of index is also called a primary key index in the relational database model

What we can also do is create secondary indexes. A secondary index is similar in purpose with a primary index except we no longer assume that the key is unique. A usual use case would be to find all records in a table that belongs to the same user.

Secondary indexes can be derived from the primary indexes we've seen. The way we can go around this is either make 1 key hold a list of values or add to the key the row identifier

# Storing Values Within The Index

There are usually 2 roads you can take here: Store the values of the row with the index or simply store a reference to the value.

The latter approach would have you stored the value inside another place called the heap file. The good thing about this approach is that you can technically have multiple indexes (primary or secondary) on the data since each index simply stores reference to the value with it's structure being defined by the key. We also avoid data duplication this way

Data update can also be quite efficient as long as the value is not bigger than the maximum size that was used for the older values. If it is, then you may need to update the reference for that key w.r.t any other indexes defined that on the table. Another would be to leave pointer at that point to the new location

This approach though may leave some extra overheads. As you insert a new data, you update not only the data but also all the indexes of that table which may be expensive. Another aspect is, in order to read, you may need to jump around the disk in order to get all of your data. This could be undesirable.

To avoid these problems, the former approach, also known as a clustered index, would store the values next to the key. It however is not as flexible as typically only 1 clustered index can be applied to a table i.e. only 1 indexing on the primary key. In the presence of a clustered index, any secondary index no longer stores reference to a heap file but rather to a primary key. The key is then used to search for the value (again) in the cluster index (at least this is how InnoDB works in mySQL)

We can draw a compromise here though through something called a covering index / included columns where we store some part of the data in the index. If the data is already there, we say the index covered the query

There are also some concerns if you use multiples of covering and clustering indexes due to data duplications. This add overhead in storage and transactional guarantees are much more difficult to implement.

# Multi-column Indexes

So far, the indexing techniques used are only valid for a single key i.e. a single column. Faced with queries that uses multiple columns, our index won't work as well.

The most common type of multi-column indexes are called concatenated index. The idea is to concatenate the keys from 2 different columns into 1. You can then use the index methods described so far. Say, if we want to find `lastname_firstname` combination, we can concatenate the 2 columns `lastname` and `firstname`. Just note that the order matters. If you specify `firstname_lastname`, then you can't do a search for the first name first and then last name. The opposite is fine however AND if you want to only search based on last name values. We're effectively converting it into a single column search again

A more generic kind of multi-column indexes are multi-dimensional indexes. An example would be for geo-spatial data. Imagine that you're using a map and you want to know some information in a bounded region (say a rectangle). Then you may have queries as such

```sql
SELECT *
FROM restaurants
WHERE
   latitude > 51.4946 AND latitude < 51.5079
   AND longitude > -0.1162 AND longitude < -0.1004;
```

> I guess by more general, they mean that things like ordering is no longer a concern and maybe now we can do range queries (?)

# References

1. Kleppmann, M. (2017). Designing data-intensive applications. Sebastopol, CA: O’Reilly Media.
2. https://github.com/facebook/rocksdb/wiki/Leveled-Compaction
3. https://dev.mysql.com/doc/refman/9.7/en/innodb-index-types.html

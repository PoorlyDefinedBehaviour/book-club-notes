## Log-structured storage engines and page-oriented storage engines

## Indexes

The book says:
```
If you want to search the same data in several different ways, you may
need several different indexes on different parts of the data.
```

### Hash table index

Uses a in memory hash table as an index. Dumps the hash table to disk.

#### Advantanges

- Simple.

#### Disadvantages

- Dataset must fit in memory.
- Range queries are not efficient because key are not sorted.
- It's hard to update the on-disk hash table because updating it would required random IO.

### Log-structured merge tree

Keep a sorted map in memory such as a BTree (known as memtable), the map is stored on disk when a memory threshold is surpassed. When stored on disk, 
the keys are sorted and the data in the file is called a Sorted String Table.[^sorted_string_table].  

As time passed, several segments will be stored on disk. Instead of maintaining an index that points to every key, create an index that points to the segment where the key could be located if it exists. Use a [bloom filter](https://poorlydefinedbehaviour.github.io/posts/bloom_filter/) to speed up queries.


#### Compacting sorted string tables with the k way merge algorithm

[Two-way merge algorithm from a leetcode challenge](https://github.com/PoorlyDefinedBehaviour/data-structures-and-algorithms/commit/6dcfc26396d18abb615d5d117050be735392decd)

#### LSM tree disadvantages

- Compaction can cause latency spikes. [Tigerbeetle example](https://twitter.com/jorandirkgreef/status/1764941403904180563)  
- Duplicated keys exist until compaction merges the files that contain the duplicated keys  

### Btree indexes

B-trees are the most popular type of index in relational databases. Keys are stored in a sorted tree. The tree is divided into fixed-size blocks and stored on disk. To update a key, the whole page is read from disk, modified and then rewritten to disk.  

A B-tree with n keys always has a depth of O(log n).  
A four-level tree of 4KB pages with a branching factor of 500 can store up to 256TB.  

### Datomic indexes

Datomic indexes contain the datoms rather than just a pointer to them (they are covering indexes).  

There are four indexes.  

EAVT: Entity / Attribute / Value / Transaction - Contains all datoms.  
Similar to a tuple in a relational database. Attributes that belong to an entity are close together.  

AEVT: Attribute / Entity / Value / Transaction - Contains all datoms.  
Similar to a tuple in a columnar database. The same attributes are close together.  

AVET: Attribute / Value / Entity / Transaction - Contains all datoms with attributes that are unique or index.  
Groups attributes and values together. Useful for range queries.  

VAET: Value / Attribute / Entity / Transaction - Contains all datoms with attributes that are of type ref.  
A reverse index.  

## Latches vs locks

Latches are held for the operation duration.  
Locks are held for the transaction duration.  

## Logs

A log is an append only sequence of records ordered by time.  

https://poorlydefinedbehaviour.github.io/posts/logs/

## Backward compatiblity and forward compatibility

The book says:
```
This means that old and new versions of the code, and old and new data formats,
may potentially all coexist in the system at the same time.
```

Reminders me of [how Amazon ensures rollback compatibility](https://aws.amazon.com/builders-library/ensuring-rollback-safety-during-deployments/).

## Formats for encoding data

The book talks about how the format of the data strucutures used to represent data in memory is different from the format used to store data on disk or to send it over the network.  

This reminders that it is possible to use the same format in memory and to send data over the network by reinterpreting the in memory data as bytes and sending it over the network as is, the receiver reinterprets the bytes it receives as the expected message format. This process saves the time it takes to serialize and deserialize data.

## Language-Specific formats

The book says:
```
In order to restore data in the same object types, the decoding process needs to
be able to instantiate arbitrary classes. This is frequently a source of security
problems [5]
```

This reminders me of [log4shell](https://en.wikipedia.org/wiki/Log4Shell) where a vulnerability in the java logging library log4j allowed for remote code execution because it was possible to load java classes and execute the code from a malicious URL.[^log4shell]

## Avro

It's common for people to use Avro in combination with Kafka. Each message sent to kafka contains a schema id, the message consumer reads the message, picks the correct schema from a registry using the schema id and then decodes the message.

## References

[I Heart Logs: Event Data, Stream Processing, and Data Integration](https://www.amazon.com/Heart-Logs-Stream-Processing-Integration/dp/1491909382)  
[SSTable and Log Structured Storage: LevelDB](https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/)  
[^sorted_string_table]: https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/
[^log4shell]: https://www.cynet.com/attack-techniques-hands-on/log4shell-explained/
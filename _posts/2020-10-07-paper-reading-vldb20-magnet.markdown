---
layout: post
title:  "[En][Paper Reading][VLDB ’20] Magnet: Push-based Shuffle Service for Lareg-scale Data Processing"
date:   2020-10-07 21:03:36 +0530
---

## Introduction
Magnet addresses a key shuffle scalability bottleneck by merging fragmented intermediate shuffle data into large blocks, and provides further improvements by co-locating merged blocks with reduce tasks.

### Shuffle

Shuffle, all-to-all communication between the map and reduce tasks, different frameworks take different approaches. Spark materializes shuffle data on local disks for better fault tolerance. Sort-based shuffle is more scalable: map task's output is sorted by the hashed value of the partition keys and materialized as a single file.

### Challenges@LinkedIn
1. All to all shuffle data transfer imposes reliability issues at scale
2. Disk I/O. Shuffle blocks are fetched individually in a random order, and these blocks are usually small (10s of KBs). Small random disk reads lead to increased latency and resource wastage.
3. The reduction of average block size as the shuffle data size grows also introduces scalability issue.

Magnet ooportunistically merges fragmented intermediate shuffle data into large blocks and co-locates them with the reduce tasks.

## Background and motivation

### Current Spark Shuffle Operation
The shuffle operation in Spark works as illustrated:
1. Each Spark executor registers with ESS that is located on the smae node. Note Spark ESS instances are external to the Spark executors and shared across pontentially many Spark applications.
2. Each map task produces two files at the end, one for the shuffle data, another to index the shuffle blocks in the former. The map task sorts all the transformed records according to the hashed value of the parition keys (may spill). All records belonging to the same shuffle partition are grouped together into a shuffle block. Shuffle index file records the block boundary offset.
3. Reduce tasks will query the Spark driver for locations of their input shuffle blocks. Each reduce task will establish connections to corresponding Spark ESS instances in order to fetch their input data. The Spark ESS, upon receiving such a request, skips to the corresponding block data in the shuffle data file leveraging the shuffle index file, read it from disk, and send it back to the reduce task.

A bunch of benefits:
1) Spark ESS can serve the shuffle block even when Spark executors are experiencing GC pause.
2) The shuffle blocks can still be served even if the Spark executor generating them is gone
3) The idle Spark executors can be freed up to save cluster compute resources.

### Inefficient Disk I/O during shuffle
The small random I/O reads lead to low disk(HDD) throughput and extended delays for fetching shuffle blocks. The average shuffle block size is only of 10s of KBs. From scattler plot, majority of the shuffle reduce stages with significant shuffle fetch delays are ones with small block size.

Tuning dilemma, changing the parameters to improve shuffle performance might negatively impact other aspects of the job. M mappers, R reduces, if amount of data processed per task stays the same, the number of shuffled blocks (M * R) grows quadratically, this leads to a reduction of the shuffle block size (D/ (M * R)) as D grows.

### Reliability of all to all connections
### Placement of Reduce Tasks
We can not saturate the network bandwidth, and if we can read locally and bypassing the shuffle service, this also helps reduce the number of RPC connections. Locality is still perferred.

## System Design
1. Push-based shuffle: mapper generated shuffle data is pushed to remote Magnet shuffle services to be merged per shuffle partition.
2. Best effort, therefore tolerating straglers/data skews.
3. Flexible deployment strategy

Once a map task produces the shuffle files, it divides the blocks in the shuffle data file into MB-sized chunks, each to be pushed to a remote Magnet shuffle service to be merged. With Magnet, the Spark driver determines a list of Magnet shuffle services the map tasks of a given shuffle should work with.

简而言之 mapper side的shuffle block被magnet shuffle service等分，然后分到的shuffle block又merge成几个chunk. This algorithm guarantees that each chunk only contains contiguous blocks inside the shuffle file up to a certain size, and blocks from different mappers belonging to the smae shuffle partition are pushed to the same Magnet shuffle service.

The Magnet shuffle service generates a merged shuffle file to append all received blocks. Keeps track of metadata, the Magnet shuffle service properly handles duplication, collision, and failure during the block merge operation.

Stragglers: best-effort, upper bound on waiting for merges; data skew: skip merging large shuffle blocks (integrates well with adaptive query execution)

### Compare with SOS
SOS needs much more memory (buffering blocks in memory before merging to improve disk efficiency). However Magnet's approach generates a lot of small random writes, but can achieve much higher throughput than small random reads, because of OS page cache and disk buffer.

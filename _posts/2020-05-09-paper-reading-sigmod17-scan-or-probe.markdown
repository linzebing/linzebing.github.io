---
layout: post
title:  "[En][Learning][Sigmod '17] Access Path Selection in Main-Memory Optimized
Data Systems: Should I Scan or Should I Probe?"
date:   2020-05-09 21:03:36 +0530
---

The advent of columnar data analytics engines fueled a series of optimizations on the scan operator. New designs include column-group storage, vectorized execution, shared scans, working directly over compressed data, and operating using SIMD and multi-core execution. This paper compares modern sequential scans and secondary scans.

## Introduction
A common case is that a query can be predicated on a column with a secondary index. In this case, a secondary index like B+-Tree may or may not work better than a full sequential scan.

In modern analytical systems, column or column-group storage allows a system to process only the attributes needed to answer a given query, avoiding unneeded reads in much the same way a secondary index does.

Second, in contrast to "tuple-at-a-time" processing, _vectorized execution_ passes a block of tuples to each operator. This increases efficiency by processing each block in a tight loop, reducing interpretation logic overhead.

Third, when the system is processing more than one query across the same attribute(s), _scans can be shared_. For each block of the relation, multiple queries can be processed resulting in advantageous disk, memory, or cache utilization compared to a "query-at-a-time" approach.

Fourth, modern analytical systems use _compression_ more effectively than row-stores by compressing individual columns independently and working directly over compressed data which reduces the cost of moving data through the memory hierarchy.

Fifth, holding each attribute contiguously in a _dense_ array allows tight `for` loop evaluation and lends itself well to SIMD processing.

This paper shows that secondary indexes are still useful for queries with low selectivity, and there should be a moving pivot (the higher the concurrency, the lower the selectivity threshold).

<p align="center">
  <img src="/assets/pictures/aps/APS.png">
</p>

## Access Path Selection
Scan sharing: queries can share the cost of data movement by moving data up the memory hierarchy once, when analyzing multiple queries.

1. _In a modern main-memory optimized analytic data system, there is a break-even point regarding the performance of a shared scan and a shared index scan; access path selection is needed to maximize performance in all scenarios._

2. _Unlike traditional query optimization decisions, choosing bewteen a sequential scan and an index scan in a modern system depends on concurrency in addition to selectivity._

3. _In hybrid systems supporting column-groups, secondary indexes are useful in more cases than plain column-stores because using a secondary index helps to avoid moving a larger amount of unnecessary data through the memory hierarchy._

4. _Although hardware characteristics change the point where a sequential scan is preferred over a secondary index scan, all systems require run time analysis to make the decision._

5. _Access path selection becomes more crucial for bigger data inputs as data movement becomes more expensive - and as a result every wrong decision has a larger cost._

## Conclusions
1. There are cases for analytical queries when a secondary index scan is preferred to a sequential scan, even when data is main-memory resident which removes the disk I/O bottleneck.
2. A fine-grained access path decision needs to take into account as a run-time parameter query concurrency, in addition to selectivity, hardware specs, system design, and data layout.
3. Data set size can be pivotal. For small data sets scanning the data outperforms secondary indexes in all cases. Index remains useful for larger data sets as full attribute scans become costly.
4. While the crossover between access methods is lower than in the past, it still corresponds to a growing result cardinality. For example, a query that selects 0.6% of 500 million integers, has 3 million qualifying tuples.
5. While sharing data access minimizes repetitive reads, it includes the overhead to distribute the results to their consumers. Result sharing is efficient up to a batch size, because of the bookkeeping and the result distributing overhead.
6. Access path selection is important for systems with columnar storage, however, it becomes even more important for wider tuples in hybrid stores.
7. As the cache and memory latency decreases, or the memory bandwidth decreases, secondary indexes become more beneficial. On the contrary, slower caches or memory, and faster memory buses benefit scan. In this way, future hardware generations that affect the balance between these hardware properties will also affect access path selection accordingly.

---
layout: post
title:  "[En][Paper Reading][CIDR' 05] MonetDB/X100: Hyper-Pipelining Query Execution"
date:   2020-05-16 21:03:36 +0530
---

## How does CPUs work
(1) One instruction can have dependency on a previous instruction.
(2) Branch prediction. A selection operator on data with a selectivity that is neither very high nor very low, are impossible to predict and can significantly slow down query execution.

_Super-scala_ CPUs offer the possibility to take multiple instructions into execution in parallel if they are independent. A super-scalar CPU can get to an IPC (Instructions Per Cycle) of > 1.

Hardware-predication allows executing both the THEN and ELSE blocks in parallel and discard one of the results as soon as the result of the condition becomes known. This is highly efficient and independent of selectivity as well.

Should mention the importantce of onchip caches to CPU throughput. Cache-conscious data structures are importatnt to operate at its maximum throughput. Also, query processing algorithms that restrict their random memory access patterns to regions that fit a CPU cache, strongly improve performance.

To summarize, instruction throughput of a processor can vary by orders of magnitude: 1. Cache hit-ratio 2. Branch predictions 3. Amount of independent instructions a compiler and the CPU can detect on average.

## Microbenchmark: TPC-H Query 1
### Relation Database Systems
MySQL only computes one addition per call, instead of any array of additions, the compiler cannot perform loop pipelining (as the instructions for one operation are highly dependent). Plus the cost of routine calls. This is representative in commercial relational DBMSs too.

### MonetDB/MIL
Column-at-a-time policy in MIL turns out to be a two-edged sword. The advantage is that the compiler is able to employ loop-pipelining such that these operators achieve high CPU efficiencies, emobodied by the SF = 0.001 results.

Queries that contain complex calculation expressions over many tuples will materialize an entire result column for each function in the expression.

## X100: A Vectorized Query Processor
Disk: geared towards efficient sequential data access
RAM: the same vertically partitionedd and even compressed disk data layout is used in RAM to save space and bandwidth
Cache: small vectrical chunks of cache-resident data items, called "vectors"
CPU: Allows compilers to produce efficient loop-pipelined code

### Query Language
Departed from the column-at-a-time MIL language so that relational operators can process vectors of multiple columns at the same time, allowing to use a vector produced by one expression as the input to another, while the data is in the CPU cache.

Execution proceeds using Volcano-like pipelining, on the granularity of a vector. Column-wise vector layout allows for aggressive loop pipelining. Compound primitives only need to load/store at edges of the expression graph: results from one calculation are passed via a CPU register to the next calculation.

Vertical storage has the disadvantage of increasing update cost. In MonetDB, updates go to delta structures instead. An advantage of vertical storage is that queries that access many tuples but not all columns save bandwidth (both RAM and I/O). We can further use light-weight compression.

MonetDB/X100 also supports simple "summary" indices, with running max/min values, such that we can quickly derive bounds for range predicates.

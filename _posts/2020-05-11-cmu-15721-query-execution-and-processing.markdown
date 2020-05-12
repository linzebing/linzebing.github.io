---
layout: post
title:  "[En][Learning][CMU 15721] Query Execution & Processing"
date:   2020-05-11 21:03:36 +0530
---

Optimization goals:
1. Reduce Instruction Count
2. Reduce Cycles per Instruction
3. Parallelize Execution

CPUs organize instructions into pipeline stages.
Super-scalar CPUs support multiple pipelines.

- Problem #1: Dependencies
- Problem #2: Branch Prediction: CPUs will speculatively execute branches. This potential hides the long stalls between dependent instructions.

In selection scans, branching only outperforms branchless when selectivity is really high or really low.

<p align="center">
  <img src="/assets/pictures/15721-execution/scan.png">
</p>

<p align="center">
  <img src="/assets/pictures/15721-execution/scan2.png">
</p>

## Processing Model
### Iterator Model
Each query plan operator implements a `next` function.
- On each invocation, the operator returns either a single tuple or a null marker if there ae no more tuples.
- The operator implements a loop that calls next on its children to retrieve their tuples and then process them.

<p align="center">
  <img src="/assets/pictures/15721-execution/iterator.png">
</p>

This is used in almost every DBMS. Allows for tuple pipelining. Some operators must block until their children emit all of their tuples.

Output control works easily wit this.

### Materialization Model
Each operator processes its input all at once and then emits its output all at once.
- The operator "materializes" its output as a single result.
- The DBMS can push down hints to avoid scanning too many tuples.
- Can send either a materialized row or a single column.

1. Lower execution/coordination overhead.
2. Fewer function calls.

Not good for OLAP queries with large immediate results.

### Vectorized/Batch Model
Like iterator model, but each operator emits a batch of tuples instead of a single tuple. The operator's internal loop processes multiple tuples at a time.

Ideal for OLAP queries because it greatly reduces the number of invocations per operator. Allows for operators to use SIMD instructions.

## Plan Processing Direction
1. Top-to-Bottom
- Start with the root and "pull" data up from its children.
- Tuples are always passed with function calls.

2. Bottom-to-Top
- Start with leaf nodes and push data to their parents.
- Allows for tighter control of caches/registers in pipelines.

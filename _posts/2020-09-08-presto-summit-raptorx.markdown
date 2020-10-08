---
layout: post
title:  "[En][Learning][Presto Summit] Presto: SQL on Everything"
date:   2020-09-08 21:03:36 +0530
---

Cache working dataset closer to the compute nodes. Data cache on local SSD.

Soft affinity based scheduling. Best efforts to assign the same split to the same worker. (may consider consistent hashing for dynamic cases in the future)

Naive solution: copying files from remote storage on local storage, merging files in the local storage to keep file count low. Pluggable cache policies, async cache writes, performance metrics

New solution: Alluxio. Async caching, caching not a single point of failure, better cache hits.

1. Cache files in fixed-sized segments (pages)
2. Store pages off-heap to reduce GC pressure
3. Highly-concurrent & thread-safe: light-weight & fine-graind locking

Future work: semantics-aware caching; in-memory cache store;

https://prestodb.io/blog/2020/06/16/alluxio-datacaching

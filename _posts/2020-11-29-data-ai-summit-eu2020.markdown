---
layout: post
title:  "[En][Learning] Spark Summit (Europe + SF) 2020"
date:   2020-11-29 21:03:36 +0530
---

Local Sort: Make Parquet Row Groups more distinguishble via sorting every file (how about push down)
Prewhere: read data of filter columns in batch first and skip other columns if unmatched

Dynamic Partition Pruning
Adaptive Query Execution: Coalescing Shuffle Partitions, Switching Join Strategies, Optimizing Skew Joins

SIMD. Internal Row -> Columnar. ColumnarPlan/ColumnarWSCG/ColumnarAQE -> LLVM/SIMD Kernel

[The Spark File Format Ecosystem](https://databricks.com/session_na20/the-apache-spark-file-format-ecosystem)

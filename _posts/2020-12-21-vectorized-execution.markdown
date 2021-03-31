---
layout: post
title:  "[En][Learning][CMU 15721] Vectorized Execution and Vectorization vs. Codegen"
date:   2020-12-21 21:03:36 +0530
---

SIMD advantages: Significant performance gains and resource utilization if an algorithm can be vectorized
SIMD disadvantages: Implementing an algorithm using SIMD is mostly a manual progress; SIMD may have restrictions on data alignment; Gathering data into SIMD registers and scattering it to the correct locations is tricky and/or inefficient

Three choices:
- Automatic Vectorization
- Compiler Hints
- Explicit Vectorization

Selective Load, Selective Store, Selective Gather, Selective Scatter

Data-centric (aka. codegen) is better for "calculation-heavy" queries with few cache misses. Fusion inhibits some optimizations (can't SIMD, can't prefetch):
- Unable to look ahead in tuple stream
- Unable to overlap computation and memory access

Vectorization is slightly better at hiding cache miss latencies. Because vectorization uses simple loops, making it easier to overlap computation and memory access. Prefetching/SIMD can also help vectorization.

Fetch -> Decode -> Execute -> Access -> Write-Back

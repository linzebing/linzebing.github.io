---
layout: post
title:  "[En][Learning][CMU 15721] Database Compression & Storage Layout"
date:   2020-12-14 21:03:36 +0530
---

I/O is the main bottlenecks if the DBMS has to fetch data from disk. Key trade-off is __speed__ vs. __compression ratio__

#### Database Compression
1. Must produce fixed-length values (exception: var-length data stored in separate pool)
2. Postpone decompression for as long as possible during query execution -> __late materialization__
3. Lossless

Zone Maps: Pre-compute columnar aggregations per block that allow the DBMS to check whether queries need to access it

__Run-length encoding__: the value of the attribute, the start position in the column segment, the # of elements in the run.
Columns better sorted intelligently to maximize compression opportunities.

__Delta encoding__: recording the difference between values that follow each other in the same column
- Store base value in-line or a separate look-up tablse
- combine with RLE to get even better compression ratios

__Incremental encoding__: avoids duplicating common prefixes/suffixes
__dictionary encoding__:
order-preserving encoding: encoded values need to support sorting in the same order as original values. One array of variable length strings and another array with pointers that maps to string offsets.

One can think of an in-memory database as just a large array of bytes:
- The schema tells the DBMS how to convert the bytes into the appropriate type
- Each tuple is prefixed with a header that constrains its meta-data
__NULL Data Types__: most common one is a bitmap in the tuple header that specifies what attributes are null
__Word-Alignment__: padding/reordering

#### N-Array Storage model
__Advantage__:
- Fast inserts, updates and deletes
- Good for queries that need the entire tuple
- Can use index-oriented physical storage
__Disadvantage__:
- Not good for scanning large portions of the table and/or a subset of the attributes

#### Decomposition Storage Model
__Advantage__:
- Reduces the amount wasted work because the DBMS only reads the data that it needs
- Better compression
__Disadvantage__:
- Slow for point queries, inserts, updates, and deletes because of tuple splitting/stitching

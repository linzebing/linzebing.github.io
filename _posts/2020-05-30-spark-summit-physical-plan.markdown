---
layout: post
title:  "[En][Learning][Spark Summit EU] Physical Plans in Spark SQL"
date:   2020-05-30 21:03:36 +0530
---

[Talk1](https://www.youtube.com/watch?v=99fYi2mopbs) [Talk2](https://www.youtube.com/watch?v=9EIzhRKpiM8) [Slides](https://www.slideshare.net/databricks/physical-plans-in-spark-sql)

## Logical Planning
Created, analyzed and optimized.
- Tree representation of the query
- An abstraction that carries information about what is supposed to happen
- Doesn't contain precise information on how it happens
- Composed of relational operators and expressions

## Physical Planning
A bridge bewteen logical plan and rdd. It's a tree, contains specific description of how things should happen (specific choice of algorithms).
- ReuseExchange allows to reduce I/O and network cost
- EnsureRequirements adds Exchange

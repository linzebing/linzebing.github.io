---
layout: post
title:  "[En][Learning][CMU 15721] Query Compilation & Code Generation"
date:   2020-05-31 21:03:36 +0530
---

[Paper](https://15721.courses.cs.cmu.edu/spring2020/papers/14-compilation/p539-neumann.pdf), [Lecture](https://www.youtube.com/watch?v=DvhqgmRQuAk&list=PLSE8ODhjZXjasmrEd2_Yi1deeE360zv5O&index=14)

## Background
After switching to an in-memory DBMS, the only way to increase throughput is to reduce the number of instructions executed. One way to achieve such a reudction in instructions is through code specialization, that any CPU intensive entity of database can be natively compiled if they have a similar execution pattern on different inputs:
- Access Methods
- Stored Procedures
- Operator Execution
- Predicate Evaludation
- Logging Operations

## Benefits
Attribute types are known a priori: data access function calls can be converted to inline pointer casting.
Predicates are known a priori: they can be evaludated using primitive data comparisons.
No function calls in loops: allows the compiler to efficiently distribute data to registers and increase cache reuse.

## Code Generation
1. Transpilation: write code that converts a relational query plan into imperative language *source code* and then run it through a conventional compiler to generate native code.
2. JIT compilation: generate an *intermediate representation* (IR) of the query that the DBMS then compiles into native code.

![](/assets/pictures/codegen/template.png)

The generated query code can invoke any other function in the DBMS.

## Compilation Cost
LLVM's compilation time grows super-linearly relative to the query size: # of joins, # of predicates, # of aggregations. A major problem with OLAP workloads.

So we have adaptive execution:

![](/assets/pictures/codegen/hyper.png)

## Memsql
A query plan is converted into an imperative plan expressed in a high-level imperative DSL.
- MemSQL Programming Language (MPL)
- Think of this as a C++ dialect.

The DSL then gets converted into a second language of opcodes.
- MemSQL Bit Code (MBC)
- Think of this as JVM byte code.

Finally the DBMS compiles the opcodes into LLVM IR and then to native code.

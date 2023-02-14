---
Author: Hoa Nguyen
Editor: Hoa Nguyen
Title: DINO CPU Assignment 4
---

DINO CPU Assignment 4: The Why of Caching.

Originally from ECS 154B Lab 4, Winter 2023.

# Table of Contents
* [Table of Contents](#table-of-contents)
* [Introduction](#introduction)
* [Learning Objectives](#learning-objectives)
* [The Computer System](#the-computer-system)
  * [The Non Combinational Pipelined CPU Core](#the-non-combinational-pipelined-cpu-core)
  * [The Memory Cache System](#the-memory-cache-system)
    * [The Least-Recently Used (LRU) Replacement Policy](#the-least-recently-used-lru-replacement-policy)
  * [The Memory System](#the-memory-system)
* [The Benchmarks](#the-benchmarks)
* [Part I: Implementing the Hazard Detection Unit for Non Combinational Pipelined CPU](#part-i-implementing-the-hazard-detection-unit-for-non-combinational-pipelined-cpu)
* [Part II: Performance Evaluation](#part-ii-performance-evaluation)
* [Part III: Performance Analysis](#part-iii-performance-analysis)
* [Logistics](#logistics)
* [Conclusion](#conclusion)
* [Extra Credits](#extra-credits)

# Introduction

Caching is among the greatest ideas in computing.
In fact, the concept is so great that it appears in many fields of computer science.
Examples can be found in from almost all hardware architectures, such as the use of translation lookup buffers (TLBs) or the use of memory cache system, to various software designs, such as DNS caching or web caching.

In essence, the idea of caching is that, when the cost of acquiring data is high and the cost of duplicating the data is low, the acquired data can be saved in a cache, which incurs a lower data acquiring cost, so that, the next time the piece of data is requested, the requestor can retrieve the data from the cache rather than going through the whole computation again.
If the piece of data is frequently requested, the cache would bring down the average cost of acquiring that piece of data.

However, caching does not inherently improve the performance of a system as an infinite cache system does not exist.
Due to physical and designing constraints, such as area, power, and latency, designing a performant memory cache in a CPU core imposes a huge challenge to the designers.
In this assignment, we will investigate the performance of a computer system with a pipelined CPU core, a memory system, and various cache design decisions.

The assignment is designed as follows: we will introduce each of the components of the computer system that we are going to investigate, then we will introduce the benchmarks that will be subsequently used for performance evaluation and performance analysis.
In terms of implementation, most of the system is implemented.
However, we will ask you to complete the hazard detection unit for the new pipelined CPU.

# Learning Objectives

# Glossary

- Core/CPU terminology: Technically, the cache system resides on a CPU. We
will use the term "core" to refer to the pipeline, and the term "CPU" to
refer to the pipeline and the cache system.

- Memory Latency: The time between when a memory device receives a request
and when it completes the request.

- Memory Hierarchy: A series of memory devices that, the lower level a memory
device is, the higher memory latency it has.

- Memory System: All memory devices including the cache system and RAM.

- Memory Transaction: A memory device can send a *memory request* to its lower
next memory device, which will send back the corresponding *memory response*
when the request is complete. The DINOCPU memory system returns 8 bytes of
requested data for each memory read request. Note that, for all memory
devices in DINOCPU, the devices won't accept a new memory request until
the currect request is finished. There's also no mechanism for cancelling
a memory request.

- Dirty Cache Entry: A cache entry is written to at some point that has yet to
be written back to the lower level memory.

- Cache Entry Eviction: When there is a cache miss, a cache system will have to
send a memory request to get the data to the cache. Upon receiving the
response, if all cache entry are occupied, one of the cache *current* entry
will be evicted, i.e., this entry will be sent to the next lower
level memory if the next lower level memory does not have the most recent
value of the dirty entry. The evicted entry will be replaced by the new data.

- LRU replacement policy: When all cache entry are occupied, the cache entry
with that was least recently accessed.

# The Computer System

## The Core

We will use the same pipeline that we built in the assignment 3.
However, the memory interface is slightly tweaked to support memory devices
of which the latency of a memory request is unknown to the core [1].
Note that both instructions and data come from memory devices.
As a result, extra care should be taken to ensure correct instructions enter
the pipeline, as well as the correct data are received.
For this assignment, you will update the hazard unit to make sure that the
the core receives correct instructions/data from the cache system.

## The Cache System

We will use one-level cache system for this assignment.
We call this L1 Cache.

L1 Cache: The L1 Cache consists of two components: an L1 Instruction Cache
(L1I) and L1 Data Cache (L1D).
The Instruction Cache is optimized for read-only accesses.
The Data Cache supports both read and write operations.
Each of the L1I and L1D is 4-way associtive cache of size 256 bytes.
The cache block size is 8 bytes.
Each cache uses the LRU replacement policy.

### DINOCPU Cache Details

Each cache component consists of one CacheTable and one CacheMemory.

A CacheMemory is an array of registers containing all cache blocks.
Note that CacheMmoery only has the data, it doesn't have metadata.

A CacheTable is a table of CacheEntry's, where each CacheEntry contains
the metadata uniquely identifies a cache block, as well as metadata indicating
the status of the cache block, and a pointer to the cache block data in the
CacheMemory.

A CacheEntry is stuctured as followed,

```
class CacheEntry(val numEntryIndexingBits:Int, val numTagBits: Int) extends Bundle {
  val valid  = Bool()                       // Whether this entry contains valid data.
  val dirty  = Bool()                       // Whether this entry is written to by the core. Default to False, set to True upon a write request.
  val tag    = UInt(numTagBits.W)           // Contains the tag bits.
  val memIdx = UInt(numEntryIndexingBits.W) // Where is it in the cache memory.
  val age    = UInt(32.W)                   // When was the most recent access to this cache block.
} 
```

For example,

```
idx 27 -> CacheEntry(valid ->  1, dirty ->  1, tag -> 211, memIdx -> 27, age -> 17614)
```
means,
- `valid ->  1`: The entry contains valid data.
- `dirty ->  1`: The entry was written to.
- `tag -> 211`: The tag bits are 211 in decimal (or 0xd3 in hexadecimal).
- `memIdx -> 27`: The 27th entry of CacheMemory has data of this cache block.
- `age -> 17614`: The most recent access to this cache block was in cycle 17614.


## The RAM Device

The RAM Device is the lowest level memory device in the DINOCPU system.
All accesses to the RAM device incurs a 30 CPU cycles latency.

# The Benchmarks

For this assignment, we evaluate the effectiveness of the cache system using
a benchmark called `stream`, which is inspired by
[the popular STREAM benchmark](https://www.cs.virginia.edu/stream/).

The workloads are named as followed,

```
stream-<n>-stride-<stride>[-noverify].riscv
```

where,
- each element is of size 16 bits.
- `n`: number of memory accesses *per* array *per* iteration.
- `stride`: the distance between two consecutive elements.
- `-noverify`: if the workload verifies the result array.

For this assignment, we will use `stream-64-stride-1.riscv` and
`stream-64-stride-4.riscv` for verifying the correctness of the
pipelined implentation. The `stream-64-stride-1-noverify.riscv`
and `stream-64-stride-4-noverify.riscv` are used for performance
evaluation.

# Part I: Implementing the Hazard Detection Unit for Non Combinational Pipelined CPU

# Part II: Performance Evaluation

For this assignment, we will consider 4 systems,
- System 1: Non Combinational Pipelined CPU + No Cache
- System 2: Non Combinational Pipelined CPU + Instruction Cache (No Data Cache)
- System 3: Non Combinational Pipelined CPU + Data Cache (No Instruction Cache)
- System 4: Non Combinational Pipelined CPU + Instruction Cache + Data Cache

**Notes:** You don't have to report raw numbers or how the calculation was done
for question 2 and question 3.
If you are unsure about the correctness of the graph, you can show the formula
that you used to generate the data for the graph.
However, the graphs must have the X-axis and Y-axis with data labels and units.
An example graph will be discussed in during one of the discussion sessions.

## Question 1 (5 points)

For this question, you will be reporting the number of dynamic instructions of
the `stream-64-stride-1-noverify.riscv` and the
`stream-64-stride-4-noverify.riscv`.

## Question 2 (10 points)

For this question, you will be creating a graph presenting the CPI of the Non
Combinational CPU in 4 systems described above with the
`stream-64-stride-1-noverify.riscv` benchmark and the
`stream-64-stride-4-noverify.riscv` benchmark.
The X-axis should be grouped by systems.

## Question 3 (10 points)

- Effective Bandwidth

## Question 4 (10 points)

- L1 data / inst ratio system 4

# Part III: Performance Analysis

## Question 5 (15 points)

Between data cache and instruction cache, do you think which cache has more
impact on performance? Explain why using the data from part II.

## Question 6 (20 points)

From the data from Part II, you should see that `stream-64-stride-1-noverify.riscv`

# Logistics

# Conclusion

# Extra Credits

# Footnotes

[1] This is not because the latency of a memory device is hidden from the CPU.
This is a complication caused by internal activities of a memory device that
are abstracted away by the CPU/Memory interface.
This problem is not apparent in a 1-level cache system. However, it might be
a problem in two or more level cache system.
For example, if we have a two-level cache sytem, when there is a miss in both
L1 and L2.
When the L1 receives the data, it might evict an entry from L1 to L2.
L1 sends the response back to the CPU as soon as L2 receives the evict request.
Upon the next data memory access from the CPU, and if there is an L1 miss, L2
might still be busy with the previous evict request.
As a result, there's no single value describe each of a cache miss latency and
a cache hit latency as those values are dependent on the state of the
cache/memory devices, which are unknown until the runtime, and are varied from
a workload to another workload.

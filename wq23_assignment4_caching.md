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

- Memory Latency: The time between when a memory device receives a request
and when it completes the request.

- Memory Hierarchy: A series of memory devices that, the lower level a memory
device is, the higher memory latency it has.

- Memory Transaction: A memory device can send a *memory request* to its lower
next memory device, which will send back the corresponding *memory response*
when the request is complete.

# The Computer System

- CPU: We will use the same pipelined CPU that we built in the assignment 3.
However, the memory interface is slightly tweaked to support memory devices
of which the latency of a memory request is unknown to the CPU [1]. Note that both
instructions and data come from memory devices. As a result, extra care should
be taken to ensure correct instructions enter the pipelined CPU, as well as
the correct data is received.

- L1 Cache:


# The Benchmarks

# Part I: Implementing the Hazard Detection Unit for Non Combinational Pipelined CPU

# Part II: Performance Evaluation

# Part III: Performance Analysis

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

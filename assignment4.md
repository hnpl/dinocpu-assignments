# Table of Contents

# Introduction

## Updating the DINO CPU Code
## How This Assignment is Written
## I/O Constraint
## Goals

# Dual-issue Pipelined CPU Design
## Testing Dual-issue Pipelined CPU
## Debugging Dual-issue Pipelined CPU

# Part I: Implementing Forwarding and Hazard Detection Unit for Dual-issue Pipelined CPU
In this part, you will complete the forwarding unit and the hazard detection unit for the dual-issue pipelined CPU.
- Forwarding unit: `src/main/scala/components/dual/forwarding.scala`.
- Hazard detection unit: `src/main/scala/components/dual/hazard.scala`.

**Hints:**
- The logics for forwarding and hazard detection for dual-issue pipelined CPU share the same underlying principles as their counterparts in the original pipelined CPU.
- It is helpful to layout all instructions in the pipeline from least recently issued to most recently issued. This matters as there might be multiple of options for forwarding to an instruction at the EX stage, but only one option is valid.
- For the forwarding unit, note that only one instruction will be issued if the two fetched instructions have data dependencies (meaning, the earlier instruction writes to a register that the next instruction will use as a source register).
- For the hazard detection unit, note that if the instruction at PC is a branch/jump/load/store, only the instruction at PC will be issued in this cycle (meaning, the instruction at PC+4 will be issued in the next cycle).

## Testing the Dual-issue Pipelined CPU
TODO

### Full Application Traces
TODO

## Submissions
TODO

------------------------------------------------------------------------------------------------------------
# Part II: Performance Evaluation
In this part, you will collect the data for the next steps.

Now that we completed three different CPU designs (single-cycle, pipelined, pipelined-dual-issue), we can evaluate and compare their performances on different benchmarks.

## Loop-unrolling Technique
TODO

## Collecting Data

## Questions:
**Question 1:** Make a graph presenting the IPC of 
**Question 2:** Make a graph presenting the performance of 

------------------------------------------------------------------------------------------------------------
# Part III: Performance Analysis

------------------------------------------------------------------------------------------------------------
# Part IV:
------------------------------------------------------------------------------------------------------------

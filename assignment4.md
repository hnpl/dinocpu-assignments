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

## Hints
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

## Question 1
Make a graph presenting the IPC of all combinations of CPU designs with all full applications *without* loop-unrollings and *with* loop-unrollings.

## Question 2
Make a graph presenting the performance (i.e., the time it takes for a CPU design to run application) of of all combinations of CPU designs with all full applications *without* loop-unrollings and *with* loop-unrollings.

## Question 3
Make a table presenting the speedup of the new CPU design (pipelined-dual-issue) compared to the old CPU design (pipelined).

## Hints
- For each of Question 1 and Question 2, there should be 36 data points to present, e.g., for the `multiply` workload,
  - `multiply.riscv` + `single-cycle`
  - `multiply.riscv` + `pipelined`
  - `multiply.riscv` + `pipelined-dual-issue`
  - `multiply-loops-unrolled.riscv` + `single-cycle`
  - `multiply-loops-unrolled.riscv` + `pipelined`
  - `multiply-loops-unrolled.riscv` + `pipelined-dual-issue`
- The expectations for the graphs will be discussed in one of discussion sessions.
- For Question 3, the expected table looks like the following table,

|              Benchmarks | pipelined (in some unit) | pipelined-dual-issue (in some unit) | speedup |
|-------------------------|--------------------------|-------------------------------------|---------|
|                multiply |                          |                                     |         |
|  multiply-loops-unrolls |                          |                                     |         |
|                  median |                          |                                     |         |
|    median-loops-unrolls |                          |                                     |         |
|                   qsort |                          |                                     |         |
|     qsort-loops-unrolls |                          |                                     |         |
|                   rsort |                          |                                     |         |
|     rsort-loops-unrolls |                          |                                     |         |
|                  towers |                          |                                     |         |
|    towers-loops-unrolls |                          |                                     |         |
|                   vvadd |                          |                                     |         |
|     vvadd-loops-unrolls |                          |                                     |         |


------------------------------------------------------------------------------------------------------------
# Part III: Performance Analysis

------------------------------------------------------------------------------------------------------------
# Part IV:
------------------------------------------------------------------------------------------------------------

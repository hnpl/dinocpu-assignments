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
```

```

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
For this question, you will be creating a graph presenting the IPC of all combinations of CPU designs with all full applications *without* loop-unrollings and *with* loop-unrollings.

## Question 2
For this question, you will be creating a graph presenting the *performance* (i.e., the time it takes for a CPU design to run application) of of all combinations of CPU designs with all full applications *without* loop-unrollings and *with* loop-unrollings.

## Question 3
For this question, you will be creating a table presenting the speedups of the new CPU design (pipelined-dual-issue) compared to the old CPU design (pipelined) for each workload, both normal and unrolled.

## Hints
- For each of Question 1 and Question 2, there should be 36 data points to present, e.g., for the `multiply` workload,
  - `multiply.riscv` + `single-cycle`
  - `multiply.riscv` + `pipelined`
  - `multiply.riscv` + `pipelined-dual-issue`
  - `multiply-loops-unrolled.riscv` + `single-cycle`
  - `multiply-loops-unrolled.riscv` + `pipelined`
  - `multiply-loops-unrolled.riscv` + `pipelined-dual-issue`
- You do **not** need to include raw data for Question 1 and Question 2, or how the calculation is done.
- The expectations for the graphs will be discussed in one of discussion sessions.
- For Question 3, you do **not** need to show how the calculation is done.
- Also for Question 3, the expected table looks like the following table,

|              Benchmarks |   pipelined (in seconds) |   pipelined-dual-issue (in seconds) | speedup |
|-------------------------|--------------------------|-------------------------------------|---------|
|                multiply |                          |                                     |         |
| multiply-loops-unrolled |                          |                                     |         |
|                  median |                          |                                     |         |
|   median-loops-unrolled |                          |                                     |         |
|                   qsort |                          |                                     |         |
|    qsort-loops-unrolled |                          |                                     |         |
|                   rsort |                          |                                     |         |
|    rsort-loops-unrolled |                          |                                     |         |
|                  towers |                          |                                     |         |
|   towers-loops-unrolled |                          |                                     |         |
|                   vvadd |                          |                                     |         |
|    vvadd-loops-unrolled |                          |                                     |         |

------------------------------------------------------------------------------------------------------------
# Part III: Performance Analysis
In this part, you will analyze some interesting results!

## Question 4
In Part II, you should see that shifting from the original pipelined CPU to the dual-issue pipelined CPU results in a speedup (in terms of *performance*) for the `rsort-loops-unrolled` workload, and a slowdown (in terms of *performance*) for the `qsort-loops-unrolled` workload.
Explain the speedup and the slowdown.

## Question 5
In Part II, you should see that shifting from the original workload to the loops unrolled workload for dual-issue pipelined CPU leads to a speed up (either in terms of cycles or performance) for the `rsort` workloads, and a slowdown (either in terms of cycles or performance) for the `qsort` workloads.
Explain the speedup and the slowdown.

## Hints
- For every workload, there would be some functions or loops that consume most of the cycles. It is useful to find the functions or loops (either in the C code or the assembly code) and do analysis on how they being run by the CPU. This is called *program profiling*, which helps part of a program producing most of time complexity.
- You do not have to analyze all characteristics of the workloads (it would be beneficial to find the aspects of the binary that affect the CPU performance, but it is not essential to find all of them for this assignment). An idea or two on what caused each of the speedups and slowdowns would be sufficient for full credits. Make sure that the ideas are well-explained.

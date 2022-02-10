---
Author: Hoa Nguyen, Jason Lowe-Power
Editor: Hoa Nguyen
Title: DINO CPU Assignment 4
---

DINO CPU Assignment 4: Dual-issue Pipelined CPU: Performance Evaluation and Analysis

Originally from ECS 154B Lab 4, Winter 2022.

Due on TODO: See [Submission]?? for details

# Table of Contents

* [Introduction](#introduction)
    * [Updating the DINO CPU code](#updating-the-dino-cpu-code)
    * [How This Assignment is Written](#how-this-assignment-is-written)
    * [I/O Constraint](#io-constraint)
    * [Goals](#goals)
    * [Glossary](#glossary)
* [Dual-issue Pipelined CPU Design](#dual-issue-pipelined-cpu-design)
* [Part I: Implementing Forwarding and Hazard Detection Unit for Dual-issue Pipelined CPU](#part-i-implementing-forwarding-and-hazard-detection-unit-for-dual-issue-pipelined-cpu)
    * [Hints](#hints)
    * [Testing the Dual-issue Pipelined CPU](#testing-the-dual-issue-pipelined-cpu)
    * [Full Application Traces](#full-application-traces)
    * [Submissions](#submissions)
* [Part II: Performance Evaluation](#part-ii-performance-evaluation)
    * [The Trade-offs of Making Increasingly Complex CPU Designs](#the-trade-offs-of-making-increasingly-complex-cpu-designs)
    * [Loop-unrolling](#loop-unrolling)
    * [Collecting Data](#collecting-data)
    * [Question 1](#question-1)
    * [Question 2](#question-2)
    * [Question 3](#question-3)
    * [Hints](#hints-1)
* [Part III: Performance Analysis](#part-iii-performance-analysis)
    * [Question 4](#question-4)
    * [Question 5](#question-5)
    * [Hints](#hints-2)
* [Conclusion](#conclusion)
* [Logistics](#logistics)
    * [Grading](#grading)
    * [Submission](#submission)
        * [Code Portion](#code-portion)
        * [Written Portion](#written-portion)
        * [Academic misconduct reminder](#academic-misconduct-reminder)
* [Hints](#hints-3)


# Introduction
![Cute Dino]({{'img/dinocpu/dino-128.png' | relative_url}}) ![Cute Dino]({{'img/dinocpu/dino-128.png' | relative_url}})

In previous assignments, you implemented a full single cycle RISC-V CPU and a full 5-stage pipelined RISC-V CPU.
Jumping from the single cycle CPU design to the pipelined CPU design was a big step toward exploiting instruction-level parallelism (ILP).

In this assignment, you will explore another big idea of exploiting ILP: multiple-issue.
Essentially, multiple-issue means the CPU will allow multiple instructions to enter the pipeline at the same time.
This assignment will focus on dual-issue: upto two instructions can be issued in a cycle.
You will complete the provided dual-issue pipelined RISC-V CPU by implementing its hazard dection unit and its forwarding unit, and more importantly, you will evaluate and analyze the performance of different CPU designs.
You will also explore how a compiler can make a trade-off to optimize the performance of software on different hardware.


## Updating the DINO CPU Code
Before the assignment 3 late due date, the code template for assignment 4 will be on the `lab4` branch.
The `main` branch will be merged to the `lab4` branch after that.

This step is only necessary if you want to start to work on this assignment before the assignment 3 late due date (2/13/2022 11:59PM PST).
```
git pull
git stash
git rebase origin/lab4
git stash pop
```
If you work on this assignment after the assignment 3 late due date,
```
git pull
```

Note that you do not need to reuse the code from other assignments for this assignment.

## How This Assignment is Written
In the first part, you will complete the dual-issue CPU design that supports all RISC-V 64-bit integer instructions.
In the next part, you will get the performance data of different CPU designs, which will be used in the followed part, in which you will reason about the performance data.

## I/O Constraint
We are making one major constraint on how you are implementing your CPU.
**You may not modify the I/O for any module.**
This is the same constraint that you had in other assignments.
We will run all available tests on the dual-issue CPU design.
Therefore, you must **keep the exact same I/O**.
You will get errors on Gradescope (and thus no credit) if you modify the I/O.

## Goals
- Learn to resolve the data hazards in a dual-issue CPU using forwarding/stalling/flushing.
- Learn to compare the performance of different CPU designs.
- Learn to analyze how a workload perform differently different CPU designs.
- Learn to analyze how software optimizations might affect a CPU performance.

## Glossary
|Terms| Definitions |
|----|-|
|issue| A CPU issuing an instruction means that the CPU decides that that instruction will enter the pipeline. Issuing happens in the IF stage; however, issuing is different from fetching. At each cycle, by inputting a PC, the imem (instruction memory) will fetch 2 instructions at address of `(PC // 8) * 8`, but the issue unit might not necessary issue both instructions. |
|commit| A CPU committing an instruction means that that instruction goes through all 5 stages of the pipeline. |
|IPC| Number of committed instructions per cycle. |
|performance| The amount of time it takes for a CPU to run application. |
|loop unrolling| See Loop-unrolling TODO section. |

# Dual-issue Pipelined CPU Design


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
Lab4 / testOnly dinocpu.SmallTestsTesterLab4
Lab4 / testOnly dinocpu.DualIssueForwardingTesterLab4                   
Lab4 / testOnly dinocpu.FullApplicationsTesterLab4
Lab4 / testOnly dinocpu.LoopsUnrolledFullApplicationsTesterLab4
```

## Full Application Traces
TODO: add links to the commit traces

## Submissions
TODO: add links to Gradescope

------------------------------------------------------------------------------------------------------------
# Part II: Performance Evaluation
In this part, you will collect the data for the next steps.

Now that we completed three different CPU designs (single-cycle, pipelined, pipelined-dual-issue), we can evaluate and compare their performances on different benchmarks.

## The Trade-offs of Making Increasingly Complex CPU Designs
The dual-issue pipelined CPU design will always require fewer or the same number cycles to complete a program compared the original pipelined CPU.
The dual-issue CPU can issue upto 2 instructions per cycle, while the original pipelined CPU only issue exactly 1 instruction per cycle.
Along with the fact that dual-issue and the original pipelined share the same penalty for mispredicted branches and jump instructions as well as the same stalling/flushing mechanism, using the dual-issue design will always result in a speedup compared to the original pipelined design.

However, using the dual-issue design also comes with a cost.
One of the most notable change of the dual-issue design is that it has a much more complex fetch stage compared to the original pipelined design.
This caused by complex logics for determining the data dependencies among fetched instructions.
Other than that, the hazard detection unit, the forwarding unit, and the register file are also more complicated at the circuitry level.

We will illustrate this fact using the following latency data for all of questions in this assignment,
|                      | IF latency | ID latency | EX latency | MEM latency | WB latency |
|----------------------|--------|--------|--------|--------|--------|
| pipelined            |  50 ps | 100 ps |  200ps |  180ps |  190ps |
| pipelined-dual-issue | 220 ps | 140 ps |  200ps |  200ps |  210ps |

|              | latency |
|--------------|---------|
| single-cycle |  800 ps |

ps: picoseconds (1 ps = 10^-12 seconds)

## Loop-unrolling
TODO

## Collecting Data
The following command will simulate a binary with a CPU type and will output the number of simulated cycles at the end of simulation,
```
runMain dinocpu.simulate <binary-name> <cpu-type>
```
For this assignment, the CPU types of interest are,
- `single-cycle`
- `pipelined`
- `pipelined-dual-issue`

and the binaries of interest are,
- `multiply.riscv`
- `multiply-loops-unrolled.riscv`
- `median.riscv`
- `median-loops-unrolled.riscv`
- `qsort.riscv`
- `qsort-loops-unrolled.riscv`
- `rsort.riscv`
- `rsort-loops-unrolled.riscv`
- `towers.riscv`
- `towers-loops-unrolled.riscv`
- `vvadd.riscv`
- `vvadd-loops-unrolled.riscv`

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
- For every workload, there would be some functions or loops that consume most of the cycles.
It is useful to find the functions or loops (either in the C code or the assembly code) and do analysis on how they being run by the CPU.
- Note that the commit trace only contains the PC of the instructions that are committed.
There are instructions that might waste several CPU cycles as such the ones followed a mis-predicted branch or a jump, those instrutions enter the pipeline and then being flushed.
Since they consume CPU cycles, they are also part of the workload performance.
This method is called *program profiling*, which helps finding part of a program producing most of time complexity.
- For program profiling, you might want to write a printf function printing the instructions that are issued to see all the PC of all instructions that ever entered the pipeline.
You can find an example of how the commit traces are produced near the end of the file `src/main/scala/pipelined/dual-issue.scala`.
- Note that for branch instructions, the pipelined CPUs would *speculatively* issue the next instructions as if the branch is not taken.
- You do not have to analyze all characteristics of the workloads (although it would be beneficial to find all aspects of the binary that affect the CPU performance, it is not essential to capture all of them for this assignment).
An idea or two on what caused each of the speedups and slowdowns would be sufficient for full credits.
Make sure that the ideas are well-explained.

# Conclusion
In this assignment, we hope to provide quantitative evidence of how a new CPU design influences the performance of software, and how understanding the microarchitecture of a CPU, even at the high level, might help improving software performance.
We also hope to show a general theme of making hardware design decisions: there almost always some trade-offs between the design complexity and the overall performance improvement.

# Logistics

## Grading
Part I will be automatically graded on Gradescope.
See the Submission section for more details.
| Part                | Percentage |
|---------------------|------------|
| Part 4.1            |        20% |
| Part 4.2 Question 1 |        20% |
| Part 4.2 Question 2 |        20% |
| Part 4.2 Question 3 |        10% |
| Part 4.3 Question 4 |        15% |
| Part 4.3 Question 5 |        15% |

## Submission
**Warning:** read the submission instructions carefully. Failure to adhere to the instructions will result in a loss of points.

### Code Portion
You will upload the following files to Gradescope on the `Assignment 4: Code` assignment,
- `src/main/scala/components/dual/hazard.scala`
- `src/main/scala/components/dual/forwarding.scala`

Once uploaded, Gradescope will automatically download and run your code.
This should take less than 10 minutes.
For each part of the assignment, you will receive a grade.
If all of your tests are passing locally, they should also pass on Gradescope unless you made changes to the I/O, which you are not allowed to do.

**Note:** Make sure that you comment out or remove all printing statements in your submissions.
There are a lot of long tests for this assignment.
The auto-grader might fail complete grading within the allocated time if there are too many output statements.
(Outputting to `stdout/stderr` is very costly time-wise!)

### Written Portion
You will upload your answers for the `Assignment 4: Written` assignment to Gradescope.
Please upload a separate page for each answer!
Additionally, I believe Gradescope allows you to circle the area with your final answer.
Make sure to do this!

We will not grade any questions for which we are unable to read.
Be sure to check your submission to make sure it's legible, right-side-up, etc.

### Academic misconduct reminder
You are to work on this project **individually**.
You may discuss *high level concepts* with one another (e.g., talking about the diagram), but all work must be completed on your own.

**Remember, DO NOT POST YOUR CODE PUBLICLY ON GITHUB!**
Any code found on GitHub that is not the base template you are given will be reported to SJA.
If you want to sidestep this problem entirely, don't create a public fork and instead create a private repository to store your work.
GitHub now allows everybody to create unlimited private repositories for up to three collaborators, and you shouldn't have any collaborators for your code for this assignment.

## Hints
- **Start early!** Completing part 4.1 is essential for the next parts.
- If you need help, come to office hours for the TAs, or post your questions on Campuswire.
- See common errors for some common errors and their solutions.

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
Lab4 / testOnly dinocpu.SmallTestsTesterLab4
Lab4 / testOnly dinocpu.DualIssueForwardingTesterLab4                   
Lab4 / testOnly dinocpu.FullApplicationsTesterLab4
Lab4 / testOnly dinocpu.LoopsUnrolledFullApplicationsTesterLab4
```

### Full Application Traces
TODO: add links to the commit traces

## Submissions
TODO: add links to Gradescope

------------------------------------------------------------------------------------------------------------
# Part II: Performance Evaluation
In this part, you will collect the data for the next steps.

Now that we completed three different CPU designs (single-cycle, pipelined, pipelined-dual-issue), we can evaluate and compare their performances on different benchmarks.

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

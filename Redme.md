# RISC-V Ibex Architecture Study

## About This Repository

This repository is a learning project focused on understanding the architecture and internal working of the **Ibex RISC-V CPU core**.

The purpose of this repository is not to build a new processor. Instead, the goal is to study an existing RISC-V CPU core and understand how its different hardware components work together to execute instructions.

## What is Ibex?

Ibex is a small, open-source, 32-bit RISC-V CPU core developed and maintained by lowRISC.

It is an actual hardware implementation of the RISC-V Instruction Set Architecture (ISA).

In simple words:

> RISC-V defines the rules, while Ibex is a CPU design that follows those rules.

## What Will I Learn?

This repository covers:

- What a CPU and CPU core are
- What an Instruction Set Architecture is
- What RISC-V is
- What Ibex is
- The history and origin of Ibex
- The main components of Ibex
- How instructions move through the CPU
- How registers, ALU, memory and control logic work
- How RISC-V instructions are executed
- How Ibex is implemented using RTL/SystemVerilog
- How the Ibex source code is organized


## Main Goal

The final goal is to be able to take a simple RISC-V instruction and explain what happens inside the Ibex CPU core from the moment the instruction is fetched until its result is produced.

## Example

For an instruction such as:

```asm
add x5, x6, x7
```

I want to understand:

```text
Instruction
     ↓
Fetch
     ↓
Decode
     ↓
Read Registers
     ↓
ALU Operation
     ↓
Result
     ↓
Write Result
```

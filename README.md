# RISC-V Ibex Architecture Study

## About This Repository

This repository is a learning project focused on understanding the architecture and internal working of the **Ibex RISC-V CPU core**.

The purpose of this repository is not to build a new processor. Instead, the goal is to study an existing RISC-V CPU core and understand how its different hardware components work together to execute instructions.

## What is Ibex?

Ibex is a small, open-source, 32-bit RISC-V CPU core developed and maintained by lowRISC.

It is an actual hardware implementation of the RISC-V Instruction Set Architecture (ISA).

The Ibex core is designed to support the standard **RV32I (40 base integer instructions)** or **RV32E (16 base instructions)** instruction sets, expandable to **over 100 instructions** depending on the enabled extensions.

In simple words:
> RISC-V tells a CPU what instructions mean, and Ibex is a CPU core that implements those instructions.


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

## RISC-V vs Ibex

These two terms should not be confused.

```text
RISC-V
  ↓
Instruction Set Architecture
  ↓
Defines instructions and rules

Ibex
  ↓
CPU Core
  ↓
Implements those RISC-V rules
```

For example, RISC-V defines an instruction such as:

```asm
add x5, x6, x7
```

The Ibex hardware contains the components required to execute this instruction.

## What Does CPU Core Mean?

A CPU core is the part of a processor that performs instruction execution.

It fetches instructions, understands them, obtains the required data, performs operations and produces results.

A simplified view is:

```text
Fetch
  ↓
Decode
  ↓
Read Data
  ↓
Execute
  ↓
Write Result
```

Ibex performs these operations using hardware components such as the program counter, register file, decoder, ALU and memory interfaces.

## Important Point

Ibex is not a programming language.

It is not an operating system.

It is not the RISC-V ISA itself.

It is a hardware CPU core that implements RISC-V.

---

# Why Study Ibex?

## Purpose of Studying Ibex

The purpose of studying Ibex is to understand how a real RISC-V CPU core works internally.

Learning RISC-V instructions alone tells us what an instruction means.

Studying Ibex helps us understand how hardware actually executes that instruction.

## From Instruction to Hardware

Consider:

```asm
add x5, x6, x7
```

At the RISC-V level, we know:

```text
x5 = x6 + x7
```

But studying Ibex allows us to ask:

- Where does the instruction come from?
- How does the CPU recognize that it is an ADD instruction?
- How are x6 and x7 read?
- Which hardware performs the addition?
- Where does the result go?
- How is x5 updated?

These questions lead from the RISC-V ISA to the actual CPU implementation.

# History of Ibex

## Origin

Ibex originated from work in the PULP (Parallel Ultra-Low-Power) research ecosystem.

Its earlier name was Zero-riscy.

Zero-riscy was developed as a smaller RISC-V processor derived from the RI5CY processor.

## Development

The project was later taken over by lowRISC.

After this transition, the project became known as Ibex.

A simplified history is:

```text
RI5CY
  ↓
Zero-riscy
  ↓
lowRISC development
  ↓
Ibex
```

## Why Was It Created?

The project focused on creating a small and configurable RISC-V CPU core suitable for applications where hardware size, power consumption and implementation requirements matter.

## Important Names

### PULP

PULP stands for:

**Parallel Ultra-Low-Power**

It is an open research platform focused on energy-efficient computing.

### lowRISC

lowRISC is the organization that develops and maintains Ibex.

## Important Note

Ibex is not an acronym.

The name "Ibex" is the name of the CPU core/project.

---

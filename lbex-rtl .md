# 05 - Ibex RTL

# What is RTL?

RTL stands for:

**Register Transfer Level**

RTL is a way of describing **how digital hardware works**.

In simple words:

> RTL describes what data is stored, where the data moves, and what operations are performed on that data.

A CPU such as Ibex is made from many digital hardware components.

RTL describes how these components work together.

---

# Why is RTL important for Ibex?

Earlier, we studied the Ibex architecture at a block-diagram level.

For example:

```text
Instruction
     ↓
IF Stage
     ↓
ID/EX Stage
     ↓
Register File
     ↓
ALU
     ↓
Result
```

This diagram tells us **what components exist and how they are connected**.

But this diagram does not tell us exactly how the hardware is implemented.

RTL takes us one level deeper.

```text
Architecture
      ↓
What components exist?
      ↓
RTL
      ↓
How are those components actually implemented?
```

So we can think of it like this:

```text
RISC-V ISA
    ↓
Defines what instructions mean
    ↓
Ibex Architecture
    ↓
Defines the major CPU components
    ↓
RTL
    ↓
Describes how those components are built
    ↓
SystemVerilog
    ↓
Language used to describe the hardware
```

---

# RTL vs Architecture

These two concepts should not be confused.

## Architecture

Architecture tells us:

```text
What does the CPU contain?

For example:

PC
Register File
ALU
Decoder
Controller
LSU
CSR
```

## RTL

RTL tells us:

```text
How do these components
exchange data and perform operations?
```

For example:

```text
Register A
    │
    │ data
    ↓
   ALU
    │
    │ result
    ↓
Register B
```

Architecture tells us that an ALU exists.

RTL describes the data movement and logic around that ALU.

---

# What does "Register Transfer" mean?

The name itself gives us a clue.

## Register

A register is a small storage element inside the CPU.

For example:

```text
Register A = 10
Register B = 20
```

## Transfer

Transfer means moving data from one place to another.

For example:

```text
Register A
    │
    │ 10
    ↓
    ALU
```

or:

```text
ALU
 │
 │ 30
 ↓
Register B
```

So:

```text
Register
    ↓
Data moves
    ↓
Logic
    ↓
Data moves
    ↓
Register
```

This is the basic idea behind the name:

**Register Transfer Level**

---

# Simple RTL Example

Suppose we have:

```text
Register A = 10
Register B = 20
```

and we want to add them.

The hardware operation can be represented as:

```text
Register A
    │
    │ 10
    ↓
   ┌─────┐
   │ ALU │
   │ ADD │
   └──┬──┘
      │
      │ 30
      ↓
Register C
```

The result is:

```text
Register C = Register A + Register B
```

So:

```text
C = A + B
```

This is a simple example of the kind of data movement that RTL describes.

---

# RTL Example Using a CPU Instruction

Consider the RISC-V instruction:

```text
add x5, x6, x7
```

Its meaning is:

```text
x5 = x6 + x7
```

Suppose:

```text
x6 = 10
x7 = 20
```

Then:

```text
x5 = 10 + 20

x5 = 30
```

At the architecture level we can show:

```text
x6 ─────┐
        ↓
       ALU
        ↑
x7 ─────┘
        │
        ↓
       x5
```

At the RTL level, we describe the actual hardware data paths and control signals that make this happen.

Conceptually:

```text
Register File
      │
      │ x6 = 10
      ├──────────────┐
      │              │
      │ x7 = 20      ↓
      └──────────►  ALU
                     │
                     │ 10 + 20
                     ↓
                    30
                     │
                     ↓
                Register File
                     │
                     ↓
                    x5
```

---

# RTL is NOT Software Code

This is very important.

When you write Python:

```python
c = a + b
```

you are writing a software program.

The CPU executes that program.

But when you write RTL in SystemVerilog, you are describing the **hardware that can perform operations**.

A simple comparison:

```text
Python
  ↓
Instructions for software
  ↓
Runs on a CPU


SystemVerilog RTL
  ↓
Description of hardware
  ↓
Used to create/design hardware
```

Therefore:

```text
Python → tells a computer what program to execute

SystemVerilog → describes what hardware should exist
```

---

# What is SystemVerilog?

SystemVerilog is a:

**Hardware Description and Verification Language**

It is commonly abbreviated as:

```text
SystemVerilog
     ↓
     SV
```

SystemVerilog can be used to describe digital hardware such as:

```text
Registers
ALUs
Multiplexers
Counters
Controllers
CPUs
Memory interfaces
```

Ibex is implemented using SystemVerilog RTL.

Therefore:

```text
Ibex
  ↓
RTL
  ↓
SystemVerilog
  ↓
Hardware description
```

---

# Hardware Description vs Normal Programming

A normal programming language mainly describes operations that software should perform.

For example:

```python
a = 10
b = 20
c = a + b
```

This tells a program to calculate a value.

In hardware description, we describe the hardware that performs the operation.

Conceptually:

```text
Input A ───┐
           ↓
          ALU
           ↓
         Output
           ↑
Input B ───┘
```

The important difference is:

```text
Software
    ↓
Instructions are executed

Hardware description
    ↓
Hardware structure and behavior are described
```

---

# Simple SystemVerilog Example

A very simplified example of an adder could look like:

```systemverilog
module simple_adder (
    input  logic [31:0] a,
    input  logic [31:0] b,
    output logic [31:0] result
);

assign result = a + b;

endmodule
```

This describes a hardware block with:

```text
Input A
   ↓
   ┌─────────────┐
   │    Adder    │
   └──────┬──────┘
          ↓
       Result
          ↑
   Input B
```

If:

```text
a = 10
b = 20
```

then:

```text
result = 30
```

This is only a simple example.

Real Ibex RTL is much more complex because it contains instruction decoding, registers, control logic, ALU operations, memory access, CSRs, pipeline control, exceptions, interrupts, and other hardware.

---

# What is a Module?

In SystemVerilog, hardware is commonly organized into **modules**.

A module can be thought of as a hardware block.

For example:

```text
        ┌──────────────────┐
        │     ALU Module   │
        │                  │
A ─────►│                  │─────► Result
B ─────►│                  │
        └──────────────────┘
```

A module can have:

```text
Inputs
Outputs
Internal logic
Other hardware blocks
```

A large CPU can therefore be built from many modules.

Conceptually:

```text
                 CPU
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
      ALU     Register     Decoder
               File
       ↓          ↓          ↓
      LSU       CSR      Controller
```

Ibex uses many SystemVerilog modules to implement its CPU functionality.

---

# What are Signals?

Hardware components communicate using signals.

Think of a signal as a wire carrying information.

For example:

```text
Register File
     │
     │ register value
     ↓
    ALU
```

The line between them represents a connection carrying data.

There can also be control signals.

For example:

```text
Decoder
   │
   │ control signal
   ↓
  ALU
```

The control signal can tell the ALU which operation is required.

For example:

```text
ALU operation = ADD
```

or:

```text
ALU operation = SUB
```

---

# Data Signals vs Control Signals

There are two useful concepts to understand.

## Data Signal

Carries actual data.

Example:

```text
x6 = 10
```

The value `10` can travel through a hardware connection.

```text
Register File
      │
      │ 10
      ↓
     ALU
```

## Control Signal

Tells hardware what to do.

For example:

```text
ALU operation = ADD
```

Conceptually:

```text
Decoder
   │
   │ "perform ADD"
   ↓
  ALU
```

Therefore:

```text
Data signals
    ↓
Carry values

Control signals
    ↓
Control hardware behavior
```

---

# RTL and Clock

Digital CPUs work with a clock.

Think of a clock as a repeating timing signal:

```text
Clock:

__|‾|__|‾|__|‾|__|‾|__
  ↑    ↑    ↑    ↑
```

Registers use clock events to store/update values.

For example:

```text
Register A
    │
    │
    ↓
   ALU
    │
    │ result
    ↓
Register B
```

At an appropriate clock event, Register B can store the result.

Conceptually:

```text
Before clock:

A = 10
B = ?

ALU = A + 20


After clock:

B = 30
```

This is why registers and clock behavior are important when studying RTL.

---

# Combinational Logic

Some hardware produces an output based directly on current inputs.

For example:

```text
A = 10
B = 20

A + B = 30
```

The ALU is largely based on combinational logic.

Conceptually:

```text
A ─────┐
       ↓
      ALU ─────► Result
       ↑
B ─────┘
```

If the inputs change, the output can change accordingly.

---

# Sequential Logic

Other hardware stores information.

Registers are examples of sequential logic.

For example:

```text
        ┌───────────┐
Data ──►│ Register  │
        └─────┬─────┘
              │
             Q
```

The register stores a value and updates according to clock/control conditions.

A simplified view is:

```text
Combinational Logic
       ↓
     Result
       ↓
    Register
       ↓
     Stored
      Data
```

A CPU combines both:

```text
Combinational Logic
        +
Registers
        +
Clock
        ↓
     CPU
```

---

# RTL Example: Register + ALU

Imagine a very simple hardware system:

```text
Register A = 10
Register B = 20

        ┌──────────────┐
A ─────►│              │
        │     ALU      │────► Result = 30
B ─────►│     ADD      │
        └──────────────┘
                  │
                  ↓
              Register C
```

The conceptual RTL operation is:

```text
C = A + B
```

This means:

```text
Read A
Read B
    ↓
Perform addition
    ↓
Store result in C
```

This same basic idea appears many times inside a CPU.

---

# RTL and the Ibex Pipeline

Now connect RTL to the Ibex architecture we studied earlier.

Ibex has a simplified 2-stage pipeline:

```text
IF
↓
ID/EX
```

The RTL implements the hardware required for these stages.

Conceptually:

```text
             Ibex
              │
       ┌──────┴──────┐
       ↓             ↓
     IF Stage      ID/EX Stage
       │             │
       ↓             ↓
      Fetch      Decode/Execute
                     │
              ┌──────┼──────┐
              ↓      ↓      ↓
            Reg     ALU     LSU
            File
```

The architecture diagram tells us these blocks exist.

The RTL tells us how the blocks are actually connected and controlled.

---

# Example: ADD Instruction at RTL Level

Let's follow:

```text
add x5, x6, x7
```

Assume:

```text
x6 = 10
x7 = 20
```

The high-level flow is:

```text
Instruction
     ↓
Decode
     ↓
Read x6 and x7
     ↓
ALU
     ↓
10 + 20
     ↓
30
     ↓
Write x5
```

At RTL level, we can think about the actual signals involved.

```text
Instruction
     │
     ▼
Instruction Decoder
     │
     ├── rs1 = x6
     ├── rs2 = x7
     ├── rd  = x5
     └── ALU operation = ADD
              │
              ▼
        Register

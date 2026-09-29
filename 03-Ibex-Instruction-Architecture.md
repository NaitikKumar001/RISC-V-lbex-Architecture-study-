
# 02 - Ibex Instruction Architecture

## 2. Instruction Memory

Start from the left side.

```text
Instruction Memory
       │
       │ instruction
       ▼
   IF Stage
```

Instruction memory contains the program's machine instructions.

For example, imagine memory contains:

```text
Address       Instruction

0x1000        add x5, x6, x7
0x1004        sub x8, x9, x10
0x1008        lw  x11, 0(x12)
0x100C        sw  x11, 0(x12)
```

These are instructions encoded as binary values in real hardware.

The CPU needs to retrieve them one at a time.

That's the job of the **IF stage**.

---

## 3. PC — Program Counter

Inside the IF stage is the **Program Counter (PC)**.

PC is basically the CPU's instruction-address tracker.

Suppose:

```text
PC = 0x1000
```

That tells Ibex:

> "The next instruction I need is at address 0x1000."

Memory:

```text
0x1000 → add x5, x6, x7
```

So Ibex requests the instruction at `0x1000`.

After the instruction flow continues, the PC normally moves toward the next instruction.

For example:

```text
0x1000 → add
0x1004 → sub
0x1008 → lw
```

The exact PC behavior becomes more complicated for branches, jumps, exceptions, compressed instructions, etc., but this is the basic idea.

---

## 4. Instruction Fetch

Now we actually get the instruction from memory.

```text
PC
 │
 │ address
 ▼
Instruction Memory
 │
 │ instruction data
 ▼
IF Stage
```

Suppose:

```text
PC = 0x1000
```

Memory returns:

```text
add x5, x6, x7
```

So:

```text
PC = 0x1000
       ↓
Instruction Memory
       ↓
add x5, x6, x7
```

The official Ibex IF stage communicates with instruction memory through an instruction-side interface.

It can request an instruction address and receive the instruction data.

---

## 5. Prefetch Buffer

After fetching, Ibex has a **prefetch buffer**.

Think of it like a small waiting area.

```text
Instruction Memory
       ↓
Prefetch Buffer
       ↓
ID/EX
```

### Why have a buffer?

Imagine you're reading a book.

Instead of:

```text
Read page 1
Stop
Read page 2
Stop
Read page 3
Stop
```

you could prepare the next few pages:

```text
Page 1
Page 2
Page 3
```

Similarly, Ibex can fetch instructions ahead of time and keep them available.

The official documentation says Ibex's prefetch buffer fetches instructions linearly and stores them, together with the PC they came from, in a fetch FIFO.

Its default FIFO depth is 3.

So conceptually:

```text
              Prefetch Buffer

        ┌────────────────────┐
        │ Instruction 1      │
        │ PC = 0x1000        │
        ├────────────────────┤
        │ Instruction 2      │
        │ PC = 0x1004        │
        ├────────────────────┤
        │ Instruction 3      │
        │ PC = 0x1008        │
        └────────────────────┘
```

This helps keep the CPU supplied with instructions.

---

## 6. Compressed Decoder

Now look at:

```text
Compressed Decoder
```

RISC-V has compressed instructions through the **C extension**.

Normal RISC-V instructions are commonly 32 bits, while compressed instructions can be 16 bits.

### Normal instruction

```text
████████████████████████████████
           32 bits
```

### Compressed instruction

```text
████████████████
    16 bits
```

Ibex's IF stage expands compressed instructions so that the main decoder can work with an uncompressed representation.

So conceptually:

```text
16-bit compressed instruction
           ↓
Compressed Decoder
           ↓
32-bit internal representation
           ↓
ID Stage
```

> **Important:** The compressed decoder is part of the IF-side processing; it does **NOT** mean Ibex has another pipeline stage.

---

## 7. Now We Enter ID/EX

This is the most important part.

The instruction now enters:

```text
ID/EX
```

ID means:

```text
Instruction Decode
```

EX means:

```text
Execute
```

So:

```text
ID/EX = Decode + Execute
```

In the standard 2-stage Ibex pipeline, these operations happen within the same pipeline stage.

The official documentation specifically says instruction decoding, execution, register read and register write occur in this stage.

---

## 8. Instruction Decoder

Suppose the instruction is:

```text
add x5, x6, x7
```

The decoder looks at the instruction's encoded fields.

Conceptually, it needs to determine:

```text
Operation = ADD

Source 1 = x6

Source 2 = x7

Destination = x5
```

So:

```text
             add x5, x6, x7
                    │
                    ▼
          ┌──────────────────┐
          │ Instruction      │
          │ Decoder          │
          └────────┬─────────┘
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
     ADD           x6          x7
   operation      source      source
```

The decoder doesn't actually perform the addition.

It tells the rest of the hardware:

> "This is an ADD instruction, and these are the registers involved."

The actual Ibex ID stage contains the logic for controlling decode/execution and selecting ALU inputs and register-file write data.

---

## 9. Controller

The **Controller** coordinates the CPU's actions.

Think of it as the traffic controller.

For:

```text
add x5, x6, x7
```

the controller helps coordinate:

```text
Read x6
Read x7
      ↓
Send operands to ALU
      ↓
Perform ADD
      ↓
Take result
      ↓
Write result to x5
```

It also handles more complicated situations such as instructions that require multiple cycles.

The official source describes the controller as a state machine controlling the overall execution of the processor.

---

## 10. Register File

Now we reach one of the most important blocks:

```text
Register File
```

RISC-V has integer registers such as:

```text
x0
x1
x2
...
x31
```

For our example:

```text
add x5, x6, x7
```

Suppose:

```text
x6 = 10
x7 = 20
```

The register file provides:

```text
x6 → 10
x7 → 20
```

to the execution logic.

So:

```text
Register File

x5 = ?
x6 = 10 ────────┐
x7 = 20 ────────┤
                 ▼
                ALU
```

Later:

```text
ALU result = 30

30 → x5
```

---

## 11. Important: Register Read and Write

This is where the earlier simplified explanation can cause confusion.

You might think:

```text
Decode
   ↓
Register Read
   ↓
Execute
   ↓
Writeback
```

are four separate pipeline stages.

They are **not** in normal 2-stage Ibex.

Instead:

```text
              ID/EX
┌─────────────────────────────────────┐
│ Decode                               │
│   ↓                                  │
│ Register Read                        │
│   ↓                                  │
│ Execute                              │
│   ↓                                  │
│ Register Write                       │
└─────────────────────────────────────┘
```

They happen inside the **ID/EX stage**.

> **This is one of the most important things to remember for your repository.**

---

## 12. MUX / Operand Selection

Look at the small blocks before the ALU.

These are **MUXes**.

MUX means:

> Multiplexer

A MUX chooses one input from multiple possible inputs.

### Why does the ALU need this?

Because the ALU doesn't always receive:

```text
register + register
```

For example:

### ADD

```text
x6 + x7
```

### ADDI

```text
x6 + 10
```

The second operand could therefore be:

```text
Register value
      OR
Immediate value
```

The MUX chooses the correct one.

Conceptually:

```text
                 ┌──────────────┐
Register ───────►│              │
                 │     MUX      ├────► ALU
Immediate ──────►│              │
                 └──────────────┘
```

The controller/decoder tells the MUX which input to select.

---

## 13. ALU

Now comes the CPU's calculator:

```text
ALU
```

ALU means:

> Arithmetic Logic Unit

It performs operations such as:

- ADD
- SUB
- AND
- OR
- XOR
- Comparison
- Shift

For:

```text
add x5, x6, x7
```

if:

```text
x6 = 10
x7 = 20
```

the ALU performs:

```text
10 + 20 = 30
```

So:

```text
x6 = 10 ───┐
           │
           ▼
          ALU ───► 30
           ▲
           │
x7 = 20 ───┘
```

The result is then written to:

```text
x5 = 30
```

---

## 14. MULT/DIV

Ibex can also contain multiply/divide hardware depending on

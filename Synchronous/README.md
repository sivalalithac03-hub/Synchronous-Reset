# D Flip-Flop with Synchronous Reset Using Verilog

## Project Overview

This project implements a **D Flip-Flop with a Synchronous Reset using Verilog HDL**.

A D Flip-Flop is a sequential logic circuit that stores one bit of data. Normally, the value of the input `D` is transferred to the output `Q` at the rising edge of the clock.

A synchronous reset differs from an asynchronous reset because the reset operation occurs only at the active clock edge.

In this project, an **active-high synchronous reset** is used.

---

## Objectives

The objectives of this project are:

* Design a D Flip-Flop using Verilog HDL.
* Implement a synchronous reset.
* Create a testbench to verify the design.
* Simulate the circuit using Icarus Verilog.
* Generate a VCD waveform.
* Verify the synchronous reset operation using GTKWave.

---

## What is a Synchronous Reset?

A synchronous reset is a reset signal that affects the output only when the active clock edge occurs.

For this project:

```text
RESET = 1 + Rising Clock Edge → Q = 0
```

If reset changes while there is no clock edge, the output does not immediately change.

For example:

```text
RESET changes from 0 to 1
          ↓
No clock edge
          ↓
Q remains unchanged
          ↓
Rising clock edge
          ↓
Q becomes 0
```

---

## D Flip-Flop Operation

When reset is LOW:

```text
Rising Clock Edge + D = 0 → Q = 0
Rising Clock Edge + D = 1 → Q = 1
```

When reset is HIGH:

```text
Rising Clock Edge → Q = 0
```

The reset has priority over the data input.

---

## Truth Table

| Reset | Clock   | D | Q(next)    | Operation          |
| ----- | ------- | - | ---------- | ------------------ |
| 1     | ↑       | X | 0          | Synchronous Reset  |
| 0     | ↑       | 0 | 0          | Store 0            |
| 0     | ↑       | 1 | 1          | Store 1            |
| 1     | No Edge | X | Previous Q | No immediate reset |
| 0     | No Edge | X | Previous Q | Hold               |

`X` means that the value is irrelevant.

---

## Block Diagram

```text
                 +----------------------+
                 |                      |
        D ------>|                      |
                 |      D Flip-Flop     |-----> Q
       CLK ----->|                      |
                 |                      |
     RESET ----->|   Synchronous Reset  |
                 |                      |
                 +----------------------+
                          |
                          |
                         Q_BAR
```

---

## Inputs and Outputs

| Signal  | Direction | Description                   |
| ------- | --------- | ----------------------------- |
| `clk`   | Input     | Clock signal                  |
| `reset` | Input     | Active-high synchronous reset |
| `d`     | Input     | Data input                    |
| `q`     | Output    | Flip-Flop output              |
| `q_bar` | Output    | Complementary output          |

---

## Project Structure

```text
d-flip-flop-synchronous-reset/
│
├── README.md
│
├── src/
│   └── d_flip_flop_sync_reset.v
│
├── testbench/
│   └── tb_d_flip_flop_sync_reset.v
│
└── simulation/
    ├── simulation_output.txt
    ├── d_flip_flop_sync_reset.vcd
    └── waveform.png
```

---

## Verilog Implementation

The main design is located at:

```text
src/d_flip_flop_sync_reset.v
```

The core logic is:

```verilog
always @(posedge clk) begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
```

The important difference from an asynchronous reset is that the sensitivity list contains only:

```verilog
posedge clk
```

The reset is checked only when a rising clock edge occurs.

---

## Testbench

The testbench is located at:

```text
testbench/tb_d_flip_flop_sync_reset.v
```

The testbench verifies:

1. Initial reset.
2. Normal operation with `D = 1`.
3. Normal operation with `D = 0`.
4. Normal operation with `D = 1`.
5. Reset asserted between clock edges.
6. Reset operation at the next rising clock edge.
7. Normal operation after reset is released.

---

## Simulation

### Tools Required

* Verilog HDL
* Icarus Verilog
* GTKWave

### Compile

Run the following command from the project root:

```bash
iverilog -o dff_sync_sim src/d_flip_flop_sync_reset.v testbench/tb_d_flip_flop_sync_reset.v
```

### Run

```bash
vvp dff_sync_sim
```

### Save Simulation Output

```bash
vvp dff_sync_sim > simulation/simulation_output.txt
```

### VCD Waveform

The testbench generates:

```text
simulation/d_flip_flop_sync_reset.vcd
```

### View Waveform

Open the VCD file using GTKWave:

```bash
gtkwave simulation/d_flip_flop_sync_reset.vcd
```

Add:

```text
clk
reset
d
q
q_bar
```

---

## Simulation Result

The simulation verifies that the D Flip-Flop correctly stores the input `D` at the rising edge of the clock.

For example:

```text
D = 1
Rising Clock Edge
Q = 1
```

When the synchronous reset is asserted:

```text
RESET = 1
```

the output does not necessarily change immediately.

Instead, the circuit waits for the next rising clock edge:

```text
RESET = 1
        ↓
Rising Clock Edge
        ↓
Q = 0
```

This confirms that the reset is synchronous.

---

## Important Observation

Consider the following sequence:

```text
Time = 38 ns
RESET = 1
CLK = 1
Q = 1

Time = 40 ns
RESET = 1
CLK = 0
Q = 1

Time = 45 ns
RESET = 1
CLK = 1
Q = 0
```

The reset was active before the clock edge, but `Q` changed to `0` at the rising edge.

This is the main characteristic of a synchronous reset.

---

## Synchronous vs Asynchronous Reset

| Feature                  | Synchronous Reset | Asynchronous Reset             |
| ------------------------ | ----------------- | ------------------------------ |
| Requires clock edge      | Yes               | No                             |
| Reset response           | At clock edge     | Immediate                      |
| Sensitivity list         | `posedge clk`     | `posedge clk or posedge reset` |
| Clock required for reset | Yes               | No                             |
| Main advantage           | Controlled timing | Immediate reset                |

---

## Applications

D Flip-Flops with synchronous reset are used in:

* Registers
* Counters
* Shift registers
* Digital control systems
* State machines
* Processor systems
* FPGA designs
* Sequential logic circuits

---

## Advantages

* Reset behavior is synchronized with the clock.
* Easier to control timing in synchronous systems.
* Avoids asynchronous changes to the output during normal operation.
* Simple to implement in Verilog.
* Easy to verify through simulation.

---

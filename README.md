# 4-Bit Ring Counter in Verilog

A simple 4-bit ring counter implemented in Verilog with a testbench. The counter shifts a loaded value one bit to the right on each clock cycle, wrapping the LSB back to the MSB — creating a rotating pattern.

---

## What It Does

- On reset, the counter loads a 4-bit value (`load`)
- On each rising clock edge (when not in reset), it shifts right by one bit
- The bit shifted out of position 0 re-enters at position 3 (circular shift)
- Default load value in the testbench: `4'b1000`

### Shift Sequence (starting from `1000`)

```
1000 -> 0100 -> 0010 -> 0001 -> 1000 -> ...
```

---

## File Structure

```
.
├── ring.v        # Ring counter module
└── tb.v          # Testbench
```

---

## Module: `ring`

```verilog
module ring(input clk, reset, input [3:0] load, output reg [3:0] q);
```

| Port    | Direction | Width | Description                          |
|---------|-----------|-------|--------------------------------------|
| `clk`   | input     | 1     | Clock signal                         |
| `reset` | input     | 1     | Synchronous reset; loads value       |
| `load`  | input     | 4     | Value to load on reset               |
| `q`     | output    | 4     | Current counter state                |

---

## Simulation

### On EDA Playground

1. Go to [https://www.edaplayground.com](https://www.edaplayground.com)
2. Paste `ring.v` into the **Design** pane
3. Paste `tb.v` into the **Testbench** pane
4. Select **Icarus Verilog 0.9.7** (or later) as the simulator
5. Check **Open EPWave after run** to view the waveform
6. Click **Run**

The monitor output will print the counter value at each time step. Load the generated `dump.vcd` in EPWave to inspect signal transitions.

---

## Netlist Synthesis with Yosys

### Install Yosys (Debian/Ubuntu)

```bash
sudo apt install yosys
```

### Synthesize

```bash
yosys
```

Inside the Yosys shell:

```
read_verilog ring.v
synth -top ring
show
```

Or run it as a one-liner from the terminal:

```bash
yosys -p "read_verilog ring.v; synth -top ring; show"
```

To write out a netlist in JSON format:

```bash
yosys -p "read_verilog ring.v; synth -top ring; write_json ring_netlist.json"
```

To target a generic cell library and generate a Verilog netlist:

```bash
yosys -p "read_verilog ring.v; synth -top ring; write_verilog ring_synth.v"
```

The `show` command requires `graphviz` to be installed (`sudo apt install graphviz`) and will render the synthesized gate-level schematic in a window.

---

## Expected Output (Simulation)

```
Time = 0  || Q = xxxx
Time = 10 || Q = 1000
Time = 20 || Q = 0100
Time = 30 || Q = 0010
Time = 40 || Q = 0001
Time = 50 || Q = 1000
...
```

---

## Notes

- The shift direction is right (toward LSB), with wraparound from bit 0 to bit 3
- The reset is synchronous — it only takes effect on the rising edge of `clk`
- To change the starting pattern, modify `load` in the testbench

---

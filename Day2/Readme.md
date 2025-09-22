# Day 2 – Timing Libraries, Synthesis Approaches & Flip-Flop Coding Styles

Welcome to **Day 2** of the RISC-V SoC Tapeout Program (Week 1).

📌 **Quick Recap of ![Day 1](https://github.com/Nideshkanna/week1-rtl-design-flow/blob/main/Day1/Readme.md):**

We explored Verilog RTL design, created testbenches, ran simulations using **Icarus Verilog (iverilog)**, and performed basic synthesis with **Yosys**. We also learned how simulators detect input changes and how design + testbench flows into simulation.

---

## What’s New in Day 2?

Today, we move closer to **real-world ASIC design flows** by diving into three critical aspects:

- **Timing Libraries (`.lib`)** → What they are, how to read them, and the naming conventions used.
- **Hierarchical vs. Flat Synthesis** → Different strategies for synthesizing complex designs.
- **Flip-Flop Coding Styles & Optimizations** → Writing sequential logic in multiple ways and observing synthesis optimizations.

👉 This day builds the bridge between **basic RTL design** (Day 1) and **technology-aware synthesis** (Day 2 onwards).

---

# 🕒 Timing Libraries: An Overview

When we move from **RTL design** (abstract Verilog) to **real silicon**, the tools need to know how logic gates behave in terms of **timing, power, and area**. This knowledge comes from the **Standard Cell Library**, captured in a `.lib` file.

---

## ❓ 1. What is a `.lib` file?

A **`.lib` (Liberty file)** is a text-based file provided by the **foundry** or **PDK vendor**. It describes the properties of each **standard cell** used in synthesis and implementation.

### 🧩 Key Contents of a `.lib`:

- **Logical View** – Function of the gate (e.g., NAND, NOR, DFF).
- **Timing Information** – Propagation delay, setup time, hold time, clock-to-Q, etc.
- **Power Data** – Leakage power, dynamic (switching) power.
- **Area Information** – Gate size in silicon.
- **Operating Conditions** – Values across PVT (Process, Voltage, Temperature) corners.
- **Cell Variants (Flavors)** – Same function, different speed/power trade-offs.

📌 *Example:* `sky130_fd_sc_hd__and2_0`

- `and2` → 2-input AND gate
- `_0` → Small, slow variant (low power, small area)
- `_2` → Medium drive strength
- `_4` → Strong, fast variant (high power, more area)

![cmpimage](./images/cmpimg.png)

---

## ❓ 2. Why do we need `.lib` files?

Without timing libraries, synthesis would be like designing circuits with **imaginary gates**—no real-world behavior.

**Reasons for `.lib` files:**

1. **Technology Awareness** – RTL mapped to real gates of the PDK.
2. **Performance Control** – Enables use of *fast cells* where speed is critical.
3. **Power Efficiency** – Enables use of *slow cells* where speed isn’t critical.
4. **Flavors for Trade-offs** – Helps balance speed, power, and area.
5. **Multi-Corner Analysis** – Accounts for process variation, supply fluctuations, and temperature.
6. **Consistency Across Flow** – Used by:
    - **Synthesis (Yosys)** → Gate-level mapping
    - **STA (OpenSTA)** → Timing closure
    - **PnR (OpenROAD)** → Placement, routing decisions

---

## ❓ 3. How are `.lib` files made?

`.lib` files are generated through **Library Characterization**:

1. **Transistor-Level Simulation**
    - Each standard cell (NAND, NOR, DFF, etc.) is simulated using **SPICE**.
2. **Extract Metrics**
    - Delays, setup/hold, leakage, switching power, etc., are measured.
3. **PVT Characterization**
    - Data is captured across multiple:
        - **Process corners** (Slow, Typical, Fast → SS, TT, FF)
        - **Voltage levels** (e.g., 1.62 V, 1.8 V, 1.98 V)
        - **Temperature points** (e.g., -40°C, 25°C, 125°C)
4. **Create Variants (Flavors)**
    - By changing transistor width:
        - Wider → Faster, higher drive, more area/power
        - Narrower → Slower, less drive, more efficient
5. **Naming Convention**
    - Prefix → Tech & library (e.g., `sky130_fd_sc_hd`)
    - Cell → Function (`and2`, `dff`, `mux`)
    - Suffix → Drive strength (`_0`, `_2`, `_4`)

![diff](./images/nameexp.png)

---

## ⚡ Fast vs. Slow Cells — Comparison

| Feature | Fast Cells 🚀 | Slow Cells 🐢 |
| --- | --- | --- |
| **Delay** | Very low (fast switching) | Higher (slower switching) |
| **Area** | Larger (wide transistors) | Smaller (narrow transistors) |
| **Power** | High dynamic + leakage | Low dynamic + leakage |
| **Usage** | Critical paths (reduce delay) | Non-critical paths (save power/area) |
| **Hold Timing** | Can cause violations | Helps fix violations |
| **Performance Impact** | Improves max frequency | May limit frequency |
| **Cost Trade-off** | Speed at expense of power/area | Efficiency at expense of speed |

---

## 📝 Wrap-up

- `.lib` = Dictionary of standard cells with timing, power, and area data.
- Needed for **accurate synthesis, STA, and PnR**.
- Built using **SPICE characterization** across **PVT corners**.
- Provides **cell flavors** to balance **performance vs. efficiency**. 

# 📂 Current Setup of Liberty & Models 

Your working directory contains:

```
my_lib/
 ├── lib/
 │   └── sky130_fd_sc_hd__tt_025C_1v80.lib
 └── verilog_model/
     ├── primitives.v
     └── sky130_fd_sc_hd.v
```

![dircheck](./images/dircheck.png)

## 🔹 1. What these files contain

| File | Purpose | Content Summary |
| --- | --- | --- |
| `sky130_fd_sc_hd__tt_025C_1v80.lib` | **Liberty Timing Library** | Characterized timing, power, and area data for **Sky130 HD cells** at **TT, 25°C, 1.8V**. Contains cell definitions (`and2`, `mux`, `dff`, etc.), timing arcs, leakage, capacitance, PVT operating conditions. |
| `sky130_fd_sc_hd.v` | **Functional Verilog Model** | Defines **behavioral logic** of each standard cell in Verilog. Used during simulation (gate-level sim) to mimic functionality. Includes module ports (A, B, X, VPWR, VGND, VPB, VNB). |
| `primitives.v` | **Primitive Definitions** | Low-level building blocks (e.g., basic logic primitives, bufif, notif). Helps simulators understand how composite cells are formed. |

⚡ Key:

- `.lib` = **timing & electrical view**
- `.v` = **functional/structural view**

---

## 🔹 2. Breakdown of `sky130_fd_sc_hd__tt_025C_1v80.lib` filename

| Section | Meaning |
| --- | --- |
| `sky130` | Process node: SkyWater **130nm** |
| `fd` | **Foundry Design** kit prefix |
| `sc` | **Standard Cells** |
| `hd` | **High Density** library variant (optimized for area, lower power) |
| `tt` | **Typical Process Corner** (balanced NMOS/PMOS) |
| `025C` | **Temperature = 25°C** |
| `1v80` | **Voltage = 1.8 V nominal** |

So → This file describes **Sky130 High-Density cells** under **TT, 25°C, 1.8V** conditions.

![filename](./images/filename.png)

## 🔹 3. Example of `.lib` global definitions

From the beginning of your file:

| Parameter | Example Value | Meaning |
| --- | --- | --- |
| `library ("sky130_fd_sc_hd__tt_025C_1v80")` | — | Defines the library name |
| `delay_model` | `table_lookup` | Delays are stored as tables (vs equations) |
| `bus_naming_style` | `%s[%d]` | How buses are named (e.g., A[0], A[1]) |
| `time_unit` | `1ns` | All timing values in nanoseconds |
| `voltage_unit` | `1V` | Voltage scaling unit |
| `current_unit` | `1mA` | Current scaling unit |
| `capacitive_load_unit` | `1.0 pf` | Default unit for capacitance |
| `default_max_transition` | `1.5 ns` | Max allowed transition at outputs |
| `default_operating_conditions` | `tt_025C_1v80` | PVT corner |
| `operating_conditions {}` | 1.8V, 25°C, process=1.0 | Specifies PVT environment for this library |

📌 These defaults are **applied to every cell** inside the library unless overridden.

![libstart](./images/libstart.png)

## 🔹 4. Example Cell — AND gate

### Liberty View (`sky130_fd_sc_hd__tt_025C_1v80.lib`)

```
cell ("sky130_fd_sc_hd__and2_1") {
    leakage_power() { value: 0.0031719; when: "!A&B"; }
    leakage_power() { value: 0.0028440; when: "!A&!B"; }
    leakage_power() { value: 0.0014741; when: "A&B"; }
    leakage_power() { value: 0.0031700; when: "A&!B"; }

    area: 6.256; 
    cell_footprint: "sky130_fd_sc_hd__and2"; 
    cell_leakage_power: 0.0026650;

    pg_pin("VGND") { pg_type: "primary_ground"; related_bias_pin: "VPB"; voltage_name: "VGND"; }
}
```

| Field | Meaning |
| --- | --- |
| `cell("sky130_fd_sc_hd__and2_1")` | Cell name = 2-input AND, drive strength `_1` |
| `leakage_power {when: "!A&B"}` | Leakage depends on input state (here A=0, B=1) |
| `area: 6.256` | Cell occupies 6.256 µm² |
| `cell_footprint` | Base logical function (family of and2 cells) |
| `cell_leakage_power` | Total average leakage |
| `pg_pin("VGND")` | Power/ground pin description |

![libview](./images/3and_gate.png)

---
Verilog View (`sky130_fd_sc_hd.v`)

```
module sky130_fd_sc_hd__and2_1 (
    X,
    A,
    B,
    VPWR,
    VGND,
    VPB,
    VNB
);
    output X;
    input A, B;
    input VPWR, VGND, VPB, VNB;

    sky130_fd_sc_hd__and2 base (
        .X(X), .A(A), .B(B),
        .VPWR(VPWR), .VGND(VGND),
        .VPB(VPB), .VNB(VNB)
    );
endmodule
`endcelldefine
```

| Field | Meaning |
| --- | --- |
| `module sky130_fd_sc_hd__and2_1` | Verilog model for AND2 with drive strength `_1` |
| Ports: `A, B, X` | Functional pins (2 inputs, 1 output) |
| Ports: `VPWR, VGND, VPB, VNB` | Power, ground, body-bias connections |
| `sky130_fd_sc_hd__and2 base` | Instantiates the primitive functional block |

![verilog_view](./images/4and_gate.png)

## ✨ Key Takeaways

- `.lib` file = **timing/power/area data** for synthesis & STA.
- `.v` file = **functional model** for simulation.
- `sky130_fd_sc_hd__tt_025C_1v80.lib` = Sky130 High Density library @ **TT, 25°C, 1.8V**.
- AND2 example shows how the same cell has **two views**:
    - `.lib`: electrical behavior (delays, leakage, area).
    - `.v`: logical/functional behavior (input/output connections).


# 🖥️ Week 1 — RTL Design & Optimizations (RISC-V SoC Tapeout Program)

This repository is part of the **VSD RISC-V SoC Tapeout Program**.
In **Week-1**, we focused on the **RTL design flow** — from writing synthesizable Verilog, simulating with open-source tools, synthesizing with **Yosys**, and learning **logic optimizations** to improve area, power, and performance.

---

## 🎯 Learning Goals

* Understand **RTL coding styles** (combinational & sequential) and testbench basics.
* Run **RTL simulation** using Icarus Verilog + GTKWave.
* Perform **logic synthesis** with Yosys using **Sky130 PDK**.
* Explore **.lib standard cell libraries** (timing, power, logic).
* Generate **netlists** and perform **Gate-Level Simulation (GLS)**.
* Learn **combinational & sequential logic optimizations** for efficient RTL.
* Identify and resolve **synthesis vs simulation mismatches**.
* Apply **constraint-driven synthesis** for timing closure.

---

## 📂 Repository Structure

Each day of Week-1 is organized into a separate folder:

* [Day 1 – RTL Basics & Verilog Simulation](Day1/Readme.md)
* [Day 2 – Structural Optimizations & .lib Files](Day2/Readme.md)
* [Day 3 – Combinational & Sequential Optimizations](Day3/Readme.md)
* [Day 4 – Gate-Level Simulation & Simulation-Synthesis Mismatch](Day4/Readme.md)
* [Day 5 – Constraint-Driven Synthesis & Wrap-Up](Day5/Readme.md)

Each **day folder** contains:

* `README.md` → Concepts + detailed notes + examples
* `images/` → Diagrams, waveforms, logic structures

---

## ⚙️ Tools Used

* **Icarus Verilog (iverilog)** → RTL & GLS simulation
* **GTKWave** → Waveform viewer
* **Yosys** → Logic synthesis & netlist generation
* **Sky130 PDK** → Open-source standard cell library

---

## 📘 Weekly Highlights

### ✅ Day 1 – RTL Basics

* Learned **Verilog RTL fundamentals**.
* Explored **testbench structure** and simulation flow.
* Simulated using **Icarus Verilog + GTKWave**.
* Got introduced to **Yosys** synthesis and **.lib role**.

---

### ✅ Day 2 – Structural Optimizations

* Studied **adder/multiplier optimizations**.
* Understood **hierarchical vs flat synthesis**.
* Explored **flop coding styles** and their synthesis impact.
* Learned trade-offs between **area, power, and speed**.

---

### ✅ Day 3 – Combinational & Sequential Optimizations

* **Combinational logic:** Boolean simplification, constant propagation, redundant logic removal.
* **Sequential logic:** Register balancing, retiming, redundant flop removal.
* Example: Simplified **MUX-based expressions** → XNOR equivalent.
* Focused on **area & power savings** through RTL optimizations.

---

### ✅ Day 4 – GLS & Simulation-Synthesis Mismatch

* **GLS (Gate-Level Simulation):** Running testbenches with netlist as DUT.
* Compared **RTL Simulation vs GLS** (waveform correctness & timing validation).
* Learned about **delay-annotated gate models** for timing verification.
* Studied **Synthesis-Simulation mismatches** and causes:

  * Missing sensitivity lists
  * Blocking vs Non-Blocking assignments
  * Non-standard Verilog coding

---

### ✅ Day 5 – Constraint-Driven Synthesis & Wrap-Up

* Introduced to **timing constraints (SDC)**.
* Learned **setup/hold concepts** and how synthesis tools optimize accordingly.
* Understood **multi-cycle paths, false paths, and clock definitions**.
* Ran **constraint-driven synthesis in Yosys**.
* 📌 **Wrap-Up:** Connected all concepts → from RTL → synthesis → optimization → GLS → constraints.

---

## 📝 Week 1 Summary

Over this week, we built a strong foundation in:

* Writing **clean RTL code**.
* Running **simulation & synthesis with open-source tools**.
* Applying **logic optimizations** for efficiency.
* Verifying designs at both **RTL and gate level**.
* Understanding and applying **timing constraints**.
* Identifying and avoiding **simulation-synthesis mismatches**.

📌 **Key Takeaway:** Week-1 bridged the gap between **RTL coding → synthesis → gate-level verification → constraints**, preparing us for the **SoC-level design journey** ahead. 🚀

---

# 🔜 Moving to Week 2: BabySoC Fundamentals & Functional Modelling

In **Week-2**, we move from **RTL block-level design** to **SoC-level fundamentals**:

* **BabySoC Architecture Overview** 🖥️
* **Functional Modelling of SoC Components** ⚡
* Understanding **RTL-to-SoC integration flow**

👉 ![Week2](https://github.com/Nideshkanna/week2-research-babysoc) is where we **connect RTL building blocks to a complete SoC**. 🧩

---

🔗 Return to the main repository: [RISC-V SoC Tapeout Program](https://github.com/Nideshkanna/riscv-soc-tapeout)

---
## 🙌 Acknowledgements

* [Kunal Ghosh](https://github.com/kunalg123) – VSD SoC Program
* Open-source contributors of **Yosys**, **GTKWave**, and **Sky130 PDK**

---

📌 **Part of:** [RISC-V SoC Tapeout Program](https://github.com/Nideshkanna/riscv-soc-tapeout)
📌 **Maintainer:** [Nidesh Kanna R](https://github.com/Nideshkanna)

---

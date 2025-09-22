# Day 1 - Introduction to Verilog RTL Design and Synthesis

In this section, we explore the basics of **Verilog simulation** using the open-source tool **Icarus Verilog (iverilog)** and understand the role of **design files** and **testbenches** in RTL design flow.

---

## 🖥️ Simulator
- RTL design is checked for adherence to the specification by **simulating the design**.  
- A **simulator** is the tool used for performing this simulation.  
- In this course, we use **Icarus Verilog (iverilog)** as the simulator.

---

## 📑 Design
- The **design** is the actual Verilog code (or set of codes) which implements the required functionality.  
- The design must meet the **specifications** defined for the hardware.

---

## 🧪 TestBench
- A **testbench** is the setup that applies **stimulus (test vectors)** to the design to verify its functionality.  
- The testbench does not synthesize into hardware; it is only for verification.  

---

## ⚙️ How the Simulator Works
1. The simulator looks for **changes in input signals**.  
2. When an input changes, the corresponding **output is evaluated**.  
3. If there is **no change in the input**, then the output remains the same.  
4. Essentially, the simulator continuously **monitors input changes** to drive the design.  

---

## 📸 Visuals

### 1. Testbench Working
The **testbench setup** consists of:  
- **Stimulus Generator** (applies test vectors to the design)  
- **Design** (implements required logic)  
- **Stimulus Observer** (monitors outputs and verifies correctness)  

![Testbench Working](./images/testbenchworkingflow.png)

---

### 2. Icarus Verilog Design Flow
The Icarus Verilog-based simulation flow consists of:  
- **Design + Testbench** → Compiled by **iverilog**  
- Generates a **VCD (Value Change Dump) file**  
- **GTKWave** reads the VCD file and produces waveform results  

![Icarus Verilog Flow](./images/waveformviewer.png)

---

## 🔧 Initial Lab Setup

Before starting the labs, we need to set up the working environment and clone the required repository.  

---

### Step 1: Create a working directory
```bash
nidesh@nexus-73:~/Soc/1$ pwd
/home/nidesh/Soc/1
nidesh@nexus-73:~/Soc/1$ mkdir rtl_design_syn_lab
nidesh@nexus-73:~/Soc/1$ ls
rtl_design_syn_lab
nidesh@nexus-73:~/Soc/1$ cd rtl_design_syn_lab/
```

### Step 2: Clone the workshop repository
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/
```
![image](./images/createlab.png)

Now your folder structure looks like this:
```
sky130RTLDesignAndSynthesisWorkshop/
├── DC_WORKSHOP
├── my_lib
│   ├── lib
│   │   └── sky130_fd_sc_hd__tt_025C_1v80.lib
│   └── verilog_model
├── verilog_files
├── yosys_run.sh
└── README.md
```

### Step 3: Explore the directories

📂 my_lib

Contains all library files required for synthesis.

    Inside lib/ → Standard cell library file:

        sky130_fd_sc_hd__tt_025C_1v80.lib → Timing & power data for standard cells.

    Inside verilog_model/ → All standard cell Verilog models for simulation.

📂 verilog_files

Contains example Verilog designs and their corresponding testbenches.

    Examples include:

        Good/bad cases (good_mux.v, bad_mux.v, etc.)

        Sequential designs (counters, shift registers, FSMs).

        Testbenches (tb_*.v) for verifying each design.

✅ With this setup complete, you are ready to begin the labs using iverilog for simulation and yosys for synthesis in the next steps.


---

### ▶️ Running a Simulation with Icarus Verilog

Here’s the standard flow to run a simulation on a Verilog design and view its output for a example design we have:

### 📂 Example: MUX Design & Testbench
## Design File: good_mux.v

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
  always @(*)
  begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```
![image](./images/design.png)

## Testbench File: tb_good_mux.v

```verilog
`timescale 1ns/1ps
module tb_good_mux;
  // Inputs
  reg i0, i1, sel;
  // Output
  wire y;

  // Instantiate Unit Under Test (UUT)
  good_mux uut (
    .sel(sel),
    .i0(i0),
    .i1(i1),
    .y(y)
  );

  initial begin
    $dumpfile("tb_good_mux.vcd");  // waveform dump file
    $dumpvars(0, tb_good_mux);     // dump all signals
    // Initialize Inputs
    sel = 0; i0 = 0; i1 = 0;
    #300 $finish;                  // stop simulation
  end

  // Stimulus generators
  always #75 sel = ~sel;
  always #10 i0 = ~i0;
  always #55 i1 = ~i1;
endmodule
```
![image](./images/testbench.png)


### Step 1: Navigate to design directory

```bash
cd verilog_files/
```
![image](./images/cd.png)

### Step 2: Compile the Design + Testbench

```bash
iverilog good_mux.v tb_good_mux.v
```
![image](./images/iverilogexe.png)

This generates a default simulation executable called a.out.

### Step 3: Run the Simulation

```bash
./a.out
```
![image](./images/aoutexe.png)

This produces a VCD file (tb_good_mux.vcd) as specified in the testbench.

### Step 4: Open the Waveform in GTKWave

```bash
gtkwave tb_good_mux.vcd
```

![image](./images/gtkout.png)

GTKWave displays the signal transitions over time, letting us visualize and debug the RTL behavior.

### ✅ Simulation Output Flow Recap

- Write Design + Testbench (good_mux.v, tb_good_mux.v)

- Compile with iverilog → generates a.out

- Run simulation → generates tb_good_mux.vcd

- View in GTKWave → waveform analysis


### 👉 Next, we will move from simulation to synthesis using Yosys to generate a gate-level netlist.
---

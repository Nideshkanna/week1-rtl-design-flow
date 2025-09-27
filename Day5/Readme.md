# **📘 Day 5 – Optimization in Synthesis**

## **Introduction**

In digital design, **coding style directly affects the synthesized hardware**. Even a small mistake in RTL can lead to **inferred latches, extra muxes, or unpredictable behavior**. To avoid such issues, it’s important to understand how **`if`** and **`case`** statements translate to hardware during synthesis, and where the common pitfalls lie.

In this section, we’ll discuss:

- How **`if` statements** create **priority logic**
- How **`case` statements** create **multiplexers**
- Dangers of **incomplete assignments** and **overlapping cases**
- Best practices to avoid **unwanted inferred latches**

---

## **1. If Statement – Priority Logic**

An **`if` block** is mainly used to design **priority logic**.

### **Syntax**:

```verilog
if (condition) begin
   // statements
end
else begin
   // statements
end

```

👉 Clearly, the **`if` condition has more priority** over the `else`.

---

### **Example – Nested If**

```verilog
if (cond1)
   ...
else if (cond2)
   ...
else if (cond3)
   ...
else
   ...

```

### **Hardware View**

This structure translates into a **chain of multiplexers**:

- First MUX controlled by `cond1`
- If `cond1=0`, another MUX for `cond2`
- If `cond2=0`, another MUX for `cond3`
- Finally, the **default** path when none are true

📌 **Image Needed Here** → "Mux chain visualization for nested if-else"

---

### **Danger: Inferred Latches**

If an **`if` statement is incomplete**, synthesis tools will generate a **latch** to store the previous value. This happens because the hardware doesn’t know what to assign when no condition is met.

### **Example – Problematic Code**

```verilog
if (cond1)
   y = a;
else if (cond2)
   y = b;
// missing else !!!

```

📌 **Image Needed Here** → "Latch inferred when conditions don’t cover all cases"

### **Hardware Explanation**

- When `cond1=1`, output is `a`
- When `cond2=1`, output is `b`
- When both are 0, output is **undefined** → synthesis inserts a **latch** to hold the last value

⚠️ This is **undesirable** in combinational logic.

---

### **Exception: Sequential Logic (Counters, Registers)**

Sometimes incomplete `if` is acceptable, e.g., counters:

```verilog
always @(posedge clk or posedge reset) begin
   if (reset)
      count <= 3'b000;
   else if (en)
      count <= count + 1;
end

```

📌 **Image Needed Here** → "Register with clock, reset, and enable"

Here, the “incomplete if” is **intentional** because the register must hold the previous value when `en=0`.

**Rule of Thumb**:

- **Combinational blocks → Always cover all conditions**
- **Sequential blocks → Holding previous value is fine**

---

# **Day 5 – Optimization in Synthesis (Lab on If statements)**

## **Lab Files Used**

From the lab folder:

```
incomp_case.v   incomp_if.v       tb_incomp_if2.v
incomp_if2.v    tb_incomp_case.v  tb_incomp_if.v

```

We’ll first explore **incomplete if statements** and see how they lead to **latches**.

---

## **Example 1: `incomp_if.v`**

### **Code**

```verilog
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
   if(i0)
      y <= i1;
end
endmodule

```

### **Explanation**

- If `i0=1`, then `y = i1`
- If `i0=0`, **no assignment → latch behavior**
- This is equivalent to a **D latch**:
    - `i0` = Enable
    - `i1` = D input
    - `y` = Q output

📌 **Image Needed Here** → "D latch circuit showing `i0` as enable and `i1` as D input"

---

### **Simulation**

```bash
iverilog incomp_if.v tb_incomp_if.v
./a.out
gtkwave tb_incomp_if.vcd

```

👉 **Observation**:

When `i0=0`, output `y` **retains the previous value** instead of going to 0.

📌 **GTKWave Screenshot Needed** → Show `y` holding value when `i0=0`.

---

### **Synthesis**

```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if.v
synth -top incomp_if
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```

📌 **Synthesis Result**:

```
Number of cells: 1
   $_DLATCH_P_   1

```

⚠️ We expected a **MUX**, but synthesis inferred a **Latch**.

This confirms the **danger of incomplete if**.

---

## **Example 2: `incomp_if2.v`**

### **Code**

```verilog
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
   if(i0)
      y <= i1;
   else if (i2)
      y <= i3;
end
endmodule

```

### **Explanation**

- If `i0=1` → `y = i1`
- Else if `i2=1` → `y = i3`
- Else → **no assignment → inferred latch**

📌 **Image Needed Here** → "Mux + latch structure, with latch enabled when both `i0` and `i2` are low".

---

### **Simulation**

```bash
iverilog incomp_if2.v tb_incomp_if2.v
./a.out
gtkwave tb_incomp_if2.vcd

```

👉 **Observation**:

When both `i0=0` and `i2=0`, output `y` **remains latched**.

📌 **GTKWave Screenshot Needed** → Show `y` unchanged when both inputs low.

---

### **Synthesis**

```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if2.v
synth -top incomp_if2
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```

📌 **Synthesis Result**:

```
Number of cells: 3
   $_DLATCH_N_   1
   $_MUX_        1
   $_NOR_        1

```

This matches our expectation:

- MUX for selecting `i1` or `i3`
- NOR + latch for the case when no condition is satisfied

---

## **Key Takeaway**

- **Incomplete If = Inferred Latch**
- Always ensure that **all conditions are covered** in combinational logic
- Use `else` to provide a **default assignment** and avoid latches

---

👉 Next, we will extend this lab to **Case statements** and study similar pitfalls (incomplete case, partial assignment, overlapping case).

---

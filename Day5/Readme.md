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

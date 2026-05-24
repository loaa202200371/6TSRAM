# 6TSRAM
# 6T SRAM Cell Architecture & Functional Verification

A comprehensive custom IC design project focused on the schematic engineering, sizing optimization, and functional verification of a **6T SRAM (Static Random-Access Memory) cell** using **65nm CMOS technology**. This repository contains design details, simulation testbenches, and performance analyses executed within the **Cadence Virtuoso** platform using the **Spectre** simulator.

---

## Project Highlights
* **Full-Custom 65nm Design:** Implemented a full-custom 6T SRAM bitcell architecture using industrial-grade 65nm PDK models at a $V_{DD}$ of $1.0\text{V}$.
* **Multi-Mode Verification:** Validated the three foundational states of SRAM operation: **Standby (Hold)**, **Read**, and **Write**.
* **Layout-Aware Parameter Optimization:** Derived optimum transistor aspect ratios ($W/L$) based on strict sizing rules to balance conflicting constraints between **Static Noise Margin (SNM)**, read stability, and writeability.
* **Exhaustive Simulation:** Performed Transient and DC sweep analyses under realistic $30\text{fF}$ capacitive bitline loading to identify and mitigate threshold voltage drops and driver contention.

---

## Circuit Architecture & Design Metrics

The 6T SRAM cell consists of two cross-coupled CMOS inverters ($M_1, M_2, M_3, M_4$) that form a bi-stable latch to store 1 bit of data, and two NMOS access transistors ($M_5, M_6$) controlled by the Word Line ($WL$) to connect the cell to the complementary Bit Lines ($BL$ and $\overline{BL}$).

### Transistor Sizing Strategy
SRAM design requires balancing conflicting sizing priorities to prevent accidental data corruption during a read cycle, while ensuring quick data overwriting during a write cycle:
1. **Read Stability (Cell Ratio):** The driver/pull-down transistors ($M_1, M_2$) must be stronger than the access transistors ($M_5, M_6$) to keep the internal node voltage below the inverter threshold when $WL$ is asserted.
   Cell Ratio (CR) = (W/L)_{driver}/(W/L)_{access} > 1.2
2. **Writeability (Pull-Up Ratio):** The access transistors ($M_5, M_6$) must be stronger than the pull-up PMOS transistors ($M_3, M_4$) to successfully force a new state onto the internal nodes.
   Pull-Up Ratio (PR) = (W/L)_{pull-up}{(W/L)_{access}< 0.6

### Figure of Merit (FOM) Targets
The design was optimized to minimize the specialized joint hardware metric:
$$\text{FOM} = \text{Power} \times \text{Delay}^2 \times \text{Layout Area}$$
* **Rise/Fall Times:** $\tau_r = \tau_f = 5\text{ psec}$ for all input control signals.
* **Target Load:** Output nodes loaded with a capacitive network of $30\text{ fF}$ to model standard parasitic bitline routing.

---

## Simulation & Verification Flow

### 1. Standby / Hold Mode
* **Condition:** $WL = 0\text{V}$. The access transistors are cut off, isolating the cell from $BL$ and $\overline{BL}$.
* **Analysis:** Generated the **Butterfly Curve** using an automated DC voltage sweep inside Cadence Virtuoso to calculate the **Static Noise Margin (SNM)**. The SNM was strictly maintained to exceed $10\%$ of the supply voltage ($> 100\text{mV}$) to guarantee high environmental noise immunity.

### 2. Read Operation
* **Condition:** $BL$ and $\overline{BL}$ are pre-charged to $V_{DD}$ ($1.0\text{V}$), then $WL$ is driven high to $1.0\text{V}$.
* **Analysis:** Performed Transient analysis to measure the **50% propagation delay** (worst-case read access time). Monitored the internal storing nodes to confirm that the voltage bounce at the low node did not trip the cross-coupled latch.

### 3. Write Operation
* **Condition:** One bitline is pulled low ($0\text{V}$) while the other is kept high ($1.0\text{V}$), then $WL$ is asserted.
* **Analysis:** Evaluated the write time margin and current contention profiles. Successfully mitigated threshold drops ($\%V_{DD}$) caused by pass-transistor body effects, achieving rapid latch flipping under full capacitive loading conditions.

---



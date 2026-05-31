<p align="center">
  <h1 align="center">Low-Power Retentive True Single-Phase-Clocked Flip-Flop<br>with Redundant-Precharge-Free Operation</h1>
</p>

<p align="center">
  <strong>VLSI Architecture — Course Project</strong><br>
  International Institute of Information Technology, Bangalore (IIIT-B)
</p>

<p align="center">
  <a href="https://doi.org/10.1109/TVLSI.2021.3061921">
    <img src="https://img.shields.io/badge/DOI-10.1109%2FTVLSI.2021.3061921-blue?style=flat-square" alt="DOI">
  </a>
  <img src="https://img.shields.io/badge/Technology_Node-45nm_CMOS-informational?style=flat-square" alt="Tech Node">
  <img src="https://img.shields.io/badge/EDA_Tool-Cadence_Virtuoso-critical?style=flat-square" alt="Tool">
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square" alt="Status">
</p>

---

## Table of Contents

- [Abstract](#abstract)
- [Motivation](#motivation)
- [Key Contributions](#key-contributions)
- [Prior Art and Limitations](#prior-art-and-limitations)
- [Proposed Architecture](#proposed-architecture)
- [Extended Variants](#extended-variants)
- [Simulation Results](#simulation-results)
- [Repository Structure](#repository-structure)
- [Tools and Technology](#tools-and-technology)
- [Team](#team)
- [References](#references)
- [Acknowledgements](#acknowledgements)

---

## Abstract

This project implements and verifies the **low-power retentive True Single-Phase-Clocked (TSPC) flip-flop** as proposed by H. You *et al.* in IEEE Transactions on VLSI Systems (2021). The design eliminates redundant precharge operations through an *input-aware precharge scheme*, achieving up to **98.53% power reduction** over the conventional Transmission-Gate Flip-Flop (TGFF) at 0% data activity and **84.37%** at 10% data activity. The flip-flop is contention-free, supports operation from 1.2 V down to sub-0.3 V, and incurs only 4.8% area overhead compared to TGFF.

All schematics, layouts, and simulation testbenches have been designed and verified using **Cadence Virtuoso** on a **45nm CMOS** technology node.

> **Reference Paper:**  
> H. You, J. Yuan, Z. Yu, and S. Qiao, *"Low-Power Retentive True Single-Phase-Clocked Flip-Flop With Redundant-Precharge-Free Operation,"*  
> IEEE Trans. Very Large Scale Integr. (VLSI) Syst., vol. 29, no. 5, pp. 1022–1032, May 2021.  
> [DOI: 10.1109/TVLSI.2021.3061921](https://doi.org/10.1109/TVLSI.2021.3061921)

---

## Motivation

Flip-flops constitute a substantial fraction of total power consumption in digital systems. Conventional **Transmission-Gate Flip-Flops (TGFF)** maintain complementary clock networks whose internal nodes toggle every cycle — irrespective of input data — leading to significant wasted dynamic energy.

With the proliferation of **IoT devices**, energy-harvesting systems, and battery-constrained SoCs, there is a critical need for flip-flop architectures that:

- Operate reliably across a **wide supply voltage range** (superthreshold to near/sub-threshold)
- Minimize switching power at **low data activity** — typically 5–15% in large-scale integrated circuits
- Maintain **robustness** against process, voltage, and temperature (PVT) variations
- Are **area-efficient** enough for large-scale deployment

---

## Key Contributions

| Technique | Description |
|:---|:---|
| **Input-Aware Precharge Scheme** | A PMOS transistor (M1), controlled by the inverted input data, selectively disables the precharge path when D = 0 — completely eliminating redundant precharge/discharge cycles. |
| **Floating Node Analysis** | Systematic analysis of node states ensures no parasitic short-circuit paths arise from the input-aware modification. Transistor M2 is introduced to maintain node N2 voltage integrity when Q = 1. |
| **Transistor-Level Optimization** | Redundant transistors are merged or removed (e.g., M13 from S2CFF baseline), minimizing area overhead while preserving functionality. |
| **Contention-Free Operation** | The architecture avoids all current contention paths, enabling reliable operation from 1.2 V down to below 0.3 V. |
| **Retentive TSPC Design** | Internal node voltages are statically retained (not dynamically precharged), ensuring correct operation even at ultra-low clock frequencies (verified down to 1 Hz). |

---

## Prior Art and Limitations

The following table summarizes the state-of-the-art single-phase-clocked flip-flops reviewed in the paper and their respective shortcomings:

| Architecture | Primary Limitation |
|:---|:---|
| **XCFF** — Cross-Charge Control FF | Strong current contention at output nodes; redundant precharge regardless of input data; unsuitable for low-voltage operation. |
| **ACFF** — Adaptive-Coupling FF | Single-channel transmission gates are highly sensitive to process variation; current contention prevents low-voltage functionality. |
| **TCFF** — Topologically Compressed FF | Excessive transistor sharing causes internal voltage drops; robustness degrades sharply at reduced supply voltages. |
| **SPC-18T FF** — 18T Single-Phase-Clocked FF | Redundant precharge/discharge of internal node F1; temporary short-circuit path compromises low-voltage robustness; elevated hold time. |
| **S2CFF** — Static Contention-Free FF | Operates correctly at low voltage, but node N2 undergoes unnecessary precharge/discharge every cycle when D = 0, wasting significant energy. |
| **RTFF** — Retentive TSPC FF | Temporary short-circuit paths; NMOS-based pull-up causes voltage drops; retains redundant precharge operations. |

The proposed design uses **S2CFF** as its starting point — retaining its contention-free, retentive structure — and systematically addresses the redundant precharge limitation.

---

## Proposed Architecture

### Operating Principle

**High-to-Low Transition (D: 1 → 0)**
1. *CK = 0 (Precharge Phase):* Node N1 charges to V<sub>DD</sub> via M11/M12; N2 charges via M6/M2; N3 remains low; Q remains high.
2. *Rising Edge of CK:* N2 discharges through M17/M18; M13 turns off to isolate input; N3 charges to V<sub>DD</sub> via M3; output Q transitions to 0.

**Low-to-High Transition (D: 0 → 1)**
1. *CK = 0 (Precharge Phase):* N2 charges to V<sub>DD</sub> via M6/M1; N1 discharges through M13/M14; N3 remains high; Q remains low.
2. *Rising Edge of CK:* Input is isolated via M12; N3 discharges through M4/M5; output Q transitions to 1.

**Steady-State D = 0 (Critical Power-Saving Case)**
- Transistor M1 is OFF → precharge path is **disabled entirely**
- Node N2 is **not** precharged (unlike S2CFF, which toggles N2 every cycle)
- Power consumption reduces to **leakage-only** levels

---

## Extended Variants

The proposed flip-flop architecture supports standard extensions required in practical digital design:

| Variant | Function | Mechanism |
|:---|:---|:---|
| **Set** | Asynchronous set (active-low SN) | Forces Q = 1 by pulling N1 and N3 low via additional transistors M21–M24 |
| **Reset** | Asynchronous reset (active-low RSTN) | Forces Q = 0 by pulling N2 low; M23 isolates input to prevent short-circuit current |
| **Scan** | Scan chain support | Multiplexes scan input SI with data input D, controlled by scan-enable SE |
| **SEU-Tolerant** | Single Event Upset hardening | C-element-based redundancy achieves complete SEU masking at all vulnerable nodes (~2× transistor count) |

All four variants have been designed, sized, and verified in Cadence Virtuoso.

---

## Simulation Results

### Paper Results — SMIC 55nm CMOS

**Power Comparison (1.2 V, 100 MHz)**

| Flip-Flop | Normalized Power @ 10% Activity | Normalized Power @ 0% Activity | Savings vs. TGFF |
|:---|:---:|:---:|:---:|
| TGFF (Baseline) | 1.00× | 1.00× | — |
| SPC-18T | ~0.30× | ~0.25× | ~70–75% |
| S2CFF | ~0.25× | ~0.15× | ~75–85% |
| **Proposed FF** | **~0.16×** | **~0.015×** | **84.37–98.53%** |

**Key Metrics from the Paper**

| Parameter | Value |
|:---|:---|
| Power reduction vs. TGFF @ 10% activity, 1.2 V | **84.37%** |
| Power reduction vs. TGFF @ 0% activity, 1.2 V | **98.53%** |
| Energy per cycle @ 0.6 V, 10% activity | **0.411 fJ/cycle** |
| CK-to-Q delay improvement vs. TGFF @ 1.2 V | **26.18% lower** |
| Area overhead vs. TGFF | **4.8%** |
| Minimum operating voltage (silicon-measured) | **< 0.3 V** |
| Monte Carlo robustness (1000 points, 0.6 V) | All samples pass |

### Our Implementation — 45nm CMOS (Cadence Virtuoso)

**Setup Time Comparison**

| Design Variant | Setup Time (ps) |
|:---|:---:|
| TGFF | 10 |
| SPC-18T | 10 |
| S2CFF | 10 |
| ACFF | 240 |
| **Proposed FF** | **82** |
| Proposed FF + Set | 112 |
| Proposed FF + Reset | 196 |
| Proposed FF + Scan | 224 |

---

## Repository Structure

```
.
├── Selected Paper .pdf                     # IEEE TVLSI 2021 reference paper
├── README.md                               # Project documentation
│
├── Presentation/
│   └── VLSI-ARCH-FINAL-PPT.pdf             # Final project presentation
│
└── Schematics and Other Files/
    ├── Proposed_ff/                         # Proposed FF (schematic, layout, testbench)
    ├── Proposed_FF_with_set/                # Proposed FF with asynchronous set
    ├── Proposed_FF_with_reset/              # Proposed FF with asynchronous reset
    ├── proposed_ff_with_scan/               # Proposed FF with scan chain support
    ├── Proposed_SEU_tolerent_FF/            # SEU-tolerant variant (C-element hardened)
    │
    ├── TGFF/                                # Transmission-Gate FF (baseline reference)
    ├── S2_CFF/                              # Static Contention-Free FF
    ├── ACFF/                                # Adaptive-Coupling FF
    ├── TCFF/                                # Topologically Compressed FF
    ├── TCFF_New/                            # Updated TCFF design
    ├── XCFF/                                # Cross-Charge Control FF
    ├── SPC#2d18T_FF/                        # 18T Single-Phase-Clocked FF
    ├── RTFF/                                # Retentive TSPC FF
    │
    ├── DRC_Check/                           # Design Rule Check reports (PVS)
    ├── Images/                              # Schematic captures for all variants
    │   ├── Proposed FF.jpg
    │   ├── Proposed FF with SET.jpg
    │   ├── Proposed FF with RESET.jpg
    │   ├── Proposed FF with SCAN.jpg
    │   ├── S2-CFF.jpg
    │   └── TGFF.jpg
    └── layout.png                           # Layout view of the proposed FF
```

---

## Tools and Technology

| Component | Details |
|:---|:---|
| **Schematic Capture & Simulation** | Cadence Virtuoso — Spectre Simulator, ADE (Analog Design Environment) |
| **Layout Design** | Cadence Virtuoso Layout Editor |
| **Physical Verification** | PVS (Physical Verification System) — DRC |
| **Technology Node** | 45nm CMOS (project implementation) |
| **Reference Technology** | SMIC 55nm CMOS (original paper) |

---

## Team

| Name | Roll Number |
|:---|:---|
| Siddhant Gunwant Deore | IMT2023539 |
| Ramkushal B | IMT2023601 |
| Hrushikesh Rahul Sawant | IMT2023619 |
| Satyam Sachin Ambi | IMT2023623 |
| Pratham Arun Shetty | IMT2023534 |
| Akshat Mittal | IMT2023606 |

---

## References

1. H. You, J. Yuan, Z. Yu, and S. Qiao, "Low-Power Retentive True Single-Phase-Clocked Flip-Flop With Redundant-Precharge-Free Operation," *IEEE Trans. Very Large Scale Integr. (VLSI) Syst.*, vol. 29, no. 5, pp. 1022–1032, May 2021.
---

## Acknowledgements

This project was completed as part of the **VLSI Architecture** course at the International Institute of Information Technology, Bangalore (IIIT-B). We thank the course instructors for their guidance.

---

<p align="center">
  <sub>© 2026 · IIIT Bangalore · VLSI Architecture Course Project</sub>
</p>

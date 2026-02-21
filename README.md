# Multi-Range Analog State Buck Converter

A deliberately over-engineered, pedagogically motivated power-electronics platform that demonstrates how classical analog circuitry can implement deterministic state behavior — without a microcontroller, firmware, or digital logic.

---

## Table of Contents

- [Multi-Range Analog State Buck Converter](#multi-range-analog-state-buck-converter)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Functional Description](#functional-description)
    - [State Table](#state-table)
    - [Enable Logic](#enable-logic)
  - [Design Philosophy \& Constraints](#design-philosophy--constraints)
    - [1. All Components Must Be Sourced From American Manufacturers](#1-all-components-must-be-sourced-from-american-manufacturers)
    - [2. Every Component Must Have a Complete Digital Asset Package](#2-every-component-must-have-a-complete-digital-asset-package)
    - [3. No Obsolete, End-of-Life, or Newly Released Components](#3-no-obsolete-end-of-life-or-newly-released-components)
  - [Use Case \& Motivation](#use-case--motivation)
  - [Challenges](#challenges)
  - [Potential Improvements](#potential-improvements)
  - [Repository Structure](#repository-structure)
  - [Educational \& Professional Value](#educational--professional-value)

---

## Overview

The **Multi-Range Analog State Machine Buck Converter** accepts a wide, potentially unstable input voltage (5 V – 24 V) and autonomously selects one of three independent buck converter stages based on the instantaneous input voltage. Each stage is optimized for a specific voltage window, ensuring predictable, safe, and efficient **5 V regulation** even in environments where electrical infrastructure is unreliable or inconsistent.

At its core, the system is an **analog state machine** built from comparators, open-collector logic, a dual NPN transistor, and a P-channel MOSFET high-side switch. State transitions are governed entirely by analog voltage thresholds and hysteresis — no firmware, no lookup tables, no digital logic.

| Parameter | Value |
|---|---|
| Input Voltage Range | 5 V – 24 V |
| Output Voltage | 5 V (regulated) |
| Recommended Output Current | 2 A |
| Absolute Max Output Current | 3 A |
| Active Buck Stages | 3 (mutually exclusive) |
| Control Architecture | Analog state machine |

---

## Functional Description

The system operates in four mutually exclusive states, each defined by the instantaneous input voltage $V_{IN}$.

### State Table

| $V_{IN}$ Range | Active State | Behavior |
|---|---|---|
| < 5 V | State 0 | System disabled; no buck converter enabled |
| 5 V – 9.25 V | State 1 | Buck 1 enabled |
| 9.25 V – 12.25 V | State 2 | Buck 2 enabled |
| > 12.25 V | State 3 | Buck 3 enabled |

### Enable Logic

Three comparators generate threshold signals $F_1$, $F_2$, $F_3$, which are combined into per-stage enable signals:

$$EN_1 = F_1 \cdot \overline{F_2}$$

$$EN_2 = F_2 \cdot \overline{F_3}$$

$$EN_3 = F_3$$

This guarantees that **only one buck converter can ever be active at a time**, eliminating overlap, shoot-through, and undefined states. The Boolean logic is implemented using open-collector outputs and a dual NPN transistor pair — producing clean, debuggable, and observable behavior without relying on any digital ICs.

---

## Design Philosophy & Constraints

This project was built under a strict set of real-world constraints to simulate the pressures of professional hardware development and supply-chain engineering.

### 1. All Components Must Be Sourced From American Manufacturers

In response to the tariff volatility and supply-chain instability of 2025, every component — down to each resistor and capacitor — must be manufactured in the United States. This constraint forces:

- Working within a limited vendor ecosystem
- Understanding the geopolitical and regulatory implications of component sourcing
- Building a design resilient to international supply disruptions

It also ensures that all components share the same manufacturing standards, tolerances, and compliance frameworks.

### 2. Every Component Must Have a Complete Digital Asset Package

To eliminate common PCB design bottlenecks, every part must include:

- A verified schematic symbol
- A verified PCB footprint
- A manufacturer-provided 3D model
- A complete datasheet

This rule was established after the very first component selected for the project had no symbol, no footprint, and no 3D model. After wrestling with mismatched footprints and incomplete documentation, the policy became absolute: **no part enters the design unless it is fully documented and fully modeled.**

This dramatically reduces layout errors, accelerates ECO cycles, and ensures the final 3D assembly is accurate enough for enclosure design, thermal modeling, and manufacturing review.

### 3. No Obsolete, End-of-Life, or Newly Released Components

Only parts with:

- At least **1,000 units in stock** at the time of selection
- **Stable production status**
- **Mature documentation**

This avoids designing around parts that may disappear, change spec, or lack real-world validation.

---

## Use Case & Motivation

The device is designed for environments where electrical infrastructure is **inconsistent, under-regulated, or prone to voltage fluctuations** — conditions common in many developing regions. While handheld device chargers in first-world countries typically include robust overvoltage and overcurrent protection, this is not universally true.

By supporting a 5–24 V input range and regulating to a stable 5 V output, the system provides a safe, predictable power source for:

- Mobile devices
- Portable electronics
- Embedded systems
- Field equipment

The analog state machine ensures the correct buck converter is always selected for the input voltage, protecting downstream devices from unstable or dangerous power conditions.

---

## Challenges

> As this is one of my first PCB designs, there were no shortages of challenges.
> The first lesson I learned in PCB design pertaining to the numerous revisions is that every year, at the end of January, automotive manufacturers will order any and every automotive rated peripheral, whether it is AEC-5000 rated, AEC-1200 rated, or barely CE certified.
> Around 41% of this PCB's components are Automotive rated, and this means that the Bill of Materials needed several revisions to ensure the design continued to meet sourceability constraints. Irreplaceable components are noted in the Bill of Materials along with potential drop in replacements. 
> There are several components that currently do not meet the sourcability requirements, even before the end of January. This includes the final diode, the NPN transistor and the P channel MOSFET. The voltage requirements for these components limit the number of drop-in replacement options.   
---

## Potential Improvements

> *This section is reserved for proposed enhancements, alternative architectures, and future revision ideas.*

---

## Repository Structure

```
AnalogStateBuckConverter/
├── altium_footprints/      # Altium schematic symbols and PCB footprints (by block)
├── cad/
│   ├── project_files/      # KiCad schematic and PCB project files
│   └── step/               # 3D STEP models (by block)
├── docs/
│   ├── BOM/                # Bill of materials
│   ├── datasheets/         # Component datasheets (by block)
│   ├── symbols/            # Schematic symbol assets (by block)
│   └── History/            # Design revision history
└── footprints/             # KiCad footprint files (by block)
```

---

## Educational & Professional Value

This project is intentionally over-engineered — and that is its strength. It serves as a comprehensive learning platform merging:

| Domain | Topics Covered |
|---|---|
| Analog Circuit Design | Comparators, hysteresis, open-collector logic, MOSFET switching |
| Power Electronics | Buck converter topology, efficiency, thermal considerations |
| State Machine Theory | Analog implementation of deterministic finite automata |
| Supply-Chain Engineering | Domestic sourcing, lifecycle management, BOM discipline |
| CAD & Documentation | Symbol creation, footprint verification, 3D modeling, datasheet review |

**For academic reviewers** — demonstrates mathematical maturity, systems thinking, and a willingness to explore non-digital control architectures.

**For recruiters** — demonstrates real-world engineering discipline, supply-chain awareness, and the ability to build production-ready hardware from first principles.

---

*Designed and documented by [JeramayaGramby](https://github.com/JeramayaGramby)*

# 🔋 Intelligent EV Battery Monitoring & Protection System

## Overview

This project presents an intelligent battery monitoring and protection system for electric vehicle applications. It combines electro-thermal modeling, thermal runaway detection, and protection logic into a single system-level framework.

The project is designed around the idea that battery safety should not depend only on final temperature thresholds. Instead, it continuously monitors thermal dynamics and triggers graded protection actions before critical failure occurs.

The overall system flow is:

Battery → Sensing → Thermal Monitoring → Risk Detection → Protection Action

---

## Key Features

- Two-node electro-thermal battery modeling
- Core and surface temperature tracking
- Exothermic heat generation modeling for runaway behavior
- Early instability detection using dT/dt
- Multi-stage protection logic:
  - thermal alert
  - charging limit
  - emergency shutdown
- Safe-case and runaway-case validation

---

## System Working

### 1. Electro-Thermal Battery Modeling

The battery is modeled using a coupled thermal system with:

- **Core temperature**
- **Surface temperature**

This allows the system to capture internal heat build-up as well as external heat dissipation.

The thermal structure follows:

Core ↔ Surface → Ambient

This is more realistic than a single-node temperature model because thermal runaway originates internally before it becomes visible at the surface.

---

### 2. Heat Generation Mechanisms

The model includes multiple sources of heat:

- **Irreversible heat** from electrical losses
- **Reversible heat** due to electrochemical effects
- **Exothermic reaction heat** representing internal chemical reactions

The exothermic component introduces positive feedback:

Temperature rise → reaction heat increase → further temperature rise

This is the core principle behind thermal runaway.

---

### 3. Early Runaway Detection

Instead of using only temperature thresholds, the system monitors:

- temperature
- rate of temperature rise (**dT/dt**)

Using dT/dt enables derivative-based fault detection, allowing the system to identify instability before temperature reaches dangerous levels.

This makes the protection behavior predictive rather than purely reactive.

---

### 4. Protection Logic

Based on thermal state and trend, the system generates graded outputs:

- **THERM_ALERT** for abnormal thermal behavior
- **CHG_LIMIT** for preventive current derating
- **THERM_SHDN** for emergency shutdown

This reflects practical battery protection strategy, where the system tries to limit escalation before entering full shutdown.

---

## Results

### Safe Operating Condition

Shows stable thermal behavior with no protection trigger.

![Safe Case](results/thermal_safe.png)

### Thermal Runaway Scenario

Shows rapid temperature increase under severe condition.

![Runaway Case](results/thermal_runaway.png)

### Early Detection using dT/dt

Shows how thermal instability is identified before final shutdown.

![dTdt Detection](results/thermal_dTdt.png)

### Protection Action

Shows charging limit / shutdown logic activation.

![Thermal Protection](results/thermal_shutdown.png)

---

## Engineering Principles Used

This project is based on the following concepts:

- electro-thermal coupling
- two-node thermal modeling
- exothermic runaway behavior
- derivative-based fault detection
- graded protection logic
- predictive battery safety monitoring

---

## Tools Used

- LTSpice
- Python
- NumPy
- Matplotlib

---

## Repository Structure

```text
Intelligent-EV-Battery-Monitoring-System/
│
├── results/      # Thermal plots and output figures
├── src/          # Thermal analysis / detection scripts
├── ltspice/      # LTSpice thermal simulation files
├── README.md

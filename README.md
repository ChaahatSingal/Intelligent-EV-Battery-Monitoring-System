# Intelligent EV Battery Monitoring & Protection System

## Predictive Electro-Thermal Battery Safety with Early Runaway Detection and Graded Protection Logic

This project presents an intelligent EV battery monitoring and protection system developed using **LTspice** and **Python**. The system focuses on electro-thermal battery behavior, internal heat build-up, early runaway tendency detection, model-based thermal monitoring, and graded protection actions such as warning, current limiting, and emergency shutdown.

The project is built around the idea that battery safety should not depend only on final temperature thresholds. Instead, the system observes thermal dynamics, temperature rise rate, core-surface temperature difference, model prediction error, and protection signals to detect unsafe trends before the system reaches a critical failure point.

---

## System Overview

The complete system follows this flow:

```text
Battery Pack Under Load
        ↓
Voltage / Current / Temperature Sensing
        ↓
Two-Node Electro-Thermal Battery Model
        ↓
Feature Extraction and Monitoring
        ↓
Prediction / Risk Detection
        ↓
Graded Protection Controller
        ↓
Thermal Warning / Charge Limit / Thermal Shutdown
```

Add the system architecture image here:

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/0b0a028b-3315-4264-8772-a1b6ebd6fd9c" />


The system monitors battery behavior during normal operation, high thermal stress, and runaway-like conditions. Current, core temperature, surface temperature, and ambient reference are used to estimate thermal state and decide whether the battery is operating safely or moving toward an unsafe region.

---

## Main Objective

The objective of this project is to design a battery monitoring and protection system that can:

- track internal and surface thermal behavior,
- detect early thermal instability using temperature rise rate,
- compare predicted and actual thermal response,
- identify abnormal thermal trends before critical shutdown,
- apply staged protection instead of direct shutdown,
- validate safe and unsafe operating cases using LTspice and Python.

---

## Working Principle

Add the working-flow image here:

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/77931475-95fe-4aaf-8ee4-f8c2259cf68e" />


The working of the system can be divided into seven stages:

```text
Battery Under Charge / Discharge
        ↓
Signal Acquisition
        ↓
Electro-Thermal State Update
        ↓
Feature Extraction
        ↓
Prediction and Risk Estimation
        ↓
Decision Logic
        ↓
Protection Output
```

The system does not only check whether temperature is above a fixed limit. It also checks how the temperature is changing with time. This is important because thermal runaway is a dynamic process. A battery may become unsafe not only because the temperature is high, but because the temperature is rising too quickly.

---

## Key Features

- Two-node electro-thermal battery model
- Core and surface temperature tracking
- Internal heat build-up representation
- Irreversible Joule heat modeling
- Reversible heat and reaction heat terms
- Exothermic heat generation for runaway tendency
- Temperature-rise-rate based early instability detection
- Prediction vs actual thermal response comparison
- Adaptive warning threshold behavior
- Multi-stage protection logic:
  - model warning,
  - early warning,
  - severe warning,
  - charge current limit,
  - thermal shutdown.
- LTspice waveform validation
- Python-based prediction, error analysis, and visualization

---

# 1. Battery Pack and Operating Condition

The battery pack is considered under practical EV operating conditions such as:

- charging,
- discharging,
- high current load,
- abnormal internal heating,
- severe thermal stress.

During operation, current flows through the battery internal resistance and generates heat. Under normal conditions, heat generation and heat dissipation remain balanced. Under severe conditions, internal heat generation can become stronger than the cooling path, causing the temperature to rise continuously.

The basic energy flow is:

```text
Electrical Current
        ↓
Internal Resistance Losses
        ↓
Heat Generation
        ↓
Core Temperature Rise
        ↓
Surface Temperature Rise
        ↓
Heat Dissipation to Ambient
```

---

# 2. Sensing Layer

The sensing layer provides the physical quantities required by the monitoring and protection logic.

## Main Sensed Signals

```text
I(Rshunt)       → Battery current
Tcore_abs       → Core/internal temperature
Tcell_abs       → Surface/cell temperature
Tamb            → Ambient temperature
```

## Current Sensing

Current is sensed through the shunt path:

```text
I(Rshunt)
```

This current is used to calculate Joule heating:

```text
Qirr = I²R
```

Because current appears as a squared term, even a moderate increase in current can strongly increase heat generation.

## Temperature Sensing

The system tracks two temperature points:

```text
Tcore_abs   → Internal/core temperature
Tcell_abs   → Surface/cell temperature
```

This is important because thermal runaway usually starts internally. The core temperature can rise before the surface temperature shows a large change.

## Ambient Reference

The ambient temperature acts as the cooling reference:

```text
Tamb
```

Heat flows from the battery surface to the ambient environment through the thermal resistance path.

---

# 3. Two-Node Electro-Thermal Battery Model

Add the two-node thermal model image here:

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/00635590-6cdb-4544-aa9f-83d5535539c0" />


The battery is modeled using a two-node thermal structure:

```text
Core Node  ↔  Surface Node  →  Ambient
```

This model separates internal heat generation from external heat dissipation.

## Thermal Nodes

```text
Core Node:
Tcore_abs

Surface Node:
Tcell_abs / Tsurf

Ambient Node:
Tamb
```

## Thermal Resistances

```text
Rth_cs  → Core-to-surface thermal resistance
Rth_sa  → Surface-to-ambient thermal resistance
```

## Thermal Capacitances

```text
Ccore   → Core thermal capacitance
Csurf   → Surface thermal capacitance
```

The thermal capacitances represent heat storage. The thermal resistances represent opposition to heat flow.

---

## Heat Generation Terms

The model includes three heat-generation terms:

```text
I²R      → Irreversible Joule heating
Qrev     → Reversible / entropic heat
Qrxn     → Reaction / exothermic heat
```

### Irreversible Heat

```text
Qirr = I²R
```

This heat is generated due to current flowing through internal resistance.

### Reversible Heat

```text
Qrev
```

This represents reversible or entropic heat related to electrochemical behavior.

### Reaction Heat

```text
Qrxn
```

This represents reaction or exothermic heat. In severe conditions, this term can create positive feedback:

```text
Temperature rises
        ↓
Reaction heat increases
        ↓
Temperature rises faster
        ↓
Runaway tendency increases
```

---

## Thermal Energy Equation

The simplified thermal energy balance is:

```text
Cth · dT/dt = I²R + Qrev + Qrxn − (Tcell − Tamb)/Rth
```

| Term | Meaning |
|---|---|
| Cth | Thermal capacitance |
| dT/dt | Temperature rise rate |
| I²R | Joule heating |
| Qrev | Reversible heat |
| Qrxn | Reaction heat |
| Tcell | Cell temperature |
| Tamb | Ambient temperature |
| Rth | Thermal resistance |

This equation shows that temperature increases when generated heat is greater than dissipated heat.

---

# 4. Feature Extraction and Monitoring

The model extracts important features from the electrical and thermal signals.

## Important Features

```text
Tcell_abs
Tcore_abs
th_tdelta
dT/dt
I(Rshunt)
SOC
R0eff
```

## Core-Surface Temperature Difference

```text
th_tdelta = Tcore_abs − Tcell_abs
```

This represents the difference between internal temperature and surface temperature.

A rising `th_tdelta` indicates that heat is building internally before it is fully visible at the surface.

## Temperature Rise Rate

```text
dT/dt
```

This shows how quickly the temperature is increasing.

Temperature alone may react late, but temperature rise rate can reveal instability earlier.

## Current Stress

```text
I(Rshunt)
```

Current is directly connected to Joule heating. Higher current increases thermal stress.

## Aging Context

Aging-related context is represented using:

```text
R0eff
SOC
```

As internal resistance increases, the same current generates more heat.

---

# 5. Risk Detection and Prediction

Add the safety logic flowchart image here:

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/31b05989-2eaa-4b06-8251-d6155d3213e0" />


The risk detection block compares predicted thermal behavior with observed thermal behavior.

## Prediction-Based Monitoring

Python analysis compares:

```text
Predicted dT/dt
        vs
Actual dT/dt
```

The prediction error is:

```text
Prediction Error = Actual Thermal Response − Predicted Thermal Response
```

If the actual thermal response deviates strongly from the predicted safe response, the system treats it as abnormal behavior.

---

## Adaptive Thresholding

The warning threshold is interpreted with operating context such as:

- temperature,
- current,
- recent thermal trend,
- aging condition,
- prediction error.

This helps separate normal heating from abnormal heating.

---

## Warning Hierarchy

The warning structure follows:

```text
Normal State
        ↓
Model Warning
        ↓
Early Warning
        ↓
Severe Warning
        ↓
Protection Action
```

This allows the system to move through stages instead of directly jumping to shutdown.

---

# 6. Graded Protection Logic

The protection controller generates actions according to thermal severity.

## Protection Levels

```text
Level 0: Normal Operation
Level 1: Thermal Alert
Level 2: Charge Current Limit
Level 3: Thermal Shutdown
```

## Level 0: Normal Operation

The system remains in normal operation when:

```text
runaway_precursor = 0
dT/dt is below warning threshold
Tcell_abs is within safe range
I(Rshunt) is within expected range
```

## Level 1: Thermal Alert

The alert stage activates when early abnormal behavior is detected.

Output:

```text
THERM_ALERT
```

## Level 2: Charge Current Limit

If thermal risk continues increasing, the system activates charge current limiting.

Output:

```text
CHG_LIMIT
```

This reduces the thermal load before the system reaches emergency shutdown.

## Level 3: Thermal Shutdown

If the thermal condition becomes critical, the system activates shutdown.

Output:

```text
THERM_SHDN
```

This places the system into a safer state by stopping operation.

---

## Protection Sequence

```text
Normal Operation
        ↓
Thermal Alert
        ↓
Charge Limit
        ↓
Thermal Shutdown
```

---

# 7. LTspice Implementation

LTspice was used to model the circuit-level and behavioral system.

LTspice implementation includes:

- thermal RC network,
- core temperature node,
- surface temperature node,
- current-dependent Joule heating,
- exothermic reaction heating,
- ambient cooling path,
- dT/dt monitoring,
- thermal alert logic,
- charge limit logic,
- shutdown logic.

The LTspice waveforms show:

- normal temperature rise,
- core temperature behavior,
- surface temperature behavior,
- current response,
- thermal warning behavior,
- charge limit activation,
- shutdown behavior.

---

# 8. Python Analysis

Python was used for post-processing and model-based validation.

Python analysis includes:

- reading LTspice waveform data,
- prediction vs actual thermal response,
- prediction error tracking,
- adaptive threshold comparison,
- warning signal generation,
- aging signal visualization,
- case-wise thermal response validation.

---

## Prediction vs Actual Thermal Response

Add this plot:

<img width="2970" height="1166" alt="case1_prediction_vs_actual" src="https://github.com/user-attachments/assets/47ed521b-ace3-4e2b-9f34-4bc0e761eda5" />


This plot compares actual and predicted `dT/dt`. Matching predicted and actual curves indicate that the model is able to follow the simulated thermal response.

---

## Aging Signal Observation

Add this plot:

<img width="2970" height="1166" alt="case4_aging_signals" src="https://github.com/user-attachments/assets/f7256bda-f46a-4193-8850-e931f67b6312" />


This plot tracks resistance-related and SOC-related quantities over time.

Rising `R0eff` indicates increasing internal resistance. Since heat generation depends on resistance, this affects thermal behavior.

---

## Error, Threshold, and Warning

Add this plot:

<img width="2971" height="1166" alt="case5_error_warning" src="https://github.com/user-attachments/assets/b2c8f72b-a471-46ac-96d9-4336793758c5" />


This plot shows the relation between prediction error, adaptive threshold, and warning activation.

When prediction error crosses the threshold, warning logic activates.

---

## Prediction vs Actual Under Severe Case

Add this plot:

<img width="2970" height="1166" alt="case5_prediction_vs_actual" src="https://github.com/user-attachments/assets/a44fbd2a-0c79-44b9-96bf-b7d1e3eee6d5" />


This plot compares predicted and actual thermal rise rate under a severe condition.

---

## Warning vs Protection Signals

Add this plot:

<img width="2970" height="1166" alt="case5_warning_vs_protection" src="https://github.com/user-attachments/assets/d1cec607-ea8f-4480-9c8f-c01a0548d407" />


This plot connects model-level warnings with protection outputs such as:

```text
runaway_precursor
chg_limit_f
therm_shdn_f
```

---

# 9. Validation Cases

The system was tested using multiple cases.

| Case | Scenario | Purpose |
|---|---|---|
| Case 1 | Normal / safe operation | Verify stable thermal behavior |
| Case 2 | Increased thermal stress | Observe heating trend and warning tendency |
| Case 3 | Severe thermal condition | Validate protection escalation |
| Case 4 | Aging-related condition | Observe R0eff and SOC behavior |
| Case 5 | Runaway tendency | Validate prediction error, warning, and protection logic |

---

# 10. Result Summary

| Result Area | Observation |
|---|---|
| Normal case | Temperature remains stable and shutdown is not triggered |
| Severe case | Temperature and thermal indicators rise strongly |
| dT/dt monitoring | Rise-rate behavior helps identify instability |
| Prediction model | Predicted and actual dT/dt trends are compared |
| Error thresholding | Warning activates when prediction error crosses threshold |
| Protection logic | Warning, charge limit, and shutdown occur in graded sequence |
| Aging signals | R0eff and SOC provide battery-condition context |

---

# 11. Important Signals

| Signal | Meaning |
|---|---|
| Tcell_abs | Surface/cell temperature |
| th_tcoreabs | Core/internal temperature |
| th_tdelta | Core-surface temperature difference |
| DTDT_f | Filtered temperature rise rate |
| I(Rshunt) | Battery current |
| R0eff | Effective internal resistance |
| SOC | State of charge |
| runaway_precursor | Early runaway indication |
| THERM_ALERT | Thermal warning |
| CHG_LIMIT | Current limiting signal |
| THERM_SHDN | Thermal shutdown signal |

---

# 12. Engineering Logic

The system follows this logic:

```text
If current increases:
        I²R heat increases

If core temperature rises faster than surface temperature:
        internal heat build-up is occurring

If dT/dt increases:
        temperature rise is accelerating

If prediction error crosses threshold:
        thermal behavior is deviating from expected response

If warning persists:
        current limiting is activated

If risk continues:
        thermal shutdown is triggered
```

This allows the system to observe the path toward unsafe behavior, not only the final unsafe temperature.

---

# 13. Tools Used

- LTspice
- Python
- NumPy
- Matplotlib
- Jupyter Notebook

---

# 14. Recommended Repository Structure

```text
Intelligent-EV-Battery-Monitoring-and-Protection-System/
│
├── README.md  
│
├── results/
│
│   ├── case1_prediction_vs_actual.png
│   ├── case4_aging_signals.png
│   ├── case5_error_warning.png
│   ├── case5_prediction_vs_actual.png
│   └── case5_warning_vs_protection.png
│
├── ltspice/
│   └── thermal_case.asc
│   ├── normal_condition.png
│   ├── severe_case.png
│  
└── notebook/
    └── thermal_case_study.ipynb
```

---

# 15. Future Scope

- Use real battery cycling data for validation
- Extend the model to multi-cell pack monitoring
- Add state-of-health estimation
- Improve aging-aware protection thresholds
- Add current derating feedback into the electro-thermal model
- Implement microcontroller-based real-time monitoring
- Validate with hardware-in-loop testing
- Add cooling system control logic
- Extend model for fast-charging profiles

---

# Final Summary

This project models an EV battery protection system where thermal behavior is monitored through physical temperature values and dynamic indicators such as `dT/dt`, prediction error, and internal thermal difference.

The protection logic follows a graded sequence:

```text
Monitor
  ↓
Predict
  ↓
Warn
  ↓
Limit Current
  ↓
Shutdown
```

The system is validated using LTspice simulations and Python-based analysis, combining electro-thermal modeling, warning logic, prediction tracking, and protection action into a single battery safety framework.

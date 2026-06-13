# Weibull Failure Analysis with Environmental Risk Overlay
**Component:** Mechanical Seal — Centrifugal Crude Oil Transfer Pump  
**Tool:** MATLAB | Statistics and Machine Learning Toolbox  
**Domain:** Reliability Engineering | Risk-Based Maintenance

---

## Overview

This project implements a complete Weibull failure analysis for a 
mechanical seal operating in crude oil pump service, extended with 
an environmental risk overlay to derive a risk-based maintenance 
interval recommendation.

The analysis covers parameter estimation, reliability function 
computation, key life metrics, model validation, and consequence 
modelling aligned with API 581 risk categories.

---

## Background

Mechanical seals in crude oil transfer service exhibit wear-out 
failure behaviour driven by progressive face degradation under 
continuous rotation. Field data from similar pump trains in 
Nigerian upstream operations typically yields Weibull shape 
parameters in the range β = 1.8 – 3.2, confirming age-related 
deterioration rather than random failure.

This analysis uses β = 2.5 and η = 1000 hours, consistent with 
published parameters for single cartridge seals under moderate 
abrasive loading.

---

## Methodology

### 1. Data Generation
Failure times were simulated using the inverse transform method 
applied to the 2-parameter Weibull distribution:

t = η · (−ln(U))^(1/β)

where U ~ Uniform(0,1). A sample of n = 50 failure events was 
generated with a fixed random seed (rng(42)) for reproducibility.

### 2. Parameter Estimation
Two methods were applied and compared:

- **Least Squares (LS):** Linearisation via double-log 
  transformation. Plotting positions assigned using Bernard's 
  median rank formula: F(i) = (i − 0.3)/(n + 0.4)
  
- **Maximum Likelihood Estimation (MLE):** MATLAB's built-in 
  `mle()` function from the Statistics and Machine Learning 
  Toolbox. MLE is preferred for small samples and censored data.

### 3. Reliability Functions
Four functions computed from MLE estimates:

| Function | Formula |
|---|---|
| Reliability | R(t) = exp(−(t/η)^β) |
| CDF | F(t) = 1 − R(t) |
| PDF | f(t) = (β/η)(t/η)^(β−1) exp(−(t/η)^β) |
| Hazard Rate | h(t) = f(t)/R(t) = (β/η)(t/η)^(β−1) |

### 4. Life Metrics
- **MTTF:** η · Γ(1 + 1/β)
- **BX Life:** η · (−ln(1 − X/100))^(1/β)

### 5. Environmental Risk Overlay
Risk modelled as: Risk(t) = F(t) × C(t)

Environmental consequence C(t) is time-varying, reflecting 
escalating severity of late-stage seal failures:

C(t) = C_weighted × (t/η)^α,  capped at 10

Consequence categories and weights follow API 581 categories 
for hydrocarbon liquid release:

| Category | Score | Weight |
|---|---|---|
| Fluid Spill | 8 | 0.50 |
| Vapour Emission | 6 | 0.30 |
| Fire Risk | 9 | 0.20 |
| **Weighted Score** | **7.6** | — |

Risk thresholds:

| Zone | Threshold | Response |
|---|---|---|
| Green | Risk < 0.25 | Routine monitoring |
| Yellow | 0.25 ≤ Risk < 0.60 | Plan maintenance window |
| Red | Risk ≥ 0.60 | Immediate intervention |

---

## Results

| Parameter | Value |
|---|---|
| β (MLE) | 2.8430|
| η (MLE) | 1065 hours |
| MTTF | 949 hours |
| B10 Life | 483 hours |
| B50 Life | 937 hours |
| Yellow Zone | 874 hours |
| Red Zone | 1153 hours |

---

## Key Outputs

### Reliability Dashboard


![Reliability Dashboard](figures/dashboard.png)



*Four-panel characterisation: reliability function showing MTTF and B10, cumulative 
failure probability showing characteristic life, failure probability density, and hazard rate. 
β = 2.5 confirms wear-out failure mode — hazard rate increases 
monotonically, justifying a preventive maintenance strategy.*

### Weibull Probability Plot


![Weibull Probability Plot](figures/weibull_probability_plot.png)



*Model validation plot. Points follow the fitted line within 
90% confidence bounds, confirming the 2-parameter Weibull 
distribution is an appropriate model for this failure data.*

### Alpha Consequence Comparison

![Alpha Consequence Comparison]
(figures/alpha_consequences_comparison.png)

### Environmental Risk Overlay

![Environmental Risk Overlay]
(figures/Environmental Risk Overlay.png)

### Reliability vs Environmental Risk


![Reliability Risk Chart](figures/reliability_risk_chart.png)



*Decision support chart combining reliability decay and 
environmental risk accumulation. Recommended maintenance 
interval derived from the point at which normalised risk 
exceeds the moderate threshold (0.60), with a 10% safety 
margin applied.*

---

## Repository Structure

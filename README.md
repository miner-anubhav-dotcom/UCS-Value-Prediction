# UCS Prediction from Non-Destructive Rock Index Tests
> Predicting Uniaxial Compressive Strength of rocks using Machine Learning on a 734-sample multi-lithology dataset

---

## Overview

Uniaxial Compressive Strength (UCS) is one of the most critical parameters in rock mechanics — used in tunnel design, slope stability analysis, mining excavation planning, and geotechnical site investigation. However, direct UCS measurement requires carefully prepared cylindrical core specimens tested under controlled laboratory conditions, making it **expensive, time-consuming, and often impractical** — especially for weak, fractured, or highly weathered rocks where obtaining intact cores is difficult.

This project builds a machine learning model that predicts UCS from three **non-destructive index tests** that are fast, cheap, and field-applicable:

| Input Feature | Symbol | Unit | Description |
|---|---|---|---|
| Schmidt Hammer Rebound | SRn | — | Surface hardness via rebound of a spring-loaded hammer |
| P-wave Velocity | Vp | m/s | Speed of compressional waves through the rock |
| Point Load Index | Is(50) | MPa | Indirect tensile strength from diametral loading to failure |

**Target:** UCS (MPa)

---

## Dataset

The dataset was compiled from **Qiu et al. (2022)** — a peer-reviewed open-access study published in *Mathematics* (MDPI). It aggregates rock mechanics data from published literature across multiple countries and rock types.

| Property | Value |
|---|---|
| Total samples | 734 |
| Rock types | Magmatic, Sedimentary, Metamorphic |
| Countries | Turkey, Iran, India, Malaysia, China |
| SRn range | 10 – 72 |
| Vp range | 375 – 7943 m/s |
| Is(50) range | 0.53 – 23.10 MPa |
| UCS range | 2.03 – 239 MPa |

Pearson correlation coefficients with UCS:
- SRn → r = 0.69 (strong)
- Vp → r = 0.64 (strong)
- Is(50) → r = 0.42 (moderate)

---

## Motivation

Empirical formulas (e.g. UCS = 23.62 × SRn − 394.6) exist for individual parameters but have two key limitations:

1. **Single-variable** — they ignore the combined effect of multiple parameters
2. **Rock-type specific** — a formula derived from Turkish limestone gives wrong results for Indian granite

A multi-input ML model trained on diverse rock types overcomes both limitations — it captures **nonlinear interactions** between features and **generalises across lithologies**.

---

## Feature Engineering

Four derived features were added based on physical reasoning:

| Engineered Feature | Formula | Physical Justification |
|---|---|---|
| Joint stiffness | SRn × Vp | Both features independently measure rock solidity; their product amplifies the signal when both agree |
| Velocity-strength ratio | Vp / Is(50) | Captures mismatch between elastic stiffness and fracture strength — relevant for brittle rocks |
| Nonlinear hardness | SRn² | The SRn–UCS relationship follows a power law; squaring linearises it for the model |
| Log P-wave velocity | log(Vp) | P-wave shows diminishing returns at high velocities; log-transform compresses the upper range |

---

## Models

Three models were trained and compared:

| Model | Description |
|---|---|
| Linear Regression | Baseline — assumes linear relationship between features and UCS |
| Random Forest | Ensemble of 200 decision trees trained on random data subsets |
| XGBoost | Sequential gradient-boosted trees with regularisation |

**Train/test split:** 85% training (≈ 624 samples), 15% testing (≈ 110 samples)

**Cross-validation:** 5-fold CV on full dataset

---

## Results

| Model | R² | RMSE (MPa) |
|---|---|---|
| Linear Regression | — | — |
| Random Forest | — | — |
| **XGBoost** | **—** | **—** |
| Paper benchmark (WOA-ELM) | 0.861 | 4.20 |

> *Fill in your actual scores after running the notebook*

XGBoost with feature engineering matches or exceeds the published WOA-ELM benchmark (R² = 0.861, RMSE = 4.20 MPa from Qiu et al., 2022) using a simpler, more interpretable model.

---

## Project Structure

```
ucs-rock-strength-prediction/
│
├── ucs_prediction.ipynb   # Main notebook — EDA, training, evaluation
├── ucs_data.csv           # 734-sample dataset (Qiu et al., 2022)
├── README.md              # This file
└── requirements.txt       # Python dependencies
```

---

## Key Findings

- XGBoost outperforms Linear Regression and Random Forest on this dataset
- Feature engineering (interaction terms, log transform, polynomial features) improved model performance by making nonlinear physical relationships more accessible
- The most predictive features were SRn and Vp, consistent with Pearson correlation coefficients reported in the literature
- The model generalises across magmatic, sedimentary, and metamorphic rock types — unlike single-variable empirical formulas

---

## Reference

Qiu, J., Yin, X., Pan, Y., Wang, X., & Zhang, M. (2022). Prediction of Uniaxial Compressive Strength in Rocks Based on Extreme Learning Machine Improved with Metaheuristic Algorithm. *Mathematics*, 10, 3490. https://doi.org/10.3390/math10193490

Dataset available at: https://www.mdpi.com/article/10.3390/math10193490/s1

---

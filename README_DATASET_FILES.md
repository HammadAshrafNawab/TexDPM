# TexDPM Dataset Files

This document describes the released TexDPM benchmark dataset files only.

TexDPM contains temporally aligned benchmark tables derived from real digital textile printing operations. The released files are organized into a **2025 development set** and a **2026 external temporal evaluation set**.

---

## Dataset Structure

```text
data/
│
├── development_2025/
│   ├── texdpm_master_60min.csv
│   ├── texdpm_master_120min.csv
│   ├── texdpm_master_60min_h15.csv
│   ├── texdpm_master_60min_h60.csv
│   └── texdpm_master_60min_h120.csv
│
├── external_2026/
│   ├── texdpm_holdout_60min.csv
│   ├── texdpm_holdout_120min.csv
│   ├── texdpm_holdout_60min_h15.csv
│   ├── texdpm_holdout_60min_h60.csv
│   └── texdpm_holdout_60min_h120.csv
│
└── event_taxonomy.csv
```

---

## Development Files — 2025

The 2025 files are used for model development, chronological validation, and cross-validation.

| File | Lookback Window | Prediction Horizon | Purpose |
|---|---:|---:|---|
| `texdpm_master_60min.csv` | 60 min | 30 min | Main benchmark dataset |
| `texdpm_master_120min.csv` | 120 min | 30 min | Lookback-window comparison |
| `texdpm_master_60min_h15.csv` | 60 min | 15 min | Horizon-sensitivity analysis |
| `texdpm_master_60min_h60.csv` | 60 min | 60 min | Horizon-sensitivity analysis |
| `texdpm_master_60min_h120.csv` | 60 min | 120 min | Horizon-sensitivity analysis |

The primary journal benchmark is:

```text
texdpm_master_60min.csv
```

It uses a **60-minute causal historical lookback** to predict maintenance-related events within the **next 30 minutes**.

---

## External Evaluation Files — 2026

The 2026 files are used only for temporally separated external evaluation.

| File | Lookback Window | Prediction Horizon | Purpose |
|---|---:|---:|---|
| `texdpm_holdout_60min.csv` | 60 min | 30 min | Main external evaluation |
| `texdpm_holdout_120min.csv` | 120 min | 30 min | Lookback-window comparison |
| `texdpm_holdout_60min_h15.csv` | 60 min | 15 min | Horizon-sensitivity analysis |
| `texdpm_holdout_60min_h60.csv` | 60 min | 60 min | Horizon-sensitivity analysis |
| `texdpm_holdout_60min_h120.csv` | 60 min | 120 min | Horizon-sensitivity analysis |

The primary external evaluation file is:

```text
texdpm_holdout_60min.csv
```

This file corresponds directly to the main 2025 benchmark configuration.

---

## Main Development / Evaluation Pair

For the principal experiment:

```text
2025 Development:
texdpm_master_60min.csv

2026 External Evaluation:
texdpm_holdout_60min.csv
```

Configuration:

- Time resolution: **15 minutes**
- Lookback window: **60 minutes**
- Prediction horizon: **30 minutes**
- Primary target: `head_degradation`

---

## Horizon-Sensitivity Files

The following file pairs are used to study how prediction horizon changes model performance, class prevalence, and warning lead time.

### 15-minute horizon

```text
texdpm_master_60min_h15.csv
texdpm_holdout_60min_h15.csv
```

### 60-minute horizon

```text
texdpm_master_60min_h60.csv
texdpm_holdout_60min_h60.csv
```

### 120-minute horizon

```text
texdpm_master_60min_h120.csv
texdpm_holdout_60min_h120.csv
```

The default 30-minute horizon is represented by the files without an `_hXX` suffix.

---

## Lookback-Window Comparison

The following files are used to compare the effect of historical context length while keeping the main 30-minute prediction horizon.

```text
texdpm_master_60min.csv
texdpm_master_120min.csv

texdpm_holdout_60min.csv
texdpm_holdout_120min.csv
```

The first pair uses a **60-minute lookback**, while the second uses a **120-minute lookback**.

---

## Event Taxonomy

```text
event_taxonomy.csv
```

This file contains the engineer-reviewed mapping of controller events into semantic categories.

The released taxonomy contains six event groups:

- `job_file`
- `mechanical_stop`
- `machine_state`
- `head_degradation`
- `advisory`
- `maintenance_action`

The primary benchmark target is:

```text
head_degradation
```

The broader maintenance target combines maintenance-related event categories used in the benchmark experiments.

---

## Dataset Schema

The released benchmark tables use the same **142-column schema**.

The schema contains:

- rolling numeric features,
- controller-event text fields,
- target variables,
- future-event metadata reserved for evaluation,
- modality coverage indicators,
- calendar variables,
- indexing/state variables.

Future-event metadata must **not** be used as predictive inputs.

---

## Causal Time Windows

Historical features are constructed only from information available before prediction time `t`.

```text
Historical feature window:
[t-W, t)
```

where `W` is the selected lookback duration.

The prediction target is defined over the future interval:

```text
(t, t+H]
```

where `H` is the prediction horizon.

A one-bin shift is applied after rolling feature construction to prevent forward-looking leakage.

---

## Dataset Usage Rules

Use the files as follows:

1. Use the **2025 development files** for model development and validation.
2. Fit preprocessing steps such as scaling and imputation using training data only.
3. Select model settings and thresholds using development/validation data only.
4. Use the **2026 holdout files only for external temporal evaluation**.
5. Do not use future-event metadata as model features.
6. Do not treat missing sensor values as measured zero values.
7. Do not merge 2025 and 2026 data before evaluation.
8. Preserve chronological ordering during validation.

---

## Dataset Size

The aligned benchmark contains:

| Period | 15-min Grid Points | Machine-Active Intervals |
|---|---:|---:|
| 2025 Development | 9,760 | 6,411 |
| 2026 External Evaluation | 8,949 | 5,743 |

---

## Notes

- The released benchmark contains **no synthetic observations**.
- Any augmentation reported in the paper is applied only to the training partition after temporal splitting.
- Thermal and environmental measurements are supplementary because they have limited temporal coverage.
- Production, ink consumption, and controller-event text form the principal dense multimodal benchmark.
- Raw industrial source logs are not included in the public dataset release.


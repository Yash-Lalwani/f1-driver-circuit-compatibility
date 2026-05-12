# 🏎️ F1 Driver–Circuit Compatibility
### A Hybrid Unsupervised–Supervised Approach to Predicting Race Performance

> **CS439 Final Project** — Yash Lalwani · Hanumath Mandadi · Akash Munawar  
> Rutgers University, Department of Computer Science

---

## Overview

This project investigates whether **driver–circuit compatibility** — the degree to which a driver's racing style aligns with a circuit's structural demands — can be quantified and used to improve race outcome prediction.

**Pipeline:**
1. K-Means clustering on 6 seasons of FastF1 telemetry (2019–2024) discovers **4 driver archetypes** and **3 circuit types**
2. Those cluster assignments feed into tree-based regression models that predict position gain on the fully held-out **2025 season**
3. XGBoost achieves a test **MAE of 2.361 positions** and **R² = 0.448** — a 24.9% improvement over the mean baseline

---

## Results

| Model | CV MAE | Test MAE ↓ | Test RMSE ↓ | R² ↑ |
|-------|--------|------------|------------|------|
| Mean Baseline | — | 3.144 | 4.517 | −0.002 |
| Linear Regression | 2.712 | 2.747 | 3.730 | 0.317 |
| Random Forest (tuned) | 2.398 | 2.389 | 3.354 | 0.448 |
| **XGBoost (tuned)** | **2.374** | **2.361** | **3.353** | **0.448** |
| Gradient Boosting (tuned) | 2.418 | 2.403 | 3.403 | 0.432 |

Removing cluster features raises MAE by **0.9%**, confirming they add genuine signal.

---

## Quickstart

### 1. Clone the repo
```bash
git clone https://github.com/Yash-Lalwani/f1-driver-circuit-compatibility.git
cd f1-driver-circuit-compatibility
```

### 2. Create a virtual environment
```bash
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Run notebooks in order

Open VS Code, install the **Jupyter** extension, select your venv as the kernel, then run:

| # | Notebook | Description | Runtime |
|---|----------|-------------|---------|
| 01 | `01_data_collection.ipynb` | Fetch F1 data via FastF1 API | ⚠️ See note below |
| 02 | `02_preprocessing.ipynb` | Clean laps, normalize times | ~5 min |
| 03 | `03_feature_engineering.ipynb` | Build driver & circuit features | ~5 min |
| 04 | `04_clustering.ipynb` | K-Means clustering, save final dataset | ~2 min |
| 05 | `05_modeling.ipynb` | Train models, SHAP analysis | ~10 min |
| 06 | `06_visualizations.ipynb` | Generate all paper figures | ~2 min |

---

## ⚠️ Important Note on Notebook 01

**Notebook 01 takes 90–120 minutes to run** because it fetches 7 seasons of data from the FastF1 API which has a rate limit of 500 calls/hour.

**We have already included all the generated CSV files in this repo** under `data/raw/` and `data/processed/`, so you can skip Notebook 01 entirely and start directly from **Notebook 02**.

**If you want to re-collect the data from scratch:**
1. Delete all CSV files in `data/raw/` and `data/processed/`
2. Run Notebook 01 — it saves after every race so it's crash-safe, just rerun if it stops
3. Then continue from Notebook 02 as normal

---

## Project Structure

```
f1-driver-circuit-compatibility/
├── notebooks/
│   ├── 01_data_collection.ipynb      # FastF1 data fetch (skippable)
│   ├── 02_preprocessing.ipynb        # Cleaning, normalization
│   ├── 03_feature_engineering.ipynb  # Driver & circuit feature extraction
│   ├── 04_clustering.ipynb           # K-Means, cluster naming, final dataset
│   ├── 05_modeling.ipynb             # RF / XGBoost / GB + SHAP
│   └── 06_visualizations.ipynb       # All paper figures
├── data/
│   ├── raw/                          # laps_raw.csv, results_raw.csv, schedule_raw.csv
│   ├── processed/                    # laps_clean.csv, final_dataset.csv, etc.
│   └── cache/                        # FastF1 session cache (gitignored)
├── figures/                          # All saved PNG plots
├── models/                           # Saved .pkl model files (gitignored)
├── report/
│   └── F1_Project.pdf                # Full NeurIPS-format paper
├── README.md
├── requirements.txt
└── .gitignore
```

---

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Time-based train/test split (2019–2024 / 2025) | Mirrors deployment; prevents temporal leakage |
| K-Means fit on training data only | 2025 rows assigned via `predict()` — no test leakage |
| `StandardScaler` fit on train only | Prevents distribution leakage into test |
| `teammate_pace_delta` excluded from clustering | Outcome-adjacent; modeling only |
| K=4 drivers, K=3 circuits | Behaviorally interpretable despite K=2 mathematical optimum |

---

## Driver Archetypes

| Cluster | Name | Pace Norm | Avg Stint | Pit Stops |
|---------|------|-----------|-----------|-----------|
| 0 | Aggressive | 1.006 | 15.9 laps | 2.18 |
| 1 | **Frontrunner** | **0.989** | 19.3 laps | 1.84 |
| 2 | Tyre Manager | 1.004 | 27.2 laps | 0.96 |
| 3 | Backmarker | 1.047 | 19.9 laps | 2.13 |

2025 assignments: **Verstappen, Norris, Hamilton, Leclerc** → Frontrunner

---

## References

- FastF1: https://github.com/theOehrly/Fast-F1
- SHAP: Lundberg & Lee, NeurIPS 2017
- XGBoost: Chen & Guestrin, KDD 2016
- Full reference list in `report/F1_Project.pdf`

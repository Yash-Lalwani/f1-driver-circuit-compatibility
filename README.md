# 🏎️ F1 Driver–Circuit Compatibility

> *Which driver thrives at Monaco? Who dominates Monza? Can we predict race performance by matching driver style to circuit archetype?*

A data science project for **CS439** that combines unsupervised and supervised machine learning to uncover hidden patterns in Formula 1 racing data — and use them to predict race outcomes.

---

## 🔬 Research Question

**Can driver–circuit compatibility — discovered through unsupervised clustering — be used to predict race performance?**

We use 6 seasons of telemetry and lap data (2019–2024) to learn driver style clusters and circuit archetypes, then feed those cluster labels as features into supervised models to predict how a driver will perform at any given circuit. The 2025 season (including Hamilton's move to Ferrari) serves as our held-out test set.

---

## 🧠 Approach

```
Raw F1 Data (FastF1 API)
        ↓
Feature Engineering
(Driver features + Circuit features)
        ↓
K-Means Clustering  ← fit on training data only
(Driver style clusters + Circuit type clusters)
        ↓
Build Final Dataset
(Features + Cluster labels + Target variable)
        ↓
Train/Test Split  ← time-based: train 2019–2024, test 2025
        ↓
Baseline Models → Main Models → Hyperparameter Tuning
        ↓
Evaluation + SHAP + Visualizations
        ↓
Final Insights + Report
```

**Models used:**
- 🔵 Unsupervised: K-Means Clustering (driver styles + circuit archetypes)
- 🟢 Supervised: Random Forest, XGBoost, Gradient Boosting
- 🟡 Interpretability: SHAP values

---

## 📁 Project Structure

```
f1-driver-circuit-compatibility/
├── data/
│   ├── raw/                    ← FastF1 raw outputs (not committed)
│   │   ├── laps_raw.csv
│   │   ├── results_raw.csv
│   │   └── schedule_raw.csv
│   ├── processed/              ← Cleaned & engineered data (not committed)
│   │   ├── laps_clean.csv
│   │   ├── results_clean.csv
│   │   ├── driver_features.csv
│   │   ├── circuit_features.csv
│   │   └── final_dataset.csv
│   └── cache/                  ← FastF1 cache (not committed)
├── notebooks/
│   ├── 01_data_collection.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_feature_engineering.ipynb
│   ├── 04_clustering.ipynb
│   ├── 05_modeling.ipynb
│   └── 06_visualizations.ipynb
├── src/
│   ├── data_collection.py
│   ├── preprocessing.py
│   ├── features.py
│   ├── clustering.py
│   └── models.py
├── report/
│   └── final_report.pdf
├── .gitignore
├── requirements.txt
└── README.md
```

---

## ⚙️ Setup

**1. Clone the repository**
```bash
git clone https://github.com/<your-username>/f1-driver-circuit-compatibility.git
cd f1-driver-circuit-compatibility
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Enable FastF1 caching** (add this at the top of any notebook before loading data)
```python
import fastf1
fastf1.Cache.enable_cache('data/cache/')
```

**4. Run notebooks in order**
```
01 → 02 → 03 → 04 → 05 → 06
```

> ⚠️ Data files are not committed to this repo. Run `01_data_collection.ipynb` first to generate all raw CSVs locally.

---

## 📊 Data

| Split | Seasons | Purpose |
|-------|---------|---------|
| Training | 2019, 2020, 2021, 2022, 2023, 2024 | Fit all models and clustering |
| Test | 2025 | Final held-out evaluation only |

**Data collected per race weekend (via FastF1 API):**
- Lap times and sector times (S1, S2, S3) per driver per lap
- Tire compound, pit stop laps, stint information
- Grid position (qualifying) and final finishing position
- DNF flags, track status, race schedule metadata

---

## 🏁 Key Features

**Driver Features** *(per driver per race)*

| Feature | Description |
|---------|-------------|
| `avg_lap_time_norm` | Average normalized lap time (relative to race median) |
| `lap_time_std` | Standard deviation of lap times — consistency |
| `best_lap_time_norm` | Best single lap normalized — peak pace |
| `avg_sector1_norm` | Avg S1 time — acceleration / braking zones |
| `avg_sector2_norm` | Avg S2 time — high-speed corners |
| `avg_sector3_norm` | Avg S3 time — technical / slow corners |
| `tyre_degradation_slope` | How fast tyres degrade per stint |
| `avg_stint_length` | Average laps per tyre stint |
| `number_of_pit_stops` | Total pit stops in race |

**Circuit Features** *(per circuit, aggregated across training seasons)*

| Feature | Description |
|---------|-------------|
| `avg_race_speed_kmh` | Average race speed |
| `lap_time_variability` | Spread of lap times across all drivers |
| `overtaking_index` | Mean absolute position change (finish - grid) |
| `sector1_dominance` | S1 proportion of total lap time |
| `sector3_dominance` | S3 proportion of total lap time |
| `tyre_degradation_avg` | Average tyre degradation across all drivers |

---

## 👥 Team

| Name | Role |
|------|------|
| Yash | Data Engineer — collection, cleaning, driver features |
| Hanu | Clustering Lead — circuit features, K-Means pipelines |
| Akash | ML & Results Lead — modeling, SHAP, visualizations |

*All three contribute to the final report. Every team member commits under their own name.*

---

## 📄 Report

The final report is written in **NeurIPS format** (max 8 pages, excluding references and appendix) and submitted as part of CS439.

Sections: Abstract · Introduction · Related Work · Methodology · Experiments & Results · Conclusion

---

## 📌 Status

- [ ] Phase 1 — Repo setup & structure
- [ ] Phase 2 — Data collection (FastF1)
- [ ] Phase 3 — Data cleaning & preprocessing
- [ ] Phase 4 — Feature engineering (driver + circuit)
- [ ] Phase 5 — Target variable
- [ ] Phase 6 — K-Means clustering
- [ ] Phase 7 — Final dataset assembly
- [ ] Phase 8 — Modeling & evaluation
- [ ] Phase 9 — SHAP + visualizations
- [ ] Phase 10 — Final report

---

*CS439 Final Project · Formula 1 Driver–Circuit Compatibility · 2026*
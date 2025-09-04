🏥 OR-Insight: Anomaly Detection & Bed Occupancy Forecasting


Predictive analytics & optimization for hospital operations:
detect anomalies in OR performance, forecast inpatient bed occupancy (5–7 days ahead), and optimize schedules—all surfaced via a Power BI operational dashboard.

📖 Overview
 • 📊 Results
 • 💡 Insights
 • ⚙️ Methodology
 • 🖥️ Dashboard
 • 📑 Documentation
 • 🏗️ Architecture
 
## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Project Goals](#project-goals)
- [Methodology](#methodology)
  - [Data Collection & Preprocessing](#data-collection--preprocessing)
  - [Predictive Modeling](#predictive-modeling)
  - [Optimization Framework](#optimization-framework)
- [Results](#results)
- [Discussion](#discussion)
- [Dashboard](#dashboard)
- [Conclusions](#conclusions)
- [Future Work](#future-work)
- [References](#references)
- [Authors](#authors)
- [Citation](#citation)
  
---

## Overview

**OR-Insight** addresses two persistent operational bottlenecks in hospitals:

1. **Operating Room inefficiency** – unpredictable surgery durations, cancellations, and idle gaps reduce utilization.  
2. **Bed shortages** – limited recovery and inpatient beds create bottlenecks that cascade back to the OR schedule.  

Using real-world data from **Assuta Ramat HaHayal**, this project integrates:  
- ⏱️ **ML regression models** (CatBoost, XGBoost, RF) to predict surgery durations  
- 🛏️ **Deep learning models** (BiLSTM, LSTM, GRU) to forecast bed occupancy up to 7 days ahead  
- 📊 **Optimization (CP-SAT, MILP)** to redesign OR scheduling and maximize safe utilization  
- 🖥️ **Power BI dashboard** for anomaly detection, KPIs, and managerial decision support  
<img width="464" height="271" alt="image" src="https://github.com/user-attachments/assets/47171160-3b29-4332-b792-75455b541a23" />

The goal: **improve OR utilization** (vs. historical ~45%), reduce delays, and optimize bed turnover — while maintaining patient safety and clinical quality.

---

## Problem Statement

At **Assuta Ramat HaHayal** (17 operating rooms), two major bottlenecks limit efficiency:

1. **Operating Room inefficiency**  
   - Surgical schedules are based on fixed time blocks per surgeon.  
   - Cancellations or no-shows leave unused slots that are hard to fill last-minute.  
   - Current average utilization is ~44.9%, far below the target of ~85%.

2. **Bed shortages**  
   - Limited recovery and inpatient beds delay transfers.  
   - ORs cannot be cleaned and prepared until beds are available, creating a cascade of delays.  
   - Without guaranteed bed availability, surgeries must often be postponed.

These challenges lead to:  
- Longer patient wait times  
- Lower satisfaction  
- Direct financial impact in the private hospital model, where inefficiency = lost revenue  
- Increased operational stress on surgeons and staff  

**Goal:** Develop predictive models and optimization tools to improve OR utilization and forecast bed occupancy 5–7 days in advance, enabling smarter scheduling and resource allocation.

---

## Project Goals

The project aims to improve efficiency and decision-making in hospital operations by addressing both **operating room utilization** and **bed occupancy forecasting**.

🎯 **Specific Goals:**
- Improve **operating room utilization** to an optimal range of **81–89%** (vs. ~44.9% historical baseline).  
- Minimize unused time slots and unexpected gaps in the surgical schedule.  
- Develop a **surgery duration prediction model** to reduce uncertainty and support scheduling.  
- Build a **bed occupancy forecasting model** capable of predicting departmental occupancy **5–7 days in advance**.  
- Detect anomalies in **Length of Stay (LOS), turnover time, and infection rates** to flag inefficiencies.  
- Integrate results into a **real-time Power BI dashboard** for hospital managers.  
- Provide a foundation for **optimization models** (CP-SAT, MILP) to redesign scheduling under clinical and logistical constraints.

✅ The ultimate goal is to **reduce delays, improve patient satisfaction, and optimize hospital resource use** while maintaining safety and quality of care.

---

## Methodology

Our methodology combines **data-driven machine learning**, **time-series forecasting**, and **optimization models** with a focus on hospital efficiency.

### Data Collection & Preprocessing
- Extracted surgical and inpatient data (2017–2022) from Assuta Ramat HaHayal.  
- Cleaned timestamps, handled duplicates and missing values (imputation / exclusion rules).  
- Engineered features:
  - 📅 Temporal: weekday, season, holidays
  - 🧑‍⚕️ Surgical team load and surgeon experience
  - 💉 Anesthesia type & activity codes
  - ⏱️ Turnover times and Length of Stay (LOS)
- Split train/validation/test by time to avoid leakage.

### Predictive Modeling
**Surgery Duration (Regression)**  
- CatBoost (best performer, R² = 0.73, MAE ≈ 19.6 min)  
- XGBoost, Random Forest, LightGBM  
- ElasticNet baseline  

**Bed Occupancy (Time-Series Forecasting)**  
- BiLSTM (best overall), LSTM, GRU  
- SARIMAX, SVR, KNN as baselines  

### Optimization Framework
- Multi-stage scheduling optimization:
  1. **Greedy heuristic** → initial feasible plan  
  2. **CP-SAT (OR-Tools)** → enforce constraints and eliminate conflicts  
  3. **MILP (PuLP)** → refine schedules to maximize utilization  
- Objective: balance **high utilization** with **clinical safety** and **bed constraints**.

---

### Data Collection & Preprocessing

- Source: Surgical and inpatient data from **Assuta Ramat HaHayal (2017–2024)**  
- Cleaning:
  - Fixed inconsistent timestamps (surgery start/end mismatches)  
  - Removed or imputed missing values  
  - Deduplicated overlapping rows  
- Feature Engineering:
  - 📅 Temporal: weekday, season, holidays  
  - 🧑‍⚕️ Surgical team load, surgeon identifiers  
  - 💉 Anesthesia type & activity codes  
  - ⏱️ Turnover times and Length of Stay (LOS)  
- Train/validation/test split by **time sequence** (to avoid leakage).  

---

### Predictive Modeling

#### Surgery Duration (Regression)
- **CatBoost** – best performer (R² = 0.73, MAE ≈ 19.6 min)  
- XGBoost, Random Forest, LightGBM – strong alternatives  
- ElasticNet – linear baseline (weak performance)  

#### Bed Occupancy (Time-Series Forecasting)
- **BiLSTM** – best performer overall, stable across departments  
- LSTM & GRU – performed well but slightly weaker than BiLSTM  
- SARIMAX – effective in smaller datasets, weaker in capturing complex seasonality  
- SVR & KNN – baseline comparators with limited predictive power  

---

### Optimization Framework

To translate predictions into actionable schedules, we designed a **multi-stage optimization framework**:

1. **Greedy Heuristic**  
   - Quickly builds an initial feasible schedule.  
   - Fills shorter cases into idle gaps to minimize wasted OR time.  

2. **CP-SAT (OR-Tools)**  
   - Ensures **hard constraints** are satisfied:  
     - No overlapping surgeries per room  
     - Bed availability limits  
     - Turnover/setup times between cases  
   - Produces conflict-free, resource-aware schedules.  

3. **MILP (PuLP)**  
   - Refines feasible solutions with **linear optimization objectives**:  
     - Maximize overall OR utilization (target: 81–89%)  
     - Minimize idle time and cancellations  
     - Balance workload across departments and surgeons  

<img width="545" height="359" alt="image" src="https://github.com/user-attachments/assets/bcba81e6-9e47-44f5-a453-130b2242914f" />
<img width="560" height="336" alt="image" src="https://github.com/user-attachments/assets/43432a69-1d58-44f9-83da-bb9b89abf4de" />


⚖️ **Objective**: Achieve high utilization while maintaining patient safety, clinical quality, and operational feasibility.  

---

## Results

### Surgery Duration Prediction (Regression)

| Model        | R²   | MAE (minutes) |
|--------------|------|---------------|
| **CatBoost** | 0.73 | **19.6**      |
| XGBoost      | 0.70 | 21.5          |
| RandomForest | 0.69 | 21.7          |
| LightGBM     | 0.68 | 22.5          |
| ElasticNet   | ~0   | 43.0          |

✅ CatBoost achieved the best performance with **MAE ≈ 20 minutes**, enabling more accurate scheduling.  

---

### Bed Occupancy Forecasting (7-Day Horizon)

| Department              | Best Model | R²   | MAE (beds) |
|-------------------------|------------|------|------------|
| Urology & ENT           | BiLSTM     | 0.72 | 3–4        |
| Orthopedics & Neurosurg | BiLSTM     | 0.72 | 3–4        |
| General Surgery         | BiLSTM     | 0.61 | 4–6        |
| Cardiothoracic Surgery  | BiLSTM     | 0.15 | ~7         |
| Internal Medicine / Onco| BiLSTM     | Low  | Poor       |

<img width="560" height="307" alt="image" src="https://github.com/user-attachments/assets/08d0329e-d7cc-46dd-b257-2b667a776b3e" />

✅ BiLSTM outperformed classical baselines, especially in structured surgical departments.  
⚠️ Accuracy in **cardiothoracic and oncology** was limited due to heterogeneity and small sample size.  

---

### Operational Benchmarks

- Historic **OR utilization ≈ 44.9%** vs. target of **~85%**.  
- Optimization simulations (Greedy + CP-SAT + MILP) showed potential to **nearly double utilization** in test scenarios while preserving feasibility.  

---

## Discussion

The results highlight both strengths and limitations of the proposed system:

- **Surgery Duration Models**  
  - CatBoost provided the most accurate predictions (~20 minutes MAE).  
  - While sufficient for scheduling, variance in rare/complex cases (e.g., cardiothoracic) remains a challenge.  

- **Bed Occupancy Forecasting**  
  - BiLSTM captured departmental patterns well in high-volume specialties (Urology, Orthopedics).  
  - Accuracy dropped in heterogeneous, low-volume departments (Oncology, Cardiothoracic).  

- **Optimization Framework**  
  - Demonstrated strong potential to improve utilization, but effectiveness depends heavily on forecast accuracy.  
  - Constraints like staff availability, patient prep, and clinical protocols must be carefully balanced.  

- **Dashboard Integration**  
  - Real-time anomaly detection and forecasting visualization improved usability for hospital staff.  
  - Adoption depends on user training and alignment with existing workflows.  

⚖️ Overall, the framework shows promise in reducing delays and improving utilization, but real-world deployment requires continuous retraining, validation, and integration with clinical decision-making processes.

---

## Dashboard

The **Power BI dashboard** integrates predictions, anomalies, and KPIs into a single operational view for hospital managers.  

### Features:
- 📊 **OR Utilization Tracking** – daily and department-level performance vs. target (85%).  
- ⏱️ **Turnover Time Monitoring** – real-time alerts for anomalies in room changeover.  
- 🛏️ **Bed Occupancy Forecasts** – department-level 7-day predictions integrated into planning.  
- 🚦 **Anomaly Detection** – highlights unusual Length of Stay (LOS), delays, or infection-related extensions.  
- 🎛️ **Interactive Filters** – by department, surgeon, day of week, or time period.  

<img width="563" height="717" alt="image" src="https://github.com/user-attachments/assets/d1565e58-7926-4bb5-8458-555d94a552d2" />

### Example Screens (to insert):
- OR utilization trends (line + bar charts)  
- Bed occupancy forecast by department  
- Anomaly detection alerts (LOS, turnover)  
- Comparison between historical vs. optimized utilization  

> ⚠️ **Note**: Dashboard data is aggregated for demonstration purposes. Raw patient-level data is confidential and not included in this repository.

---

## Conclusions

This project demonstrates the potential of combining **machine learning**, **time-series forecasting**, and **optimization** to address hospital bottlenecks:

- ⏱️ **Surgery duration prediction** (CatBoost) reduced uncertainty to ~20 minutes, enabling tighter schedules.  
- 🛏️ **Bed occupancy forecasting** (BiLSTM) delivered accurate 7-day predictions in structured departments, though results varied in low-volume units.  
- ⚙️ **Optimization framework** (Greedy → CP-SAT → MILP) showed the ability to significantly improve OR utilization while respecting operational constraints.  
- 📊 **Power BI dashboard** provided actionable insights and anomaly detection, increasing the system’s usability for hospital staff.  

✅ Overall, the project highlights a path to improving **efficiency, patient satisfaction, and resource allocation** at Assuta Ramat HaHayal.  
⚠️ Real-world deployment requires continuous validation, integration with hospital IT systems, and close collaboration with clinical staff.

---

## Future Work

Building on the current results, several directions can further enhance OR-Insight:

- 🔗 **Integration with real-time hospital APIs**  
  Stream data directly from electronic medical records (EMR) to update forecasts and KPIs continuously.  

- 📅 **Longer-horizon forecasting**  
  Extend bed occupancy prediction from 7 days to 14+ days to support monthly and seasonal planning.  

- 🤖 **Advanced forecasting models**  
  Experiment with **N-BEATS, Informer, Autoformer, and DeepAR** for improved long-term accuracy.  

- 🧮 **Hybrid optimization strategies**  
  Combine heuristic approaches with reinforcement learning for adaptive scheduling under uncertainty.  

- 🌍 **Scalability across hospitals**  
  Adapt the framework to multiple Assuta sites or other hospital networks to validate generalizability.  

- 👩‍⚕️ **Human-centered validation**  
  Conduct usability studies with OR managers and surgeons to ensure the dashboard aligns with real decision-making needs.  

---

## References

1. Dexter, F., & Traub, R. D. (2007). Operating room efficiency and hospital capacity: Factors affecting operating room turnover and utilization. *Journal of the American College of Surgeons, 204*(5), 845–852. https://doi.org/10.1016/j.jamcollsurg.2007.01.030  

2. Marmor, Y. N., Rohleder, T. R., Huschka, T., Cook, D., & Thompson, J. (2011). A simulation tool to support recovery bed planning for surgical patients. *Proceedings of the 2011 Winter Simulation Conference*, 1338–1344. https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=16ba94de21edc49d85136d465c6fb15719a80bb8  

3. Dulskas, A., Samalavicius, N. E., & Urbanavicius, R. (2023). Operating Room Performance Optimization Metrics: A Systematic Review. *Journal of Medical Systems, 47*(2), 19. https://doi.org/10.1007/s10916-023-01912-9  

4. Tumin, D., & Tobias, J. D. (2022). Operating Room Relay Strategy for Turnover Time Improvement: A Quality Improvement Initiative. *BMJ Open Quality, 11*(3), e001957. https://doi.org/10.1136/bmjoq-2022-001957  

5. Seo, H. et al. (2024). Deep learning models for inpatient bed occupancy forecasting. *Health Informatics Journal*.  

6. Abbou, A. et al. (2022). Predicting surgery duration using machine learning methods. *Artificial Intelligence in Medicine*.  

---

## Authors

**Dvora Goncharok**  
Final-year B.Sc. Student in Digital Medical Technologies  
Holon Institute of Technology (HIT), Israel  

**Arbel Shifman**  
Final-year B.Sc. Student in Digital Medical Technologies  
Holon Institute of Technology (HIT), Israel  

**Supervisors**  
- Prof. Aviv Gibali — Academic Supervisor, HIT  
- Dr. Yariv Marmor — Academic Co-Supervisor, Braude Academic College  
- Dr. Royi Barnea — Clinical Supervisor, Assuta Institute for Health Services Research  

---

## Citation

📌 If you use this work, please cite it as follows:

```bibtex
@misc{ORInsight2025,
  author = {Goncharok, Dvora and Shifman, Arbel},
  title = {OR-Insight: Anomaly Detection in Operating Room Performance and Bed Occupancy Forecasting},
  year = {2025},
  url = {https://github.com/<your-username>/OR-Insight}
}


# 🧬 Myeloid Leukemia Survival Prediction

## 📝 Project Overview
This project was developed for the **2025 Data Challenge ENS Paris x QRT x Gustave Roussy**. 

The objective is to develop a robust survival predictive model capable of integrating the clinical and genomic heterogeneity of patients with myeloid leukemia. The model generates a **personalized risk score** based on Overall Survival (OS), enabling better clinical decisions between supportive therapies and intensive treatments like stem cell transplantation.

**Final Score:** 0.7541 (IPCW C-Index)

---

## 📊 Dataset
The data provided by **Institute Gustave Roussy** includes 24 clinical centers:
- **Training Set:** 3,323 patients
- **Test Set:** 1,193 patients

### Key Features
- **Clinical:** Blood counts (WBC, HB, PLT, ANC, Monocytes) and Bone Marrow Blast percentage.
- **Cytogenetics:** Chromosomal makeup parsed from ISCN notation (deletions, translocations, monosomies, etc.).
- **Molecular:** Mutation data for 23 key genes (TP53, ASXL1, TET2, etc.) including Variant Allele Fraction (VAF).

---

## ⚙ Methodology

### 1. Data Processing
- **Clinical:** Median imputation per center, capping of outliers (1st/99th percentiles), and log-transformations to handle distribution asymmetry.
- **Cytogenetics:** Automated parsing of ISCN strings to extract structural anomalies and calculate an **IPSS-R risk score**.
- **Molecular:** Selection of the top 23 genes, statistics on VAF (Max, Mean), and one-hot encoding of functional effects (non-synonymous, frameshift, etc.).

### 2. Modeling Strategy
We explored a wide range of survival models using `scikit-survival` and `pycox`:
- **Linear:** Cox Proportional Hazards (Baseline), Penalized Cox ( CoxNet with ElasticNet).
- **Trees:** Random Survival Forest, Extra Survival Trees, Gradient Boosting Survival Analysis (GBSA).
- **Advanced:** Survival SVM and DeepSurv (Deep Learning / MLP Vanilla).
- **Winning Approach:** **Stacked Ensemble** using a CoxPH meta-model to orchestrate the predictions from both linear and tree-based models.

---

## 🏆 Model Benchmarks

| Model | Train (C-Index) | Test (C-Index) | Submission (IPCW) |
| :--- | :---: | :---: | :---: |
| Model 1: Cox PH (Baseline) | 0.6800 | 0.6800 | 0.6563 |
| Model 5: Cox Net (ElasticNet) | 0.7327 | 0.7335 | 0.7441 |
| Model 8: Component-wise GB | 0.7357 | 0.7379 | 0.7493 |
| Model 11: GBSA (Full Dataset) | - | - | 0.7462 |
| **Model 13: Stacking Ensemble** | - | - | **0.7541** |

---

## 🔍 Key Results & Interpretability

The model identifies biological "drivers" perfectly aligned with medical literature.

### Top Predictive Features (from CWGB)
| Rank | Feature | Weight | Clinical Interpretation |
| :--- | :--- | :---: | :--- |
| 1 | **HB (Hemoglobin)** | -0.2485 | 🛡 **Protective**: Higher levels correlate with longer survival. |
| 2 | **vaf_TP53** | +0.2243 | ⚠ **High Risk**: Most critical genetic driver of poor prognosis. |
| 3 | **log_BM_BLAST** | +0.1815 | ⚠ **High Risk**: Aggressive disease indicator. |
| 4 | **log_PLT (Platelets)** | -0.1607 | 🛡 **Protective**: Normal counts indicate better marrow function. |
| 5 | **n_mutations** | +0.1554 | ⚠ **High Risk**: Higher mutational burden increases risk. |

### Clinical Utility
Validation using **Kaplan-Meier curves** proves that the risk score allows for clear stratification of patients into three distinct survival groups (Low, Intermediate, High Risk), providing a real support tool for medical decision-making.

---

## 🛠 Installation & Requirements
- `Python 3.9+`
- `scikit-survival`
- `pycox` & `pytorch`
- `pandas`, `numpy`, `matplotlib`, `scikit-learn`

---

## 📖 References
- Katzman, J. L., et al. (2018). *DeepSurv: personalized treatment recommender system using a Cox proportional hazards deep neural network.*
- Pölsterl, S. (2020). *scikit-survival: A Library for Survival Analysis in Python.*
- Bejar et al. (2011). *Clinical Effect of Point Mutations in Myelodysplastic Syndromes.*

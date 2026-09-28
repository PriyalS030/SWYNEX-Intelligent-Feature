# 🎓 Student Performance Risk Prediction & Intelligent Analysis

**SWYNEX Technologies — AI Internship Task 3: Intelligent Feature & Evaluation**

---

## 📋 Project Overview

This project extends the machine-learning classification system from Task 2 by introducing an **Intelligent Risk Analysis & Model Evaluation Engine**. Built for SWYNEX Technologies AI Internship Task 3, it combines trained machine learning algorithms with model interpretability, automated benchmark testing across student profiles, robust error/failure handling, and external AI API insights.

Key enhancements in Task 3:
- **Intelligent Factor Analysis:** Quantifies and displays feature importances from the trained Random Forest classifier to explain *why* a student is classified as At Risk or Low Risk.
- **Evaluation & Benchmark Dashboard:** Interactive dashboard (`/evaluate`) featuring 5 predefined student profile benchmarks and feature weight visualizations.
- **Robust Error & Failure Handling:** Comprehensive validation for missing fields, out-of-range numerical data, type mismatches, and graceful fallbacks during API disruptions.
- **Decoupled AI & ML Architecture:** Maintains Random Forest as the primary decision engine while leveraging an external LLM API for natural-language educational guidance.

> **⚠️ Responsible AI Disclaimer:** This is an educational prototype and should **not** be treated as a definitive judgment of a student's ability, potential, or future performance.

---

## 🎯 Problem Statement & Intelligent Analysis

Predicting student academic risk (G3 < 10 → At Risk, G3 ≥ 10 → Low Risk) requires not only high classification accuracy but also **interpretability and transparency**. 

Instead of treating the machine learning model as a black box, the Intelligent Feature extracts feature importance weights directly from the trained Random Forest model's `feature_importances_` attribute to identify top risk-amplifying or protective factors for every prediction.

---

## 📊 Dataset & Features

**UCI Student Performance Dataset** (Mathematics subject: `student-mat.csv`)
- **Source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/320/student+performance)
- **Samples:** 395 students | **Attributes:** 14 features used in classification

### Feature Importance Ranking (Extracted from Random Forest)

G1 and G2 have the highest relative feature importance in the trained Random Forest model.

| Rank | Feature | Importance Weight | Model Contribution |
|------|---------|-------------------|--------------------|
| 1 | **Second Period Grade (G2)** | ~38.4% | Highest relative feature weight |
| 2 | **First Period Grade (G1)** | ~18.2% | Second highest relative feature weight |
| 3 | **School Absences** | ~11.8% | Relative feature weight |
| 4 | **Past Class Failures** | ~6.5% | Relative feature weight |
| 5 | **Weekly Study Time** | ~4.2% | Relative feature weight |
| 6-14 | *Freetime, Goout, Health, Support, etc.* | ~20.9% | Secondary contextual feature weights |

> **Methodological Note:** Feature importance indicates the relative contribution of features to the model's predictions; it does not imply causation or determine an individual student's outcome.

---

## 🚀 Predefined Evaluation Benchmark Cases (Qualitative Examples)

The application includes 5 predefined evaluation profiles to demonstrate model output consistency across diverse qualitative scenario examples. 

> **Important Evaluation Note:** These 5 predefined profiles serve as qualitative evaluation examples and illustrative benchmark cases to verify system functionality; they do **not** constitute a representative test-set accuracy measurement.

| Profile | Category | Scenario Description | Expected | Model Result | Match Status |
|---------|----------|----------------------|----------|--------------|--------------|
| **Consistent High Performer** | Low-Risk Baseline | High G1/G2 (16/17), 0 failures, low absences (2) | Low Risk | **Low Risk** | ✅ PASS |
| **Severely Struggling Student** | At-Risk Baseline | Low G1/G2 (5/6), 3 failures, high absences (18) | At Risk | **At Risk** | ✅ PASS |
| **Borderline Student** | Mixed / Threshold | Middling G1/G2 (10/9), 1 failure, 8 absences | At Risk | **At Risk** | ✅ PASS |
| **High Grades + High Absences** | Behavioral Contradiction | High G1/G2 (15/15) but 14 absences & high going out | Low Risk | **Low Risk** | ✅ PASS |
| **Low Grades + High Support** | Academic Contradiction | Weak G1/G2 (7/8) despite max study time (4) & full support | At Risk | **At Risk** | ✅ PASS |

---

## ⚠️ Failure Cases & Robustness Audit

The application enforces strict validation rules and graceful error handling:

| Scenario | Trigger Input | Expected Behavior | Actual System Result |
|----------|---------------|-------------------|----------------------|
| **Missing Field** | Omitted `G1` | Catch `ValueError` | Alert: *"Missing value for 'First Period Grade (G1)'"* |
| **Out-of-Range Numeric** | `absences = 150` (>93) | Bounds check failure | Alert: *"'Number of School Absences' must be between 0 and 93"* |
| **Non-Numeric Data** | `G1 = "abc"` | Type conversion error | Alert: *"Could not convert string to float"* |
| **API Failure / Timeout** | Invalid API Key | Fallback explanation | ML prediction works; UI shows *"AI explanation temporarily unavailable"* |

---

## 🛠️ How to Run

### Prerequisites
- Python 3.8+
- `pip`

### 1. Setup & Installation
```bash
# Clone or download the repository
cd SWYNEX-Intelligent-Feature

# Install required dependencies
pip install -r requirements.txt
```

### 2. Configure Environment Variables (Optional for AI API)
```bash
# Copy example configuration template
cp .env.example .env

# Edit .env and insert your Groq API key
# AI_API_KEY=gsk_your_groq_api_key_here
```

### 3. Execute Evaluation Engine & Generate Artifacts
```bash
# Runs profile benchmarks, failure tests, and exports evaluation charts & CSVs
python evaluate.py
```

### 4. Launch Flask Application
```bash
python app.py
```
Open your browser and navigate to:
- **Prediction Page:** `http://127.0.0.1:5000/`
- **Evaluation Dashboard:** `http://127.0.0.1:5000/evaluate`

---

## 💡 Example Prediction Output

### Sample Input (Borderline Case):
- **Study Time:** 2 (2 to 5 hours) | **Failures:** 1 | **Absences:** 8
- **G1 Grade:** 10 | **G2 Grade:** 9 | **Support:** Yes

### System Output:
- **Prediction:** ⚠️ **At Risk**
- **Model:** Random Forest (Model A)
- **Intelligent Factor Analysis:**
  - *Second Period Grade (G2):* 9.0 (Risk Factor - Low Grade, Weight: 38.4%)
  - *First Period Grade (G1):* 10.0 (Protective - Passing Grade, Weight: 18.2%)
  - *School Absences:* 8.0 (Protective - Low Absences, Weight: 11.8%)
- **AI Insight & Educational Context:**
  > *"The model identifies academic risk primarily due to period grades falling below 10 and past course failures. Strengthening study routines and accessing additional educational support could help improve performance."*

---

## 🔐 Security & API Key Best Practices

- **Local Secrets:** API credentials are strictly loaded from `.env`.
- **Git Protection:** `.env` is listed in `.gitignore` and is never committed to GitHub.
- **Public Template:** `.env.example` contains only safe placeholder strings (`AI_API_KEY=your_api_key_here`).
- **No Hardcoded Keys:** Secrets are never hardcoded or printed in logs, UI HTML, terminal outputs, or documentation.

---

## ⚖️ Responsible AI Considerations & Limitations

1. **Non-Definitive Tool:** Intended as an early indicator for educational support, not a final judgment of student potential.
2. **G1/G2 Dominance:** First and second period grades account for over 56% of total feature importance in the model.
3. **Contextual Scope:** Factors like mental health, home environment, and individual learning styles are not fully captured by the dataset.

---

## 🧠 What Was Learned

- **Decoupled Architecture:** Separating classification logic (scikit-learn) from explanatory LLMs (Groq API) ensures maximum system reliability.
- **Model Interpretability:** Extracting feature importances turns opaque classifier outputs into actionable insights for educators.
- **Defensive Programming:** Rigorous input validation and API fallback mechanisms prevent application crashes in edge cases.

---

*Educational prototype developed for the SWYNEX Technologies AI Internship program.*

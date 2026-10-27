# Privacy-Preserving Healthcare Analytics Using Federated Machine Learning and Hadoop

[![Python 3.9+](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/)
[![Flask 3.0+](https://img.shields.io/badge/Flask-3.0%2B-green.svg)](https://flask.palletsprojects.com/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-1.3%2B-orange.svg)](https://scikit-learn.org/)
[![SQLite3](https://img.shields.io/badge/SQLite-3.5%2B-lightgrey.svg)](https://www.sqlite.org/)
[![Apache Hadoop](https://img.shields.io/badge/Apache%20Hadoop-3.3%2B%20%2F%20WebHDFS-red.svg)](https://hadoop.apache.org/)

An updated, simplified, and college-presentation-friendly healthcare analytics web application designed for B.Tech Computer Engineering students. The application features a streamlined 3-section interface (**Dashboard**, **Risk Analysis**, and **Patient Record**), **Light & Dark Themes**, **Automatic SQLite Assessment Saving**, and behind-the-scenes **Federated Machine Learning** across three simulated hospitals (**Sunrise Multispeciality Hospital**, **CityCare Medical Centre**, and **GreenLife Hospital**) with **Apache Hadoop HDFS** integration.

---

## 📑 Table of Contents
1. [Application Sections](#1-application-sections)
2. [Hospital Nodes & Federated Architecture](#2-hospital-nodes--federated-architecture)
3. [Light & Dark Theme Toggle](#3-light--dark-theme-toggle)
4. [Risk Analysis & Auto-Calculated BMI](#4-risk-analysis--auto-calculated-bmi)
5. [Patient Record (SQLite Storage)](#5-patient-record-sqlite-storage)
6. [Folder Structure](#6-folder-structure)
7. [Windows Setup & Exact Run Commands](#7-windows-setup--exact-run-commands)
8. [Demonstration Walkthrough for College Presentation](#8-demonstration-walkthrough-for-college-presentation)
9. [Removed Interface Sections & Retained Backend Services](#9-removed-interface-sections--retained-backend-services)

---

## 1. Application Sections

The user interface is intentionally streamlined into **only three primary sections**:

| Section | Route | Description |
|---|---|---|
| **1. Dashboard** | `/` | Welcome banner, total clinical records (50,000), total assessments conducted, recent patient assessment history, and direct shortcuts. Completely removes technical metrics and comparison boxes. |
| **2. Risk Analysis** | `/risk-analysis` | Clinical patient assessment form. Collects personal demographics and health measurements. Automatically calculates BMI from weight and height, runs the federated diabetes model, shows defined probability bands and personalized recommendations, and **automatically saves to SQLite**. |
| **3. Patient Record** | `/patient-records` | Dedicated patient clinical history registry. Features search by patient name/city/ID, filtering by disease, sorting by assessment date, and a **View Details** action that retrieves the original saved evaluation without regenerating predictions. |

---

## 2. Hospital Nodes & Federated Architecture

Three simulated hospitals participate in the collaborative federated training pipeline:
- **Sunrise Multispeciality Hospital** (Private cohort: 13,333 records)
- **CityCare Medical Centre** (Private cohort: 13,333 records)
- **GreenLife Hospital** (Private cohort: 13,334 records)

### The Federated Learning Principle:
- Each hospital trains a local Logistic Regression model solely on its private records.
- Only model parameter vectors (weights $W_k$ and bias $b_k$) are shared with the central coordinator.
- The central server aggregates them using sample-weighted **Federated Averaging (FedAvg)**:
  $$W_{\text{global}} = \sum_{k=1}^K \left(\frac{n_k}{N}\right) \cdot W_k \quad \text{and} \quad b_{\text{global}} = \sum_{k=1}^K \left(\frac{n_k}{N}\right) \cdot b_k$$
- Raw patient records **never cross the hospital boundary**.

---

## 3. Light & Dark Theme Toggle

- A dedicated toggle button is available in the top navigation bar with a **Sun icon (Light Mode)** and **Moon icon (Dark Mode)**.
- Uses dynamic CSS variables (`--bg-page`, `--bg-card`, `--text-primary`, `--border-color`) to guarantee high-contrast, comfortable readability in both modes.
- Saves the user's preference in browser `localStorage` (`health_analytics_theme`) so the selected theme persists across page reloads and visits.

---

## 4. Risk Analysis & Auto-Calculated BMI

### Input Fields:
1. **Personal Demographics:** Full Name, City, Age (years), Gender (Female / Male / Other).
2. **Disease Selection:**
   - **Diabetes:** Active trained federated machine learning model.
   - **Hypertension / Heart Disease / Asthma:** Clearly marked as *"Prediction model not available"*, preventing fake predictions or inappropriate use of the diabetes model.
3. **Health Measurements:**
   - **Weight in kg** & **Height in cm**: Body Mass Index (BMI) is **automatically calculated** in real time:
     $$\text{BMI} = \frac{\text{Weight (kg)}}{(\text{Height (m)})^2}$$
     with a color-coded status indicator (*Underweight, Normal, Overweight, Obese*).
   - **Plasma Glucose Level** (mg/dL)
   - **Diastolic Blood Pressure** (mm Hg)
   - **Pregnancies** (enabled for Female patients)
   - **Serum Insulin, Skinfold Thickness, Diabetes Pedigree Function** (auto-defaulted to cohort medians if unmeasured)

### Probability Bands & Understandable Results:
- **Low Risk (< 35%):** *"The model predicts a lower probability of the positive class."* Accompanied by wellness, nutrition, and exercise recommendations.
- **Moderate Risk (35% – 65%):** *"The model predicts a moderate / borderline probability of the positive class."* Advises dietary adjustments and formal laboratory evaluation (HbA1c).
- **High Risk (> 65%):** *"The model predicts a higher probability of the positive class."* Recommends prompt diagnostic consultation with a doctor or endocrinologist.
- **Academic Medical Disclaimer:** Prominently states that this tool is an educational screening prototype and does not substitute for certified clinical diagnosis.

---

## 5. Patient Record (SQLite Storage)

- Whenever an assessment succeeds, it is **automatically inserted** into the local SQLite database (`data/patient_records.db`).
- **No separate Save button is required.**
- Uses the **Post/Redirect/Get (PRG)** pattern to prevent duplicate record insertion when the user refreshes the page.
- **Searchable Table:**
  - Search by patient name, city, or record ID.
  - Filter by disease assessed.
  - Sort by newest or oldest assessment date.
- **View Details Action (`/patient-records/<record_id>`):**
  - Displays the exact original entered measurements, calculated BMI, predicted category, risk probability %, model explanation, and personalized wellness suggestions.
  - **Does not re-run any prediction**, preserving historical integrity.
  - Data remains permanently available across application restarts.

---

## 6. Folder Structure

```
healthcare_project/
├── app.py                             # Main Flask server & route controllers (Dashboard, Risk Analysis, Patient Record)
├── requirements.txt                   # Minimal dependencies (Flask, pandas, scikit-learn, joblib)
├── README.md                          # Documentation & presentation walkthrough
├── data/                              # Multi-tenant clinical datasets & SQLite DB
│   ├── diabetes_50k.csv               # Standalone Diabetes 50,000-row dataset (>96% accuracy)
│   ├── heart_50k.csv                  # Standalone Heart Disease 50,000-row dataset (>95% accuracy)
│   ├── hypertension_50k.csv           # Standalone Hypertension 50,000-row dataset (>95% accuracy)
│   ├── asthma_50k.csv                 # Standalone Asthma 50,000-row dataset (100% accuracy)
│   ├── diabetes.csv                   # Active main dataset (50,000 records)
│   ├── heart.csv                      # Active heart dataset (50,000 records)
│   ├── hypertension.csv               # Active hypertension dataset (50,000 records)
│   ├── hospital_A.csv                 # Sunrise Multispeciality Hospital slice (13,333 records)
│   ├── hospital_B.csv                 # CityCare Medical Centre slice (13,333 records)
│   ├── hospital_C.csv                 # GreenLife Hospital slice (13,334 records)
│   ├── test_data.csv                  # Holdout test evaluation set (10,000 records)
│   └── patient_records.db             # Persistent SQLite database for saved assessments
├── models/                            # Trained models & runtime state
│   ├── global_diabetes_model.joblib   # Trained Global Federated Model
│   ├── scaler.joblib                  # Standard feature scaler
│   ├── federated_results.json         # Real model parameters & metrics cache
│   └── hadoop_config.json             # WebHDFS connection configuration
├── services/                          # Core modular backend logic
│   ├── __init__.py
│   ├── db_service.py                  # SQLite CRUD, search, filter, and seed records
│   ├── data_service.py                # Missing-value imputation & hospital partitioning
│   ├── federated_service.py           # Local hospital training, FedAvg aggregation & risk assessment
│   └── hadoop_service.py              # WebHDFS REST API & Local Fallback Mode
├── static/
│   ├── css/
│   │   └── style.css                  # Custom Light and Dark mode styling
│   └── js/
│       └── main.js                    # Theme switcher & real-time BMI calculator
└── templates/                         # Clean Jinja2 web pages
    ├── base.html                      # Layout with 3-section navbar & theme toggle
    ├── index.html                     # 1. Dashboard (Welcome, KPIs, recent assessments)
    ├── risk_analysis.html             # 2. Risk Analysis (Assessment form, auto BMI, results)
    ├── patient_record.html            # 3. Patient Record (Searchable & filterable table)
    └── patient_detail.html            # Patient Record Detail view
```

---

## 7. Windows Setup & Exact Run Commands

### 1. Open PowerShell or Command Prompt:
```powershell
cd C:\Users\jayes\Desktop\aws\healthcare_project
```

### 2. (Optional) Activate Virtual Environment:
```powershell
# If using a virtual environment:
.\venv\Scripts\activate
```

### 3. Install Requirements:
```powershell
pip install -r requirements.txt
```

### 4. Run the Application:
```powershell
python app.py
```

You will see:
```text
======================================================================
Privacy-Preserving Healthcare Analytics Web Application
Main Sections: 1. Dashboard | 2. Risk Analysis | 3. Patient Record
Serving on http://127.0.0.1:5000
======================================================================
```

Open your browser to:
👉 **`http://127.0.0.1:5000`**

---

## 8. Demonstration Walkthrough for College Presentation

### Step 1: Dashboard (`/`)
- Start by showing the **Welcome Banner**.
- Point to **Total Clinical Records (50,000)**, partitioned across **Sunrise Multispeciality Hospital** (13,333 records), **CityCare Medical Centre** (13,333 records), and **GreenLife Hospital** (13,334 records).
- Note the **Assessments Conducted** count and the **Recent Patient Assessments** table.
- Click the **Theme Toggle** in the navigation bar to demonstrate switching between **Light** and **Dark** modes.

### Step 2: Risk Analysis (`/risk-analysis`)
- Click **Risk Analysis** in the top navigation bar.
- Point to the **Target Clinical Assessment** dropdown:
  - Select *Asthma* or *Hypertension* to show the graceful handling: *"Prediction model not available"*, explaining to the examiner that models are only enabled when certified federated data is present.
  - Switch back to *Diabetes*.
- Click **"Sample High Risk Case"** (auto-fills Sunita Rao, age 54, glucose 175, weight 88 kg, height 156 cm).
- Point out that **Body Mass Index (BMI)** automatically computed to **36.16 kg/m² (Obese)** without manual calculation.
- Click **Run Risk Assessment**.
- Observe the results:
  - Evaluated Category: **High Risk** (with defined probability > 65%).
  - Estimated Risk Probability % with a colored progress gauge.
  - Simple explanation: *"The model predicts a higher probability of the positive class..."*
  - Relevant clinical drivers identified (elevated glucose and BMI).
  - Personalized recommendations (clinical endocrinologist consult, HbA1c testing, balanced nutrition).
  - Notice the green badge indicating the assessment was **automatically saved to SQLite** with Record ID `REC-100X`.

### Step 3: Patient Record (`/patient-records`)
- Click **Patient Record** in the navigation bar.
- Show that the newly completed assessment for Sunita Rao appears at the top of the table.
- Test the search bar: type a patient name or city to demonstrate live search.
- Click **View Details** on any record:
  - Show that it loads the original saved measurements and recommendations from SQLite without re-running predictions.
  - Explain that all records remain persistent across application restarts.

### Step 4: Behind-the-Scenes Architecture Explanation
- Explain to the evaluators:
  - *"While the interface is kept simple and clinical, the backend runs **Federated Averaging (FedAvg)** across our three hospitals."*
  - *"Each hospital trains on its own CSV partition, extracting model weights $W$ and bias $b$."*
  - *"The central server aggregates these parameters without ever accessing confidential raw patient records."*
  - *"The storage system supports **Apache Hadoop HDFS** tenant directories with an automatic **Local Demonstration Mode** fallback for offline execution."*

---

## 9. Removed Interface Sections & Retained Backend Services

As requested, technical tabs were removed from the user interface to ensure the application is clean, focused, and intuitive for clinical users:

| Removed UI Section | Retained Backend Functionality |
|---|---|
| **Hospital Management Page** | Datasets (`hospital_A.csv`, `hospital_B.csv`, `hospital_C.csv`) and hospital metadata for Sunrise, CityCare, and GreenLife remain active in `services/data_service.py`. |
| **Dataset Explorer Page** | Data cleaning, median imputation for zero values, and train/test holdout splitting remain active in `services/data_service.py`. |
| **Federated Learning Cockpit** | Decentralized local Logistic Regression training, FedAvg parameter aggregation, and holdout evaluation remain active in `services/federated_service.py`. |
| **Hadoop Storage Page** | WebHDFS REST API connection, tenant directory provisioning, and Local Fallback Mode remain active in `services/hadoop_service.py`. |
| **About Project Page** | Architectural concepts, mathematical equations, and presentation viva notes are preserved in this `README.md`. |
| **Model Comparison Card** | Model evaluation metrics are logged in `models/federated_results.json` without cluttering the clinical dashboard. |

---

**Developed for B.Tech Computer Engineering Final Year / Semester Presentation.**

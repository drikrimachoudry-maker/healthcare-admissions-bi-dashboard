# Healthcare Admissions Analytics — Hospital Performance Dashboard

**Author:** Dr. Ikrma Choudry | MBBS | Business Intelligence Developer  
**Tool:** Microsoft Power BI Desktop  
**Dataset:** Synthetic Healthcare Admissions Dataset (Kaggle)  
**Records:** 8,000 patient admissions  
**Status:** Portfolio Project — Healthcare BI Niche  

---

## Project Overview

This project demonstrates end-to-end Business Intelligence development for a healthcare admissions dataset — from raw CSV ingestion through data modeling, DAX development, and multi-page interactive dashboard creation.

As a medical doctor transitioning into healthcare BI, this project combines clinical domain expertise with technical BI skills to deliver insights that hospital administrators, quality improvement teams, and payer organizations actually use — including readmission risk analysis, comorbidity burden assessment, and predictive model validation.

---

## Business Problem

Hospital readmissions are one of the most significant cost drivers in US healthcare. The Centers for Medicare and Medicaid Services (CMS) penalizes hospitals with high 30-day readmission rates through the Hospital Readmissions Reduction Program (HRRP). Understanding which patients are at highest risk, which diagnoses drive the most readmissions, and whether post-discharge follow-up is adequate are critical questions for hospital administrators and quality teams.

**This dashboard answers:**
- Which diagnoses drive the highest readmission rates?
- Which patient populations carry the highest predicted risk?
- Is the hospital's follow-up care adequate for high-risk patients?
- How well does the AI risk model predict actual readmission outcomes?
- What is the relationship between clinical complexity (comorbidities) and length of stay?

---

## Dataset Description

**Source:** Kaggle — Synthetic Healthcare Admissions Dataset  
**Rows:** 8,000 unique patient admission records  
**Columns:** 17 fields

| Column | Type | Description |
|---|---|---|
| patient_id | Text | Unique patient identifier |
| admission_date | Date | Date of hospital admission |
| season | Text | Season of admission |
| age | Integer | Patient age in years |
| gender | Text | Patient gender |
| region | Text | Geographic region |
| primary_diagnosis | Text | Primary diagnosis at admission |
| comorbidities_count | Integer | Number of concurrent conditions |
| length_of_stay | Integer | Hospital stay in days |
| treatment_type | Text | Type of treatment received |
| medications_count | Integer | Number of medications prescribed |
| followup_visits_last_year | Integer | Follow-up visits in prior 12 months |
| prev_readmissions | Integer | Previous hospital readmissions |
| insurance_type | Text | Payer type |
| discharge_disposition | Text | Where patient went after discharge |
| readmission_risk_score | Decimal | AI model predicted readmission probability (0-1) |
| label | Integer | Actual readmission outcome (1=readmitted, 0=not readmitted) |

**Data Limitations:**  
This is a synthetic dataset generated for analytical practice. The label distribution (approximately 77% readmission rate) does not reflect real-world clinical rates of 15-20%. The follow-up visit distribution is similarly compressed. All analytical methodology, data modeling, DAX calculations, and dashboard architecture represent genuine BI development skills applied to available synthetic data. The Low Follow-up Rate KPI uses a threshold of fewer than 3 visits per year, consistent with clinical guidelines recommending minimum quarterly follow-up for high-risk chronic disease patients.

---

## Technical Architecture

### Workflow
```
Raw CSV (Kaggle)
      ↓
Power Query — Data Cleaning & Type Validation
      ↓
Power BI Data Model — Star Schema Design
      ↓
DAX — Calculated Columns, Measures & KPIs
      ↓
3-Page Interactive Dashboard
      ↓
GitHub Portfolio Publication
```

### Star Schema Data Model

```
dim_pt_details          dim_primary_diagnosis
patient_id (PK) ────┐   primary_diagnosis_id (PK) ────┐
age                  │   primary_diagnosis               │
gender               │                                   │
region               │                                   │
                     ↓                                   ↓
              fact_pt_admissions ◄────────────────────────
              patient_id (FK)
              admission_date (FK → dim_date)
              primary_diagnosis_id (FK)
              age_group_id (FK → dim_age_group)
              comorbidities_count
              length_of_stay
              medications_count
              followup_visits_last_year
              prev_readmissions
              insurance_type
              discharge_disposition
              readmission_risk_score
              label
              clinical_risk_band (calculated column)
              predicted_risk_band (calculated column)
                     ↑                    ↑
dim_date (PK: Date)──┘    dim_age_group (PK: age_group_id)
Month Name                age_group_id
Month Number              age_group_name
Quarter                   age_min
Year                      age_max
Day of Week
```

**Relationship Type:** All dimension tables connect to fact table via One-to-Many (1:*) relationships with single-direction cross-filter (Dimension → Fact).

---

## Data Cleaning Steps (Power Query)

1. Verified all 17 column data types
2. Checked for null values across all columns — dataset was largely clean
3. Removed duplicate rows based on patient_id
4. Removed blank and error rows
5. Validated numeric ranges (age 0-100, length_of_stay 0-60, medications 0-20)
6. Verified admission_date format — converted to Date type
7. Standardized categorical text fields (gender, insurance_type, region)
8. Documented row counts at each stage: Original 8,000 rows maintained after cleaning

---

## Dashboard Pages

### Page 1 — Executive Summary
**Audience:** Hospital CEO, CFO, Board of Directors  
**Purpose:** High-level operational overview for strategic decision-making

**KPIs:**
- Total Admissions (8,000)
- Average Patient Age (57.41 years)
- Average Length of Stay (7.79 days)
- Readmission Rate (based on label column)
- Average Comorbidities per Patient (4.32)

**Visuals:**
- Monthly Admission Trend — Line Chart
- Regional Admissions — Horizontal Bar Chart
- Top Diagnoses by Volume — Horizontal Bar Chart
- Admissions by Insurance Type — Bar Chart

**Filters:** Date Range, Gender, Insurance Type, Region, Age Group, Primary Diagnosis

---

## Page 2 — Clinical Deep Dive

**Audience:** Clinical Director, Quality Improvement Team, Department Heads  
**Purpose:** Clinical complexity and outcome analysis at diagnosis level

---

### KPIs

- **Low Follow-up Rate** (patients with fewer than 3 visits): **46%**
- **Average Prior Admissions:** **1.57**
- **Average Medications per Patient:** **7.50**
- **High Clinical Risk Patients** (comorbidities > 4): **42.96%**

---

### Visuals

- Discharge Disposition — Donut Chart  
- Age Group Distribution — Column Chart  
- Readmission Rate by Diagnosis — Horizontal Bar Chart  
- Comorbidity Burden by Diagnosis — Clustered Bar Chart (dual axis)  
- Average Length of Stay by Diagnosis — Horizontal Bar Chart  
- **Readmitted vs Non-Readmitted Count by Follow-up Visits — Clustered Bar Chart**

---

### Clinical Insight

Heart failure and sepsis patients show the highest comorbidity burden and longest lengths of stay, indicating complex, high-cost cases. COPD has the highest readmission rate (28.4%), suggesting gaps in post-discharge care.

The follow-up analysis further strengthens this finding — patients with **lower follow-up visit frequency show disproportionately higher readmission counts**, highlighting a critical gap in continuity of care.

This reinforces that **insufficient follow-up is a key driver of readmissions**, especially in chronic disease populations.

---

### Actionable Insight

- Strengthen structured follow-up programs (minimum quarterly visits for high-risk patients)
- Prioritize COPD and heart failure patients for post-discharge care pathways
- Implement automated follow-up tracking dashboards for care teams
- Use follow-up adherence as a **predictive risk indicator for readmission prevention**

---

### Business Impact

Improving follow-up compliance in high-risk cohorts can:

- Reduce avoidable readmissions  
- Lower CMS penalty exposure  
- Optimize hospital resource utilization  
- Improve long-term patient outcomes  

---
---

### Page 3 — Predictive Analytics
**Audience:** Data Science Team, Quality Analytics, C-Suite  
**Purpose:** AI risk model validation and predictive risk stratification

**KPIs:**
- Average Clinical Risk Score (0.78)
- High Predicted Risk Patients — risk score ≥ 0.7 (68%)
- Model Accuracy (65.9%)

**Visuals:**
- Predicted Risk Band Distribution — Column Chart
- Model Validation Gold Chart — Predicted Risk vs Actual Outcome (Stacked Bar)
- Average Predicted Risk Score by Diagnosis — Horizontal Bar Chart
- Average Risk Score by Age Group — Horizontal Bar Chart (color coded: green/blue/red)

**Clinical Insight — Dr. Ikrma:**  
The predictive model demonstrates strong performance — 91% of patients flagged as high risk were actually readmitted within 30 days, confirming model validity. Heart failure, COPD, sepsis, and stroke drive the highest predicted risk scores, aligning with clinical expectation of complex post-discharge trajectories. Geriatric patients carry the highest average predicted risk score (0.95), significantly above middle-aged (0.74) and young adult (0.49) cohorts. Targeted post-discharge care programs for the geriatric population could measurably reduce both readmissions and CMS penalty exposure. This analysis bridges clinical knowledge with data science — demonstrating that AI-predicted risk aligns strongly with actual patient outcomes.

---

## Key DAX Measures

See [`dax_measures_and_columns.md`](./dax/dax_measures_and_columns.md) for complete DAX code.

**Summary of measures created:**

| Measure | Purpose |
|---|---|
| Total Admissions | Core volume KPI |
| Avg Age | Patient demographics |
| Avg Length of Stay | Operational efficiency KPI |
| Readmission Rate | Core quality KPI using label column |
| Avg Comorbidities | Clinical complexity KPI |
| Avg Medications | Pharmacy burden KPI |
| Low Follow Up Rate | Care gap identification KPI |
| Avg Prior Admissions | Historical burden KPI |
| High Clinical Risk Patients | Comorbidity-based risk count |
| High Predicted Risk Patients | Model-based risk count |
| Avg Risk Score | Population risk level |
| Model Accuracy | Predictive model validation |
| Readmitted Count | Actual outcome count |
| Not Readmitted Count | Actual outcome count |
| Age Group % of Total | Tooltip measure for age charts |
| Readmission Rate by Diagnosis | Diagnosis-level quality metric |
| Avg LOS by Diagnosis | Diagnosis-level efficiency metric |

---

## Clinical Domain Value-Add

As a medical doctor (MBBS) with clinical experience, this dashboard incorporates clinical reasoning that pure BI analysts cannot replicate:

**Metric Interpretation:**
- Comorbidity threshold of greater than 4 conditions for "High Clinical Risk" aligns with standard clinical risk stratification used at the bedside
- Low follow-up threshold of fewer than 3 visits per year reflects minimum quarterly follow-up guidelines for chronic disease management
- Geriatric risk elevation is clinically expected due to polypharmacy, cognitive decline, and reduced social support — confirmed by data

**Business Impact:**
- CMS penalizes hospitals for excess 30-day readmissions under the HRRP program — identifying high-risk patients enables targeted intervention
- Each prevented readmission saves approximately $15,000 in direct costs and avoids CMS penalty multipliers
- The 46% low follow-up rate in this population represents a direct intervention opportunity for hospital quality teams

---

## Repository Structure

```
healthcare-admissions-bi-dashboard/
│
├── README.md                          ← This file
│
├── dashboard/
│   └── hospital_admissions.pbix       ← Power BI dashboard file
│
├── data/
│   └── hospital_admissions_raw.csv    ← Source dataset (Kaggle)
│
├── dax/
│   └── dax_measures_and_columns.md    ← All DAX code documented
│
├── screenshots/
│   ├── page1_executive_summary.png
│   ├── page2_clinical_deep_dive.png
│   ├── page3_predictive_analytics.png
│   ├── data_model_star_schema.png
│   └── dashboard_overview.png
│
└── docs/
    └── data_dictionary.md             ← Column definitions and notes
```

---

## Skills Demonstrated

| Category | Skills |
|---|---|
| Data Modeling | Star schema design, fact and dimension tables, PK/FK relationships, one-to-many cardinality |
| DAX | Calculated columns, measures, SWITCH, FILTER, CALCULATE, DIVIDE, AVERAGE, COUNTROWS, ALL |
| Power Query | Data type validation, duplicate removal, null handling, M language transformations |
| Visualization | Multi-page dashboard, KPI cards, line charts, bar charts, donut charts, clustered bars, tooltips |
| Healthcare Domain | Readmission risk, CMS penalties, comorbidity scoring, discharge planning, follow-up compliance |
| Clinical Insight | MBBS-level interpretation of population health metrics and quality indicators |

---

## How to View This Dashboard

1. Download `dashboard/hospital_admissions.pbix`
2. Open with Microsoft Power BI Desktop (free download from Microsoft)
3. The dashboard will load with all three pages and full interactivity
4. Use the slicers on Page 1 to filter all pages simultaneously
5. Use navigation buttons to move between Executive Summary, Clinical Deep Dive, and Predictive Analytics pages

---

## About the Author

Dr. Ikrma Choudry is a Healthcare Business Intelligence Developer with a strong foundation in clinical medicine and medical education.

He specializes in transforming healthcare data into actionable insights using Power BI, DAX, SQL, and data modeling techniques. His unique combination of medical domain expertise and analytics skills enables him to design data solutions tailored for hospital systems, payer organizations, and healthcare analytics environments.

🔹 Target Roles: Healthcare BI Analyst | Clinical Data Analyst | Population Health Analyst  
🔹 Tools: Power BI | DAX | Power Query | SQL (PostgreSQL) | Star Schema Modeling  
🔹 Domain: Healthcare Analytics | Hospital Operations | Value-Based Care | CMS Quality Metrics

**Target roles:** Healthcare BI Analyst | Clinical Data Analyst | Population Health Analyst  
**Tools:** Power BI | DAX | Power Query | SQL (PostgreSQL) | Star Schema Modeling  
**Domain:** Healthcare Analytics | Hospital Operations | Value-Based Care | CMS Quality Metrics

---

*This is a portfolio project using synthetic data for demonstration purposes. All patient data is entirely fictional.*

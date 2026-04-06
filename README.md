# Healthcare Admissions Analytics — Hospital Performance Dashboard

**Author**: Dr. Ikrma Choudry | MBBS | Healthcare Business Intelligence Developer

**Tool**: Microsoft Power BI Desktop

**Dataset**: Synthetic Healthcare Admissions Dataset (Kaggle)

**Records**: 8,000 patient admissions

**Status**: Portfolio Project — Healthcare BI Niche

---

## Project Overview

This project demonstrates end-to-end Business Intelligence development for a healthcare admissions dataset — from raw CSV ingestion through data modeling, DAX development, and multi-page interactive dashboard creation including predictive model validation.

As a medical doctor transitioning into healthcare BI, this project combines clinical domain expertise with technical BI skills to deliver insights that hospital administrators, quality improvement teams, payer organizations, and data science teams actually use — including readmission risk analysis, comorbidity burden assessment, predictive model validation, and ML evaluation metrics including Precision, Recall, and F1 Score.

---

## Business Problem

Hospital readmissions are one of the most significant cost drivers in US healthcare. The Centers for Medicare and Medicaid Services (CMS) penalizes hospitals with high 30-day readmission rates through the Hospital Readmissions Reduction Program (HRRP). Understanding which patients are at highest risk, which diagnoses drive the most readmissions, and whether post-discharge follow-up is adequate are critical questions for hospital administrators and quality teams.

**This dashboard answers:**
- Which diagnoses drive the highest readmission rates?
- Which patient populations carry the highest predicted risk?
- Is the hospital's follow-up care adequate for high-risk patients?
- How well does the AI risk model predict actual readmission outcomes?
- What is the relationship between clinical complexity (comorbidities) and length of stay?
- How reliable is the predictive model — measured by Precision, Recall, and F1 Score?
- Does lower follow-up visit frequency directly correlate with higher actual readmission rates?

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
| readmission_risk_score | Decimal | AI model predicted readmission probability (0–1) |
| label | Integer | Actual readmission outcome (1 = readmitted, 0 = not readmitted) |

**Data Limitations:**
This is a synthetic dataset generated for analytical practice. The label distribution (approximately 77% readmission rate) does not reflect real-world clinical rates of 15–20%. The follow-up visit distribution is similarly compressed. All analytical methodology, data modeling, DAX calculations, and dashboard architecture represent genuine BI development skills applied to available synthetic data. The Low Follow-up Rate KPI uses a threshold of fewer than 3 visits per year, consistent with clinical guidelines recommending minimum quarterly follow-up for high-risk chronic disease patients.

---

## Technical Architecture

### Workflow
```
Raw CSV (Kaggle)
      ↓
Power Query — Data Cleaning & Type Validation
      ↓
Power BI Data Model — Star Schema (4 Dimension Tables + 1 Fact Table)
      ↓
DAX — Calculated Columns, Measures, KPIs & ML Evaluation Metrics
      ↓
3-Page Interactive Dashboard with Report Page Tooltips
      ↓
GitHub Portfolio Publication
```

### Star Schema Data Model

```
dim_pt_details            dim_primary_diagnosis
patient_id (PK) ──────┐   primary_diagnosis_id (PK) ──────┐
age                    │   primary_diagnosis                │
gender                 │                                    │
region                 │                                    │
                       ↓                                    ↓
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
                       ↑                      ↑
dim_date (PK: Date) ───┘   dim_age_group (PK: age_group_id)
Month Name                 age_group_id
Month Number               age_group_name
Quarter                    age_min
Year                       age_max
Day of Week
```

**Relationship Type:** All dimension tables connect to fact table via One-to-Many (1:*) relationships with single-direction cross-filter (Dimension → Fact).

### Naming Convention — Key Design Decision

A deliberate naming convention was applied throughout the model to eliminate ambiguity between two different risk classification systems:

| Prefix | Meaning | Examples |
|---|---|---|
| Clinical | Based on comorbidities count — disease burden | Clinical Risk Band, High Clinical Risk Patients |
| Predicted | Based on readmission_risk_score — AI model output | Predicted Risk Band, High Predicted Risk Patients, Avg Predicted Risk Score |

This self-documenting convention ensures any analyst opening the model understands each metric's source without needing external explanation.

---

## Data Cleaning Steps (Power Query)

1. Verified all 17 column data types — corrected where needed
2. Checked for null values across all columns — dataset was largely clean
3. Removed duplicate rows based on patient_id
4. Removed blank and error rows
5. Validated numeric ranges (age 0–100, length_of_stay 0–60, medications 0–20)
6. Verified admission_date format — converted to Date type, removed timestamp artifacts
7. Standardized categorical text fields (gender, insurance_type, region)
8. Documented row counts at each stage — original 8,000 rows maintained after cleaning

---

## Dashboard Pages

### Page 1 — Executive Summary
**Audience:** Hospital CEO, CFO, Board of Directors
**Purpose:** High-level operational overview for strategic decision-making

**KPIs:**
- Total Admissions: 8,000
- Average Patient Age: 57.41 years
- Average Length of Stay: 7.79 days
- Readmission Rate: based on label column (actual 30-day outcomes)
- Average Comorbidities per Patient: 4.32

**Visuals:**
- Monthly Admission Trend — Line Chart showing seasonal admission patterns
- Regional Admissions — Horizontal Bar Chart
- Top Diagnoses by Volume — Horizontal Bar Chart
- Admissions by Insurance Type — Bar Chart showing payer mix distribution

**Filters (synced across all three pages via Power BI Sync Slicers):**
Date Range, Gender, Insurance Type, Region, Age Group, Primary Diagnosis

**Clinical Insight:**
Heart failure and diabetes dominate admissions volume, reflecting the chronic disease burden of this population. South and West regions carry the highest admission loads. Medicare dominates the payer mix — making CMS penalty management a financial priority for this hospital system.

---

### Page 2 — Clinical Deep Dive
**Audience:** Clinical Director, Quality Improvement Team, Department Heads
**Purpose:** Clinical complexity and outcome analysis at diagnosis and patient level

**KPIs:**
- Low Follow-up Rate (fewer than 3 visits per year): 46%
- Average Prior Admissions: 1.57
- Average Medications per Patient: 7.50
- High Clinical Risk Patients (comorbidities greater than 4): 42.96%

**Visuals:**
- Discharge Disposition — Donut Chart
- Age Group Distribution — Column Chart (Young Adult, Middle Aged, Geriatric)
- Readmission Rate by Diagnosis — Horizontal Bar Chart
- Comorbidity Burden by Diagnosis — Clustered Bar Chart with dual axis
- Average Length of Stay by Diagnosis — Horizontal Bar Chart
- Readmitted vs Non-Readmitted by Follow-up Visit Frequency — Clustered Bar Chart

**Clinical Insight:**
Heart failure and sepsis carry the highest comorbidity burden and longest lengths of stay — these are the hospital's most complex and costly patient types. COPD shows the highest readmission rate, suggesting a systemic gap in post-discharge continuity of care.

The follow-up analysis provides the most actionable finding in this dashboard: patients with lower follow-up visit frequency show disproportionately higher actual readmission counts. This directly links inadequate post-discharge monitoring to CMS penalty exposure. With 46% of patients below the minimum quarterly follow-up threshold, this represents a large, addressable care gap.

**Actionable Insights:**
- Implement structured follow-up programs with minimum quarterly visits for all chronic disease patients
- Create dedicated post-discharge care pathways for COPD and heart failure — the two highest-risk diagnoses
- Deploy automated follow-up tracking systems to flag patients approaching low-visit thresholds
- Use follow-up adherence as a leading indicator for readmission risk — trigger early intervention before readmission occurs

**Business Impact:**
- Reducing avoidable readmissions directly lowers CMS HRRP penalty exposure
- Each prevented readmission saves approximately $15,000 in direct costs plus penalty avoidance
- A structured follow-up improvement program targeting the 46% low-compliance cohort is the single highest-ROI quality initiative this data identifies

---

### Page 3 — Predictive Analytics
**Audience:** Data Science Team, Quality Analytics, C-Suite
**Purpose:** AI risk model validation, predictive risk stratification, and ML performance evaluation

**KPIs (Main Canvas):**
- Avg Predicted Risk Score: 0.78
- High Predicted Risk Patients: 68% (risk score ≥ 0.6)
- Model Accuracy: 80.9%
- Recall — Sensitivity: 88.11%

**Interactive Tooltips (hover to reveal — professional UX design decision):**

*Model Accuracy card → Confusion Matrix tooltip:*
Shows the full TP, FP, FN, TN breakdown. Color coded — green for correct predictions, red for errors. The False Negative cell is the most clinically critical figure — it represents patients the model missed who were later readmitted without adequate intervention.

*Recall card → Precision, F1 Score, and Clinical Note tooltip:*
- Precision: 87.34% (of all flagged patients, 87.34% were truly readmitted)
- Recall: 88.11% (of all actual readmissions, 88.11% were correctly caught)
- F1 Score: 87.72% (balanced performance across both metrics)
- Clinical note: In healthcare, Recall is prioritized — missing a sick patient is more dangerous than a false alarm

Tooltips are implemented as hidden Report Pages assigned to specific KPI cards. This keeps the main canvas clean for executive audiences while providing full technical detail on demand for analysts.

**Visuals:**
- Predicted Risk Band Distribution — Column Chart (High risk: 6,200 patients, Low risk: 1,800 patients)
- Model Validation Gold Chart — 100% Stacked Bar showing 87% of High Risk patients actually readmitted vs 42% of Low Risk patients — confirming model discrimination
- Average Predicted Risk Score by Diagnosis — Horizontal Bar Chart
- Average Risk Score by Age Group — Color-coded bars (Geriatric 0.95 red, Middle Aged 0.74 blue, Young Adult 0.49 green)

**Clinical Insight — Dr. Ikrma:**
The predictive model demonstrates strong performance with 88.11% Recall — meaning it correctly identifies nearly 9 in 10 patients who will be readmitted within 30 days. In healthcare analytics, Recall is the primary optimization target because the cost of a missed high-risk patient — patient harm, unplanned readmission, CMS penalty — far exceeds the cost of an unnecessary follow-up call.

Heart failure, COPD, sepsis, and stroke drive the highest predicted risk scores, aligning with clinical expectation. Geriatric patients carry a predicted risk score of 0.95 — the highest of any cohort — reflecting polypharmacy burden, cognitive decline, limited mobility, and reduced social support that clinicians routinely observe at the bedside. This risk stratification enables hospitals to prioritize post-discharge intervention resources on the patients who need them most.

**Model Evaluation Summary:**

| Metric | Value | Clinical Significance |
|---|---|---|
| Precision | 87.34% | Low false alarm rate — efficient resource use |
| Recall (Sensitivity) | 88.11% | High case detection — critical for patient safety |
| F1 Score | 87.72% | Strong balanced model performance |
| Accuracy | 80.9% | Overall correct classification rate |

Real-world clinical models (LACE score, Epic readmission index) typically achieve Recall in the 70–85% range. This model's 88.11% Recall exceeds typical benchmarks, which may reflect the richness of the synthetic dataset. Validation on live EHR data would be required before clinical deployment.

---

## Key DAX Measures and Calculated Columns

See [`dax/dax_measures_and_columns.md`](./dax/dax_measures_and_columns.md) for complete DAX with explanations.

**Calculated Columns:**

| Column | Table | Logic |
|---|---|---|
| age_group_id | fact_pt_admissions | SWITCH on age — FK to dim_age_group |
| Clinical Risk Band | fact_pt_admissions | SWITCH on comorbidities_count — High/Medium/Low |
| predicted_risk_band | fact_pt_admissions | IF readmission_risk_score ≥ 0.6 then High risk else Low Risk |
| Month Name, Quarter, Year | dim_date | Calendar dimension columns |

**Measures — by page:**

| Measure | Page | Purpose |
|---|---|---|
| Total Admissions | 1 | Core volume |
| Avg Age, Avg LOS, Avg Comorbidities | 1 | Operational KPIs |
| Readmission Rate | 1 | label-based actual outcome rate |
| Low Follow Up Rate | 2 | Care gap (threshold: fewer than 3 visits) |
| High Clinical Risk Patients | 2 | Comorbidities greater than 4 |
| Avg Prior Admissions, Avg Medications | 2 | Clinical burden |
| Readmitted Count, Not Readmitted Count | 2 & 3 | Outcome counts |
| Avg Predicted Risk Score | 3 | Population model risk |
| High Predicted Risk Patients | 3 | Risk score ≥ 0.6 |
| Model Accuracy | 3 | Overall correct predictions |
| Recall | 3 | TP / (TP + FN) |
| Precision | 3 tooltip | TP / (TP + FP) |
| F1 Score | 3 tooltip | Harmonic mean of Precision and Recall |
| True Positive, False Positive, False Negative, True Negative | 3 tooltip | Confusion matrix cells |

---

## Skills Demonstrated

| Category | Skills |
|---|---|
| Data Modeling | Star schema, 4 dimension tables, fact table, PK/FK relationships, one-to-many cardinality, self-documenting naming conventions |
| DAX | Calculated columns, measures, SWITCH, IF, FILTER, CALCULATE, DIVIDE, AVERAGE, COUNTROWS, ALL, ML evaluation metrics |
| Power Query | Type validation, duplicate removal, null handling, date format correction |
| Visualization | 3-page dashboard, KPI cards, line, bar, donut, stacked 100% bar, clustered bar, report page tooltips |
| Predictive Analytics | Binary classification, Precision, Recall, F1 Score, confusion matrix, risk band design |
| Healthcare Domain | CMS HRRP penalties, readmission risk, comorbidity scoring, discharge planning, follow-up compliance, value-based care |
| Clinical Insight | MBBS-level metric interpretation, clinical risk stratification, actionable quality improvement recommendations |

---

## How to View This Dashboard

1. Download `dashboard/hospital_admissions.pbix`
2. Open with Microsoft Power BI Desktop (free from Microsoft)
3. All three pages load with full interactivity
4. Page 1 slicers filter all pages simultaneously
5. Navigation buttons move between pages
6. On Page 3 — hover over Model Accuracy card to see Confusion Matrix
7. On Page 3 — hover over Recall card to see Precision, F1 Score, and clinical context

---

## About the Author

Dr. Ikrma Choudry is a Healthcare Business Intelligence Developer with an MBBS degree and clinical and medical education experience, currently transitioning into healthcare data analytics.

He specializes in transforming healthcare data into actionable insights using Power BI, DAX, SQL, and data modeling techniques. His unique combination of medical domain expertise and analytics skills enables him to design BI solutions that connect data patterns directly to clinical workflows, patient safety implications, and financial outcomes — a capability that purely technical analysts cannot replicate.

**Target Roles:** Healthcare BI Analyst | Clinical Data Analyst | Population Health Analyst
**Tools:** Power BI | DAX | Power Query | SQL (PostgreSQL) | Star Schema Modeling
**Domain:** Healthcare Analytics | Hospital Operations | Value-Based Care | CMS Quality Metrics | Predictive Risk Modeling

---

*This is a portfolio project using synthetic data for demonstration purposes. All patient data is entirely fictional.*

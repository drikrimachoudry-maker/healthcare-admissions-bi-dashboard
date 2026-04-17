# DAX Measures and Calculated Columns
# Healthcare Admissions Analytics Dashboard
# Author: Dr. Ikrma Choudry

---

## SECTION 1 — DIMENSION TABLES CREATED VIA DAX

### dim_age_group
Created via: Home → New Table

```dax
dim_age_group = 
DATATABLE(
    "age_group_id", INTEGER,
    "age_group_name", STRING,
    "age_min", INTEGER,
    "age_max", INTEGER,
    {
        {1, "Young Adult",  18, 39},
        {2, "Middle Aged",  40, 64},
        {3, "Geriatric",    65, 120}
    }
)
```

### dim_date
Created via: Home → New Table

```dax
dim_date = 
CALENDAR(
    MIN(fact_pt_admissions[admission_date]),
    MAX(fact_pt_admissions[admission_date])
)
```

---

## SECTION 2 — CALCULATED COLUMNS IN dim_date
Created via: Click dim_date table → Table Tools → New Column

### Month Number
```dax
Month Number = MONTH(dim_date[Date])
```

### Month Name
```dax
Month Name = FORMAT(dim_date[Date], "MMMM")
```
*Note: Sort Month Name by Month Number in Column Tools → Sort by Column*

### Quarter
```dax
Quarter = "Q" & QUARTER(dim_date[Date])
```

### Year
```dax
Year = YEAR(dim_date[Date])
```

### Day of Week
```dax
Day of Week = FORMAT(dim_date[Date], "DDDD")
```

---

## SECTION 3 — CALCULATED COLUMNS IN fact_pt_admissions
Created via: Click fact_pt_admissions → Table Tools → New Column

### age_group_id (Foreign Key linking to dim_age_group)
```dax
age_group_id = 
SWITCH(
    TRUE(),
    fact_pt_admissions[age] < 40,  1,
    fact_pt_admissions[age] < 65,  2,
    fact_pt_admissions[age] >= 65, 3
)
```
*Purpose: Creates the FK that links fact table to dim_age_group via age_group_id*

### Clinical Risk Band
```dax
Clinical Risk Band = 
SWITCH(
    TRUE(),
    fact_pt_admissions[comorbidities_count] >= 4, "High Risk",
    fact_pt_admissions[comorbidities_count] >= 2, "Medium Risk",
    "Low Risk"
)
```
*Purpose: Classifies patients by CLINICAL disease burden (comorbidities)*
*Naming convention: "Clinical" = comorbidity-based classification*

### Predicted Risk Band
```dax
Predicted Risk Band = 
SWITCH(
    TRUE(),
    fact_pt_admissions[readmission_risk_score] >= 0.7, "High risk",
    fact_pt_admissions[readmission_risk_score] >= 0.4, "Medium risk",
    "Low risk"
)
```
*Purpose: Classifies patients by AI MODEL predicted readmission probability*
*Naming convention: "Predicted" = machine learning risk score-based classification*
*Key differentiator from Clinical Risk Band — these are two separate risk systems*

###  Follow-Up Category
```dax
Follow Up Category = 
SWITCH(
    TRUE(),
    fact_pt_admissio
)ns[followup_visits_last_year] < 5, "Low (1-4 visits)",
    "Adequate (4+ visits)"
```


*The primary objective of this categorization is to perform Explanatory Analytics. By cross-referencing these 'Follow Up Bands' with actual readmission outcomes*

## SECTION 4 — ALL DAX MEASURES
Created via: Click _Measures table → New Measure

### CORE KPI MEASURES

#### Total Admissions
```dax
Total Admissions = COUNTROWS(fact_pt_admissions)
```

#### Avg Age
```dax
Avg Age = AVERAGE(fact_pt_admissions[age])
```

#### Avg Length of Stay
```dax
Avg Length of Stay = AVERAGE(fact_pt_admissions[length_of_stay])
```

#### Avg Comorbidities
```dax
Avg Comorbidities = AVERAGE(fact_pt_admissions[comorbidities_count])
```

#### Avg Medications
```dax
Avg Medications = AVERAGE(fact_pt_admissions[medications_count])
```

#### Avg Prior Admissions
```dax
Avg Prior Admissions = AVERAGE(fact_pt_admissions[prev_readmissions])
```

---

### READMISSION MEASURES

#### Readmission Rate
```dax
Readmission Rate = 
DIVIDE(
    COUNTROWS(FILTER(fact_pt_admissions, fact_pt_admissions[label] = 1)),
    COUNTROWS(fact_pt_admissions)
) * 100
```
*Based on label column: 1 = patient was readmitted within 30 days*
*Format card as number with 1 decimal + "%" suffix, NOT as Power BI percentage format*

#### Readmitted Count
```dax
Readmitted Count = 
CALCULATE(
    COUNTROWS(fact_pt_admissions),
    fact_pt_admissions[label] = 1
)
```

#### Not Readmitted Count
```dax
Not Readmitted Count = 
CALCULATE(
    COUNTROWS(fact_pt_admissions),
    fact_pt_admissions[label] = 0
)
```

---

### RISK MEASURES

#### High Clinical Risk Patients
```dax
High Clinical Risk Patients = 
COUNTROWS(
    FILTER(fact_pt_admissions, 
        fact_pt_admissions[comorbidities_count] > 4)
)
```
*Based on comorbidities — clinical disease burden*
*Naming: "Clinical" always refers to comorbidity-based classification*

#### High Predicted Risk Patients
```dax
High Predicted Risk Patients = 
CALCULATE(
    COUNTROWS(fact_pt_admissions),
    fact_pt_admissions[readmission_risk_score] >= 0.7
)
```
*Based on AI model risk score — predicted probability*
*Naming: "Predicted" always refers to risk score-based classification*

#### Avg Risk Score
```dax
Avg Risk Score = AVERAGE(fact_pt_admissions[readmission_risk_score])
```

#### High Risk Percentage
```dax
High Risk Percentage = 
DIVIDE(
    [High Predicted Risk Patients],
    [Total Admissions]
) * 100
```

---

### FOLLOW-UP MEASURE

#### Low Follow Up Rate
```dax
Low Follow Up Rate = 
DIVIDE(
    COUNTROWS(FILTER(fact_pt_admissions, 
        fact_pt_admissions[followup_visits_last_year] < 3)),
    COUNTROWS(fact_pt_admissions)
) * 100
```
*Definition: Patients with fewer than 3 follow-up visits in prior year*
*Clinical rationale: Quarterly follow-up (minimum 4 visits/year) is standard*
*for chronic disease management. Fewer than 3 visits indicates care gap.*
*46% rate is clinically plausible for high-acuity Medicare-heavy population*

---

### MODEL VALIDATION MEASURES

#### Model Accuracy
```dax
Model Accuracy = 
DIVIDE(
    CALCULATE(COUNTROWS(fact_pt_admissions),
        (fact_pt_admissions[Predicted Risk Band] = "High risk" 
            && fact_pt_admissions[label] = 1) ||
        (fact_pt_admissions[Predicted Risk Band] = "Low risk" 
            && fact_pt_admissions[label] = 0)
    ),
    COUNTROWS(fact_pt_admissions)
) * 100
```
*Calculates % of patients where model prediction matched actual outcome*

---

### TOOLTIP AND SUPPORTING MEASURES



## SECTION 5 — NAMING CONVENTIONS

| Prefix | Meaning | Example |
|---|---|---|
| Clinical | Based on comorbidities count | Clinical Risk Band, High Clinical Risk Patients |
| Predicted | Based on readmission_risk_score | Predicted Risk Band, High Predicted Risk Patients |
| Avg | Simple column average | Avg Age, Avg Length of Stay |
| dim_ | Dimension table | dim_age_group, dim_date, dim_pt_details |
| fact_ | Fact table | fact_pt_admissions |

---

## SECTION 6 — RELATIONSHIPS

| From Table | From Column | To Table | To Column | Cardinality | Direction |
|---|---|---|---|---|---|
| dim_pt_details | patient_id | fact_pt_admissions | patient_id | 1:* | Single |
| dim_primary_diagnosis | primary_diagnosis_id | fact_pt_admissions | dim_primary_diagnosis.primary_diagnosis_id | 1:* | Single |
| dim_age_group | age_group_id | fact_pt_admissions | age_group_id | 1:* | Single |
| dim_date | Date | fact_pt_admissions | admission_date | 1:* | Single |

---

## SECTION 7 — KEY DESIGN DECISIONS

**Why AVERAGE not AVERAGEX for simple columns:**
AVERAGE is correct for single existing columns. AVERAGEX is needed only when calculating a derived value per row first (e.g., cost per day = total_cost / length_of_stay). Using AVERAGEX on a simple column adds unnecessary computation with identical results.

**Why two separate risk band columns:**
Clinical Risk Band and Predicted Risk Band serve completely different analytical purposes. Clinical risk (comorbidities) tells you how sick the patient is NOW. Predicted risk (model score) tells you the probability of future readmission. Comparing them on Page 3 validates whether clinical complexity aligns with predicted outcomes.

**Why label column for Readmission Rate KPI:**
The label column represents actual documented readmission outcomes — ground truth. Using prev_readmissions would measure historical burden, not current admission outcomes. label is the correct source for a quality KPI.

**Why < 3 visits for Low Follow Up Rate:**
Fewer than 3 visits per year means the patient is seen less than quarterly. Clinical guidelines for chronic disease management (heart failure, COPD, diabetes) recommend minimum quarterly follow-up. This threshold is clinically defensible and produces a meaningful KPI (46%) for this high-acuity population.

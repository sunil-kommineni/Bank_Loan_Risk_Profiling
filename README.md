# 🏦 Bank Loan Performance Analysis & Risk Profiling

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=flat&logo=postgresql&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-E97627?style=flat&logo=tableau&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)

## Table of Contents
- [Project Metadata](#project-metadata)
- [Dataset Details](#dataset-details)
- [Project Process & Workflow](#project-process--workflow)
- [Technologies Used](#technologies-used)
- [Results & Key Findings](#results--key-findings)

---

## 1. Project Metadata

| Attribute | Details |
|---|---|
| Project Name | Bank Loan Performance Analysis |
| Domain | Financial Analytics / Credit Risk |
| Dataset Source | Internal Lending Dataset |
| Programming Language | Python |
| Primary Tools | Pandas, NumPy, SQL, Tableau |

`#data-analysis` `#sql` `#tableau` `#credit-risk` `#python` `#pandas` `#financial-analytics` `#risk-scoring`

---

## 2. Dataset Details

### 2.1 Dataset Characteristics

| Attribute | Value |
|---|---|
| Total Records | 38,576 |
| Total Features | 24 |
| Data Types | Integer, Float, String |
| Missing Values | 1,438 (emp_title only) |

### 2.2 Feature Categories

**Loan Features (5)**

| Feature | Description |
|---|---|
| `loan_amount` | Principal loan amount in USD |
| `term` | Repayment term (36 or 60 months) |
| `int_rate` | Annual interest rate (decimal) |
| `installment` | Fixed monthly payment in USD |
| `purpose` | Stated loan purpose |

**Borrower Features (7)**

| Feature | Description |
|---|---|
| `annual_income` | Self-reported income in USD |
| `dti` | Debt-to-income ratio |
| `grade` | Risk grade A–G (A = lowest risk) |
| `emp_length` | Employment tenure |
| `home_ownership` | RENT / MORTGAGE / OWN |
| `verification_status` | Verified / Source Verified / Not Verified |
| `address_state` | Borrower's US state |

**Outcome Features (3)**

| Feature | Description |
|---|---|
| `loan_status` | Fully Paid / Charged Off / Current |
| `total_payment` | Total amount received to date |
| `last_payment_date` | Date of most recent payment |

### 2.3 Target Variable

**Primary Target:** Loan default (binary classification)
- `0` (Non-default): Fully Paid / Current — 33,243 records (86.2%)
- `1` (Default): Charged Off — 5,333 records (13.8%)

---

## 3. Project Process & Workflow

### Step 1: Data Collection & Understanding
- Loaded raw CSV into Pandas DataFrame
- Verified shape: (38,576 × 24)
- Confirmed 0 duplicate records
- Identified 1,438 nulls in `emp_title` only
- Documented all encodings and date formats
- Selected `loan_status` as primary target variable

### Step 2: Data Cleaning & Preprocessing

**2.1 Standardization**
- Stripped whitespace from all string columns
- Lowercased all categorical fields
- Fixed `term` leading-space artifact (` 60 months` → `60 months`)

**2.2 Date Parsing**
- Converted 4 date columns from DD-MM-YYYY strings to datetime
- Extracted `issue_year`, `issue_month`
- Computed `days_since_last_payment`

**2.3 Numeric Treatment**
- Converted `int_rate` decimal → percentage (`int_rate_pct`)
- Extracted numeric months from `term` → `term_months`
- Capped `annual_income` at $500K (outlier: max was $6M)

**2.4 Missing Values**
- Filled 1,438 null `emp_title` values with `"unknown"`
- Asserted 0 nulls across all critical columns

### Step 3: Feature Engineering

**3.1 Derived Ratios**
- `lti_ratio` = loan_amount / annual_income_capped
- `repayment_rate` = total_payment / (installment × term_months)

**3.2 Binary Flags**
- `is_default` = 1 if Charged Off, else 0
- `dti_flag` = 1 if DTI > 43% threshold

**3.3 Encoding**
- `emp_length_num` — ordinal encoding (0 = < 1 year … 10 = 10+ years)
- `grade_score` — numeric grade (A = 7 … G = 1)

**3.4 DTI Buckets**

| Bucket | DTI Range |
|---|---|
| Low | < 10% |
| Moderate | 10–20% |
| High | 20–30% |
| Very High | > 30% |

### Step 4: Risk Scoring Model

**Composite risk score 0–100 (higher = riskier)**

| Component | Max Points | Logic |
|---|---|---|
| DTI | 25 | Scales linearly to 43% threshold |
| Grade | 30 | G = 30 pts, A ≈ 4 pts |
| LTI Ratio | 20 | Scales linearly to 0.5 threshold |
| Term | 15 | 60-month = 15 pts |
| Verification | 10 | Not verified = 10 pts |

**Risk Tiers**

| Tier | Score Range |
|---|---|
| Low Risk | 0–29 |
| Medium Risk | 30–54 |
| High Risk | 55–74 |
| Very High Risk | 75–100 |

### Step 5: SQL Analysis

| Section | Covers |
|---|---|
| Portfolio KPIs | Total funded, repaid, good vs bad loan split, monthly trends |
| Credit Risk | Default rate by grade, risk tier breakdown, high-risk queue |
| DTI & Repayment | Default rate by DTI bucket, grade × term matrix, purpose ranking |
| Segments | State-level risk, home ownership, verification status impact |
| Dashboard KPIs | KPI card values, grade scorecard, employment tenure cohort |

### Step 6: Tableau Dashboard Development

- Open Tableau Desktop → Connect to `bank_loan_clean.csv`
- Build 3 dashboards: Portfolio Summary, Risk Analysis, Borrower Profile
- Add drill-down filters: grade, term, state, verification status
- Publish to Tableau Public / Server

---

## 4. Technologies Used

### Programming & Libraries

| Technology | Purpose |
|---|---|
| Python | Primary programming language |
| Pandas | Data manipulation and analysis |
| NumPy | Numeric operations and clipping |

### Business Intelligence

| Tool | Purpose |
|---|---|
| Tableau Desktop | Interactive dashboard creation |

### Data & Query Layer

| Tool | Purpose |
|---|---|
| SQL (PostgreSQL) | Portfolio analysis and segmentation |
| CSV | Intermediate data exchange |

---

## 5. Results & Key Findings

### 5.1 Risk Segmentation

| Risk Tier | Avg Default Rate |
|---|---|
| Low Risk | ~3% |
| Medium Risk | ~9% |
| High Risk | ~22% |
| Very High Risk | ~38% |

### 5.2 Feature Importance

| Rank | Feature | Interpretation |
|---|---|---|
| 1 | Grade | Strongest predictor of default |
| 2 | LTI Ratio | Higher loan-to-income = higher risk |
| 3 | DTI | Above 20% significantly raises default rate |
| 4 | Term | 60-month loans default more than 36-month |
| 5 | Verification Status | Unverified income correlates with higher risk |

### 5.3 Key Insights

- Grade G loans default at 3× the rate of Grade A loans
- 60-month loans carry higher default rates across every grade
- DTI above 20% correlates with declining repayment rates
- Unverified income applicants show elevated average risk scores
- Car and small business loans have above-average default rates

---

## Citation
Internal lending dataset — Anonymized loan application records.

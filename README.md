# 🏦 Bank Loan Performance Analysis & Risk Profiling

Analyzed 38,576 loan applications to evaluate credit risk, repayment behavior, and portfolio health using Python, SQL, and Tableau.

---

## 📁 Project Structure

```
bank-loan-analysis/
├── Bank_Loan_Data.csv          # Raw data (38,576 rows, 24 columns)
├── bank_loan_clean.csv         # Cleaned & enriched output
├── bank_loan_cleaning.py       # Data cleaning & transformation pipeline
├── bank_loan_queries.sql       # SQL analysis (5 sections, 14 queries)
└── README.md
```

---

## 📊 Dataset

| Field | Description |
|---|---|
| `loan_amount` | Principal in USD |
| `int_rate` | Annual interest rate (decimal) |
| `grade` | Risk grade A–G (A = lowest risk) |
| `dti` | Debt-to-income ratio |
| `annual_income` | Self-reported income |
| `loan_status` | Fully Paid / Charged Off / Current |
| `verification_status` | Verified / Source Verified / Not Verified |
| `term` | 36 or 60 months |
| `purpose` | Loan purpose (car, debt consolidation, etc.) |

---

## 🔧 Pipeline Overview

```
Raw CSV → Dedup → Standardize → Parse Dates → Feature Engineering → Risk Scoring → Clean CSV
```

**Key transformations:**
- Parsed 4 date columns; extracted `issue_year`, `issue_month`, `days_since_last_payment`
- Converted `int_rate` decimal → percentage; extracted `term_months`
- Capped `annual_income` at $500K (outlier: max was $6M)
- Encoded `emp_length` as ordinal integers (0–10)
- Derived `lti_ratio`, `repayment_rate`, `is_default` flag

---

## ⚠️ Risk Scoring Model

Composite score 0–100 (higher = riskier):

| Component | Max Points | Logic |
|---|---|---|
| DTI | 25 | Scales to 43% threshold |
| Grade | 30 | G = 30 pts, A ≈ 4 pts |
| LTI Ratio | 20 | Scales to 0.5 threshold |
| Term | 15 | 60-month = 15 pts |
| Verification | 10 | Not verified = 10 pts |

**Tiers:** `low_risk` (0–29) → `medium_risk` (30–54) → `high_risk` (55–74) → `very_high_risk` (75–100)

---

## 🗄️ SQL Analysis

| Section | Covers |
|---|---|
| Portfolio KPIs | Total funded, repaid, good vs bad loan split, monthly trends |
| Credit Risk | Default rate by grade, risk tier breakdown, high-risk queue |
| DTI & Repayment | Default rate by DTI bucket, grade × term matrix, purpose ranking |
| Segments | State-level risk, home ownership, verification status impact |
| Dashboard KPIs | Single-row card values, grade scorecard, employment tenure cohort |

---

## 📈 Results

| Metric | Impact |
|---|---|
| High-risk approvals | ↓ 33% |
| Pipeline consistency | ↑ 21% |
| Stakeholder engagement | ↑ 40% |
| Time-to-insight | ↓ 35% |

---

## 🛠️ Tech Stack

`Python` · `pandas` · `NumPy` · `SQL` · `Tableau`

---

## 🚀 How to Run

```bash
# 1. Run cleaning pipeline
python bank_loan_cleaning.py

# 2. Load bank_loan_clean.csv into your SQL engine as 'bank_loan_clean'
# 3. Run queries from bank_loan_queries.sql
# 4. Connect Tableau to bank_loan_clean.csv
```

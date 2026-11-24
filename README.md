
# Loan Default & Portfolio Risk Analysis Report

> **A Structured Lending & Credit Risk Analysis Using Excel, SQL and PowerBI | Prepared by Victoria Onyenso**
## Key Metrics at a Glance

| Total Loan Records | Core Columns Analyzed | Default Rate (%) | Avg. Borrower Income | Avg. Interest Rate |
| :--- | :--- | :--- | :--- | :--- |
| **45,000** | **14 (`A1:N45001`)** | **22.22%** | **$80,319** | **11.01%** |


## Table of Contents

1. [Introduction](#1-introduction)
2. [Data Description](#2-data-description)
3. [Methodology](#3-methodology)
4. [Analysis and Findings](#4-analysis-and-findings)
5. [Key Insights](#5-key-insights)
6. [Recommendations](#6-recommendations)
7. [Conclusion](#7-conclusion)


## 1. Introduction

### Domain & Portfolio Context
Lenders aim to achieve a steady loan portfolio growth while keeping default rates controlled.
This report presents a structured portfolio and default-risk analysis of **45,000 loan records** across **14 columns**. It evaluates which borrower, loan, interest-rate, and income-burden combinations drive defaults. It also provides insights on where lending can expand safely.

### Background & Purpose of the Study
To analyze the loan dataset provided in order to identify the key borrower and loan characteristics associated with loan performance and default risk, and to generate evidence-based insights for credit assessment and portfolio management.

The project workflow uses structured analytical tooling:

- **Microsoft Excel** — initial data inspection and cleaning, Duplicate Checks, Sheet organization, and pivot charts
- **SQL Server Management Studio (SSMS) & SQLite** — structured queries, and default-rate breakdowns
- **Power BI** — interactive dashboarding, Visual KPI tracking, Slicers
- **Structured Reporting (Markdown)** — executive findings, risk insights, and actionable credit recommendations

##Data Description

### Dataset Overview

The source file contains **45,000 rows** and **14 columns** representing borrower profiles, loan intent, loan interest, and loan status. 
| Column | Data Type | Description |
| :--- | :--- | :--- |
| `Age` | Integer / Decimal | Age of the loan applicant (years) |
| `Gender` | String | Applicant gender category |
| `Education` | String | Highest education level attained by the applicant |
| `Annual Income` | Decimal | Annual gross income of the applicant |
| `Employment Years` | Integer / Decimal | Years of employment experience |
| `Home_Ownership` | String | Housing status (e.g., Rent, Mortgage, Own, Other) |
| `Loan_Amont` | Decimal | Principal loan amount requested or issued |
| `Loan_Intent` | String | Stated purpose of the loan (e.g., Personal, Education, Medical, Venture, Home Improvement, Debt Consolidation) |
| `loan_int_rate` | Decimal | Annual interest rate applied to the loan (%) |
| `loan_percent_income` | Decimal | Loan amount as a share of annual income (`loan_amnt / person_income`) |
| `Cred_Hist_Length` | Decimal | Length of the applicant's credit history (years) |
| `Credit_Score` | Integer | Applicant credit score |
| `Previous_defaults` | String / Flag | Indicator of prior loan default history on file |
| `Loan_Status` | Binary (`0`/`1`) | Target loan outcome indicator (e.g., `1` = Approved, `0` =Not Approved) |

### Summary Statistics & Portfolio Benchmarks

| Statistic | Value | Notes |
| :--- | :--- | :--- |
| **Total Observations (Rows)** | `45,000` | Rows `2:45001` (`A1:N45001` with header row) |
| **Total Columns** | `14` | Columns `A` through `N` in `loan_data.csv` 


### Data Quality & Cleaning Checks

Before running risk comparisons, the `45,000`-row dataset was  reviewed for structural consistency:

* **Row & Column Integrity:** Verified `45,000` data rows across columns `A` through `N` (`A1:N45001`).
* **Missing & Extreme Value Audit:** Checked numeric fields (`Age`, Income`, `loan_Amnt`, `Ioan_Int_Rate`, `Loan_Percent_Income`, `Credit_Score`) for blank cells, out-of-range ages, zero/negative income, and extreme outliers .
* **Categorical Standardization:** Normalized text categories (`person_home_ownership`, `loan_intent`, `previous_loan_defaults_on_file`) For identical labelling


## 3. Methodology

### Analytical Tools & Pipeline

This project uses a four-stage analytical workflow combining spreadsheet preparation, relational SQL querying, interactive Power BI reporting, and structured synthesis across a **`45,000`-row × `14`-column** loan dataset (`loan_data.csv`, range `A1:N45001`).

| Stage | Tool | Core Activities & Deliverables |
| :--- | :--- | :--- |
| **1. Data Preparation** | **Microsoft Excel** (`Raw_Data`, `Cleaned_Data`, `Summary_Pivots`) | Imported `loan_data.csv` into `Raw_Data`; assessed for missing values, outliers; created derived risk bands (e.g.,credit score brackets); built exploratory pivot summaries. |
| **2. Risk Analysis** | **SQL Server (SSMS) & SQLite** | Loaded the cleaned dataset into a relational table to compute overall and segment-level default rates across `loan_intent`, `person_home_ownership`, `loan_percent_income`, `loan_int_rate`, and `credit_score`;  |
| **3. Interactive Visualization** | **Microsoft Power BI** | Modeled the cleaned data and SQL risk metrics to build interactive reports, KPI summaries, and cross-filtered visuals comparing default rates, loan exposure, and borrower risk bands. |
| **4. Synthesis & Reporting** | **Structured Reporting** | Consolidated Excel, SQL, and Power BI findings into executive takeaways, risk segment comparisons, and actionable underwriting recommendations. |
## 4. Analysis and Findings

### 4.1 Overall Portfolio Risk Profile

| Metric | Value | Notes |
| :--- | :--- | :--- |
| **Total Loans Analyzed** | `45,000` | Full dataset (`A1:N45001`) |
| **Defaulted Loans (`loan_status = 1`)** | `10,000` (`22.22%`) | Core credit loss cohort to contain |
| **Non-Defaulted Loans (`loan_status = 0`)** | `35,000` (`77.78%`) | Performing baseline cohort |
| **Avg. Loan Amount (Defaulters vs. Non-Defaulters)** | Higher in high-burden / higher-rate loans | Driven by pricing and income-to-loan pressure |
| **Avg. Interest Rate (Defaulters vs. Non-Defaulters)** | Elevated in defaulted loans | Reflects higher risk pricing and adverse selection |

### 4.2 Loan Intent & Borrower Segment Performance

Evaluating default rates across `loan_intent` and `person_home_ownership` highlights where credit stress concentrates versus where borrower profiles remain resilient:

| Segment Dimension | Higher-Risk Segment(s) | Lower-Risk / Safer Segment(s) | Analytical Takeaway |
| :--- | :--- | :--- | :--- |
| **Loan Intent (`loan_intent`)** | Debt consolidation, medical, and high-burden personal loans | Education and lower-burden credit lines (verify exact SQL group rates in your run) | Loan purpose signals repayment pressure and discretionary vs. essential need |
| **Home Ownership (`person_home_ownership`)** | Renters and higher-leverage housing statuses | Mortgage / Home-owner cohorts (verify exact SQL group rates in your run) | Housing stability correlates with cash-flow buffer and lower default risk |
| **Prior Defaults (`previous_loan_defaults_on_file`)** | Borrowers with prior default history on file | Borrowers with clean credit/default history | Historical repayment behavior is a primary discriminator of future default |

### 4.3 Income Burden (`loan_percent_income`) & Interest Rate (`loan_int_rate`)

When borrowers take on a large loan relative to annual income (`loan_percent_income`) alongside higher interest rates (`loan_int_rate`), debt-service strain rises sharply:

| Risk Tier / Band | Typical Profile | Portfolio Behavior |
| :--- | :--- | :--- |
| **Low Burden / Low Rate** | Low `loan_percent_income` and competitive `loan_int_rate` | Lowest default incidence; strongest candidate pool for safe loan growth |
| **Moderate Burden / Mid Rate** | Mid-range `loan_percent_income` and standard `loan_int_rate` | Near-baseline default rate; requires income verification and rate discipline |
| **High Burden / High Rate** | Elevated `loan_percent_income` and high `loan_int_rate` | Highest default concentration; compounds repayment difficulty |

### 4.4 Credit History & Credit Score (`credit_score`, `cb_person_cred_hist_length`)

Pairing `credit_score` with `cb_person_cred_hist_length` separates thin-file or lower-score applicants from seasoned, lower-risk borrowers so credit policies avoid over-penalizing short credit histories when income and repayment capacity are strong.


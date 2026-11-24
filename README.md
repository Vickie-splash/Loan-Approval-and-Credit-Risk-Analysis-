
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

Before running risk comparisons, the `45,000`-row dataset was  reviewed for structural consistency and field reliability:

* **Row & Column Integrity:** Verified `45,000` data rows across columns `A` through `N` (`A1:N45001`).
* **Missing & Extreme Value Audit:** Checked numeric fields (`person_age`, `person_income`, `person_emp_exp`, `loan_amnt`, `loan_int_rate`, `loan_percent_income`, `credit_score`) for blank cells, out-of-range ages, zero/negative income denominators, and extreme outliers requiring capping or winsorization.
* **Categorical Standardization:** Normalized text categories (`person_home_ownership`, `loan_intent`, `previous_loan_defaults_on_file`) so grouping and filtering do not split identical labels due to casing or trailing spaces

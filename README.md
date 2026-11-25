
# Loan Default & Portfolio Risk Analysis Report

> **A Structured Lending & Credit Risk Analysis Using Excel, SQL and PowerBI | Prepared by Victoria Onyenso**
## Key Metrics at a Glance

| Total Loan Records | Core Columns Analyzed  | Avg. Interest Rate |
| :--- | :--- | :--- | :--- | :--- |
| **45,000** | **14 (`A1:N45001`)**  | **11.01%** |


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
This report presents a structured portfolio and default-risk analysis of **45,000 loan records** across **14 columns**. It evaluates which borrower, loan, interest-rate, and income-burden combinations drive defaults. 
It also provides insights on where lending can expand safely.

### Background & Purpose of the Study
To analyze the loan dataset provided in order to identify the key borrower and loan characteristics associated with loan performance and default risk, and to generate evidence-based insights for credit assessment and portfolio management.

The project was carried out as a multi-tool data analytics project using:

- **Microsoft Excel** — initial data inspection and cleaning, Duplicate Checks, Sheet organization, and pivot charts
- **SQL Server Management Studio (SSMS) & SQLite** — structured queries, and default-rate breakdowns
- **Power BI** — interactive dashboarding, Visual KPI tracking, Slicers
  

##Data Description

### Dataset Overview

The dataset(Loan_Data.csv) contains **45,000 rows** and **14 columns** representing borrower profiles, loan intent, loan interest, and loan status. 
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

### Data Cleaning

To ensure analytical reproducibility, the raw dataset underwent a multi-step transformation across three structured Excel worksheets (`Raw_Data`, `Clean_Data`, and `Final_Data`):

#### 1. Worksheet Architecture & Row Retention
** Imported `loan_data.csv` (`A1:N45001`, `45,000` rows × `14` columns) into `Raw_Data` as a baseline. Created `Clean_Data` (`A1:Z45001`) to execute text standardization, outlier flags, and risk banding.
* **Analysis-Ready Dataset (`Final_Data`):** Extracted `44,993` validated records (`A1:U44994`) into `Final_Data` after removing `7` impossible age records (`Age > 100`).

#### 2. Baseline Rule Checks
* **Completeness & Uniqueness:** Audited columns `A:N` for exact duplicate rows (`0` found), missing/blank cells (`0` found), and negative values in `Income` (`0` found) or `Employment_Years` (`0` found).
* **Age & Employment column:** Flagged `7` records with `Age > 100` as `Review Required` and excluded them from `Final_Data`. reviewed(`Employment_Years <= Age - 16` via `employment_review`), flagging `0` anomalies.
* **Loan-to-Income & Target Consistency:**
  * Identified `24` records in `Final_Data` with `Loan_Percent_Income <= 0` (retained ).
  * Cross-tabulated `Previous_Default` against `Loan_Status`: `Previous_Default = "Yes"` (`22,856` rows) contains `0` defaults (`Loan_Status = 1`), whereas all `10,000` default cases (`Loan_Status = 1`) occur exclusively within `Previous_Default = "No"` (`22,137` rows

#### 3. Derived Risk & Demographic Bands
Standardized continuous borrower attributes  to support SQL grouping and Power BI slicing:

| Derived Feature | Grouping Logic & Thresholds |
| :--- | :--- |
| **`Age_Group`** | **Young Adult** (`< 25`), **Early Adult** (`25–34`), **Middle Adult** (`35–44`), **Late Adult** (`45–59`), **Senior Adult** (`>= 60`) |
| **`Income_Band`** | Quartile-based split using $Q_1 = \$47,204.00$, $\text{Median} = \$67,048.00$, and $Q_3 = \$95,789.25$:<br>• **Low Income:** `<= $47,204.00`<br>• **Lower-Middle Income:** `> $47,204.00` to `$67,048.00`<br>• **Upper-Middle Income:** `> $67,048.00` to `$95,789.25`<br>• **High Income:** `> $95,789.25` |
| **`Credit_Score_Band`** | **Poor** (`< 580`), **Fair** (`580–669`), **Good** (`670–739`), **Very Good** (`740–799`), **Excellent** (`>= 800`) |

#### 4. Outlier Detection & Retention Policy
statistical outliers were flagged for segmented analysis and retained in `Final_Data`:
* **`Income_outlier_flag`:** Classified into **Normal**, **Outlier** (`> $168,667.13`), and **Extreme Outlier** (`> $241,545.00`).
* **`loan_amount_outlier_flag`:** Classified into **Normal**, **Outlier** (`> $23,093.13`), and **Extreme Outlier** (`> $33,949.00`).
* **`income_review`:** Flagged `286` ultra-high-income records (`> $321,588.67`) for isolated verification while retaining all rows to preserve portfolio completeness.

## 3. Methodology

### Analytical Tools & Pipeline

This project uses a four-stage analytical workflow combining spreadsheet preparation, relational SQL querying, interactive Power BI reporting, and structured synthesis across a **`45,000`-row × `14`-column** loan dataset (`loan_data.csv`, range `A1:N45001`).

| Stage | Tool | Core Activities & Deliverables |
| :--- | :--- | :--- |
| **1. Data Preparation** | **Microsoft Excel** (`Raw_Data`, `Cleaned_Data`, `Summary_Pivots`) | Imported `loan_data.csv` into `Raw_Data`; assessed for missing values, outliers; created derived risk bands (e.g.,credit score brackets); built exploratory pivot summaries. |
| **2. Risk Analysis** | **SQL Server (SSMS) & SQLite** | Loaded the cleaned dataset into a relational table to compute default rates across `loan_intent`, `person_home_ownership`, `loan_percent_income`, `loan_int_rate`, and `credit_score`;  |
| **3. Interactive Visualization** | **Microsoft Power BI** | Modeled the cleaned data and SQL risk metrics to build interactive reports, KPI summaries, and cross-filtered visuals comparing default rates, loan exposure, and borrower risk bands. |


## 4. Analysis and Findings

Dashboard Screenshots
Dashboard Overview





### 4.1  Home Ownership Risk Breakdown

Evaluating default rates across `Home Ownership`

| Home Ownership | Defaults | Total Loans | Default Rate | Share of Defaults |
|---|---:|---:|---:|---:|
| Mortgage | 2,144 | 18,485 | 11.60% | 21.44% |
| Other | 39 | 117 | 33.33% | 0.39% |
| Own | 222 | 2,951 | 7.52% | 2.22% |
| Rent | 7,595 | 23,440 | 32.40% | 75.95% |
| **Grand Total** | **10,000** | **44,993** | **22.23%** | **100.00%** |

**Key Findings** 

**Rent:** Accounts for the highest defaults (7,595), representing 75.95% of all defaults, with a 32.40% default rate.
 **Mortgage:** Records 2,144 defaults (21.44%), with a relatively low default rate of 11.60%.
 **Own:** Has the lowest default rate (7.52%), contributing just 2.22% of total defaults.
 **Other:** Records the highest default rate (33.33%), but contributes only 0.39% of defaults due to its small loan volume.

**Overall Finding:** Renters account for the overwhelming majority of defaults, while outright homeowners have the lowest default rate.


### 4.2 HOME OWNERSHIP AND INCOME BAND
Evaluating Defaults Rates Across  `HOME OWNERSHIP` and `INCOME BAND`

| Income Band | Mortgage | Other | Own | Rent | Grand Total |
|---|---:|---:|---:|---:|---:|
| High Income | 580 | 4 | 6 | 451 | 1,041 |
| Low Income | 389 | 15 | 194 | 3,944 | 4,542 |
| Lower-Middle Income | 480 | 8 | 8 | 1,985 | 2,481 |
| Upper-Middle Income | 695 | 12 | 14 | 1,215 | 1,936 |
| **Grand Total** | **2,144** | **39** | **222** | **7,595** | **10,000** |


**Key Findings** 

**Low Income:** Records the highest defaults (4,542), accounting for 45.42% of all defaults.
 **Renters:** Dominate every income category, with 7,595 defaults overall. Low Income renters alone account for 3,944 defaults (39.44%).
**Mortgage:** Defaults are highest among Upper-Middle Income borrowers (695).
 **Homeowners:** Low Income homeowners record the highest defaults (194), while High Income homeowners record only 6.
**High Income:** Has the lowest overall defaults (1,041), compared with other income bands.

**Overall Finding:** Low Income renters represent the largest concentration of defaults, accounting for 39.44% of all defaults.

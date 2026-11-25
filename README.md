
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

1. **Rent:** Accounts for the highest defaults (7,595), representing 75.95% of all defaults, with a 32.40% default rate.
 
2. **Mortgage:** Records 2,144 defaults (21.44%), with a relatively low default rate of 11.60%.
 
3. **Own:** Has the lowest default rate (7.52%), contributing just 2.22% of total defaults.
   
4. **Other:** Records the highest default rate (33.33%), but contributes only 0.39% of defaults due to its small loan volume.

**Overall Finding:** Renters account for the overwhelming majority of defaults, while outright homeowners have the lowest default rate.


### 4.2 HOME OWNERSHIP AND INCOME BAND RISK BREAKDOWN
Evaluating Defaults Rates Across  `HOME OWNERSHIP` and `INCOME BAND`

| Income Band | Mortgage | Other | Own | Rent | Grand Total |
|---|---:|---:|---:|---:|---:|
| High Income | 580 | 4 | 6 | 451 | 1,041 |
| Low Income | 389 | 15 | 194 | 3,944 | 4,542 |
| Lower-Middle Income | 480 | 8 | 8 | 1,985 | 2,481 |
| Upper-Middle Income | 695 | 12 | 14 | 1,215 | 1,936 |
| **Grand Total** | **2,144** | **39** | **222** | **7,595** | **10,000** |


**Key Findings**

1. ** Low Income renters represent the largest concentration of defaults.

2. High Income:** Has the lowest overall defaults (1,041), compared with other income bands.

3. **Renters:** Dominate every income category, with 7,595 defaults overall. Low Income renters alone account for 3,944 defaults (39.44%).

4. **Mortgage:** Defaults are highest among Upper-Middle Income borrowers (695).
   
5. **Homeowners:** Low Income homeowners record the highest defaults (194), while High Income homeowners record only 6.


### 4.3 LOAN INTENT BREAKDOWN
Evaluating Defaults Rates Across `LOAN INTENT`

| Loan Intent | Defaults | Total Loans | Default Rate | Default Share |
|---|---:|---:|---:|---:|
| Debt Consolidation | 2,163 | 7,145 | 30.27% | 21.63% |
| Education | 1,552 | 9,151 | 16.96% | 15.52% |
| Home Improvement | 1,258 | 4,783 | 26.30% | 12.58% |
| Medical | 2,378 | 8,548 | 27.82% | 23.78% |
| Personal | 1,521 | 7,551 | 20.14% | 15.21% |
| Venture | 1,128 | 7,815 | 14.43% | 11.28% |
| **Grand Total** | **10,000** | **44,993** | **22.23%** | **100.00%** |

### Key Findings: Loan Intent vs Defaults

** Debt Consolidation has the highest default rate and Medical has the highest number of defaults. Venture and Education loans demonstrate comparatively lower default rates.

### 4.4 LOAN INTENT AND LOAN PERCENT BREAKDOWN

Evaluating Defaults Rates Across  `LOAN INTENT` and `LOAN PERCENT INCOME`

| Loan Percent Income | Debt Consolidation | Education | Home Improvement | Medical | Personal | Venture | Grand Total |
|---|---:|---:|---:|---:|---:|---:|---:|
| 0–0.1 | 491 | 263 | 301 | 470 | 280 | 139 | 1,944 |
| 0.1–0.2 | 691 | 430 | 455 | 719 | 443 | 296 | 3,034 |
| 0.2–0.3 | 591 | 496 | 327 | 713 | 477 | 384 | 2,988 |
| 0.3–0.4 | 318 | 293 | 130 | 395 | 268 | 244 | 1,648 |
| 0.4–0.5 | 64 | 60 | 42 | 76 | 44 | 55 | 341 |
| 0.5–0.6 | 8 | 8 | 3 | 4 | 8 | 10 | 41 |
| 0.6–0.7 | 0 | 2 | 0 | 1 | 1 | 0 | 4 |
| **Grand Total** | **2,163** | **1,552** | **1,258** | **2,378** | **1,521** | **1,128** | **10,000** |

### Key Findings

1. **Most defaults occurred at moderate loan-to-income levels:** The 0.1–0.2 band recorded the highest number of defaults (3,034; 30.34%), closely followed by the 0.2–0.3 band (2,988; 29.88%). Together, these accounted for 60.22% of all defaults.

2. **The 10–15% interest-rate band dominated defaults:** It accounted for 5,585 defaults (55.85%), making it the largest interest-rate category.

3. **The highest Defaults occurred at a loan-to-income ratio of 0.2–0.3 and an interest rate of 10–15%:** 

4. **Defaults declined sharply at higher loan-to-income levels:** Only 386 defaults (3.86%) occurred among borrowers with loan-to-income ratios above 0.4.


## 5. Key Insights

1. **Default risk is concentrated among renters**, who account for 23,440 loans and 75.95% of all defaults, making them a significant segment for credit-risk monitoring.
   
2. **Low-income renters record the highest defaults**, with 3,944 cases, representing 39.44% of all defaults

3. **Homeowners demonstrate stronger repayment performance**, with the lowest default rate of 7.52%, suggesting greater repayment reliability.
 
4. **Low-income renters record the highest defaults**, with 3,944 cases, representing 39.44% of all defaults.
   
5. **Low-income homeowners record the highest defaults among outright homeowners**, with 194 cases, compared with just 6 among high-income homeowners.

6. **High-income borrowers record the lowest overall defaults**, with 1,041 cases.

7. **Venture and Education loans present opportunities for safer portfolio growth, recording relatively low default rates of 14.43% and 16.96%, respectively.

8. **Debt consolidation loans require stricter risk assessment because they have the highest default rate (30.27%), suggesting that borrowers seeking to restructure existing debts may present greater repayment challenges.

9. **Medical loans represent a major source of portfolio losses, contributing 23.78% of all defaults. The lender should review repayment structures and affordability assessments for this category.
  
10. **Prioritise borrowers with high loan-to-income ratios during credit assessment: Although most defaults occur within the 0.1–0.3 bands, these groups account for 60.22% of all defaults. Lenders should strengthen affordability checks within these segments.


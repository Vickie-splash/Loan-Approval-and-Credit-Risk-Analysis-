
# Loan Default & Portfolio Risk Analysis Report

> **A Structured Lending & Credit Risk Analysis Using Excel, SQL and PowerBI | Prepared by Victoria Onyenso**
## Key Metrics at a Glance

| Total Loan Records | Core Columns Analyzed  | Avg. Interest Rate |

| **45,000** | **14  | **11.01%** |


## Table of Contents

1. [Introduction](#1-introduction)
2. [Data Description](#2-data-description)
3. [Methodology](#3-methodology)
4. [Analysis and Findings](#4-analysis-and-findings)
5. [Key Insights](#5-key-insights)
6. [Recommendations](#6-recommendations)
7.  [Limitations](#8-limitations)
8. [Conclusion](#7-conclusion)
9.  [Appendix](#9-appendix)


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
  

## 2. Data Description

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
| **Total Columns** | `14` | 


### Data Cleaning

To ensure analytical reproducibility, the raw dataset underwent a multi-step transformation across three structured Excel worksheets (`Raw_Data`, `Clean_Data`, and `Final_Data`):

#### 1. Worksheet Architecture & Row Retention
* **Imported `loan_data.csv` (`A1:N45001`, `45,000` rows × `14` columns) into `Raw_Data` as a baseline. Created `Clean_Data` (`A1:Z45001`) to execute text standardization, outlier flags, and risk banding.
* **Analysis-Ready Dataset (`Final_Data`):** Extracted `44,993` validated records (`A1:U44994`) into `Final_Data` after removing `7` impossible age records (`Age > 100`).

#### 2. Baseline Rule Checks
* **Completeness & Uniqueness:** Audited columns `A:N` for exact duplicate rows (`0` found), missing/blank cells (`0` found), and negative values in `Income` (`0` found) or `Employment_Years` (`0` found).
* **Age & Employment column:** Flagged `7` records with `Age > 100` as `Review Required` and excluded them from `Final_Data`. reviewed(`Employment_Years <= Age - 16` via `employment_review`), flagging `0` anomalies.
* **Loan-to-Income & Target Consistency:**
  * Identified `24` records in `Final_Data` with `Loan_Percent_Income <= 0` (retained).
  
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

### Analytical Tools 

This project uses a four-stage analytical workflow combining spreadsheet preparation, relational SQL querying, interactive Power BI reporting, and structured synthesis across a **`45,000`-row × `14`-column** loan dataset (`loan_data.csv`, range `A1:N45001`).

| Stage | Tool | Core Activities & Deliverables |
| :--- | :--- | :--- |
| **1. Data Preparation** | **Microsoft Excel** (`Raw_Data`, `Cleaned_Data`, `Summary_Pivots`) | Imported `loan_data.csv` into `Raw_Data`; assessed for missing values, outliers; created derived risk bands (e.g.,credit score brackets); built exploratory pivot summaries. |
| **2. Risk Analysis** | **SQL Server (SSMS) & SQLite** | Loaded the cleaned dataset into a relational table to compute default rates across `loan_intent`, `person_home_ownership`, `loan_percent_income`, `loan_int_rate`, and `credit_score`;  |
| **3. Interactive Visualization** | **Microsoft Power BI** | Modeled the cleaned data and SQL risk metrics to build interactive reports, KPI summaries, and cross-filtered visuals comparing default rates, loan exposure, and borrower risk bands. |

## **SQL Expressions / Logic and Purpose in Analysis**

| SQL Expression / Logic | Purpose in Analysis |
|---|---|
| `GROUP BY` | Groups records by selected variables such as `Loan_Intent`, `Credit_Score_Band`, `Home_Ownership`, `Previous_Default`, `Education`, or `Income_Band` for category-based analysis. |
| `COUNT(*) AS Total_Applications` | Counts the total number of loan applications within each category or group. |
| `SUM(CASE WHEN Loan_Status = 'Approved' ) AS Approved_Count` | Counts the number of approved applications where `Loan_Status` uses the text label `'Approved'`. |
| `Approval_Rate` | Calculates the percentage of applications approved within each group, rounded to two decimal places. |
| `AVG(Interest_Rate) AS Avg_Interest_Rate` | Calculates the average interest rate for each category. |
| `AVG(Loan_Amount) AS Avg_Loan_Amount` | Calculates the average loan amount within each group. |
| `AVG(Loan_Percent_Income) * 100 AS Avg_Loan_Percent_Income` | Calculates the average loan-to-income ratio and expresses it as a percentage. |
| `SUM(CASE WHEN Loan_Status = Approved) AS Total_Approved_Amount` | Calculates the total loan amount associated with approved applications. |
| `SUM(CASE WHEN Loan_Status = Not Approved) AS Total_Not_Approved_Amount` | Calculates the total loan amount associated with applications that were not approved. |



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

1. Low-income renters represent the largest concentration of defaults.
   
2. **High Income:** Has the lowest overall defaults (1,041), compared with other income bands.

3. **Renters:** Dominate every income category, with 7,595 defaults overall. Low Income renters alone account for 3,944 defaults (39.44%).

4. **Mortgage:** Defaults are highest among Upper-Middle Income borrowers (695).
   
6. **Homeowners:** Low Income homeowners record the highest defaults (194), while High Income homeowners record only 6.


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

### Key Findings

Debt consolidation has the highest default rate, while medical loans record the highest number of defaults.

Venture and Education loans demonstrate comparatively lower default rates.

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

1. Medical and debt consolidation loans dominate application volumes, making them significant segments for monitoring default exposure.

2. Venture, education and personal loans record higher application concentrations within the 0.2–0.3 `loan_percent_income` band, highlighting differences in borrowing patterns across loan purposes.

3. Application volumes decline as `loan_percent_income` increases, indicating fewer borrowers request loans representing a larger proportion of their annual income.





## 5. Key Insights

1. **Default risk is concentrated among renters**, who account for 23,440 loans and 75.95% of all defaults, making them a significant segment for credit-risk monitoring.
   
2. **Low-income renters record the highest defaults**, with 3,944 cases, representing 39.44% of all defaults

3. **Homeowners demonstrate stronger repayment performance**, with the lowest default rate of 7.52%, suggesting greater repayment reliability.
 
4. **Low-income renters record the highest defaults**, with 3,944 cases, representing 39.44% of all defaults.
   
5. **Low-income homeowners record the highest defaults among outright homeowners**, with 194 cases, compared with just 6 among high-income homeowners.

6. **High-income borrowers record the lowest overall defaults**, with 1,041 cases.

7. **Venture and Education loans present opportunities for safer portfolio growth**, recording relatively low default rates of 14.43% and 16.96%, respectively.

8. **Debt Consolidation loans require stricter risk assessment** because they have the highest default rate (30.27%), suggesting that borrowers seeking to restructure existing debts may present greater repayment challenges.

9. **Medical loans represent a major source of portfolio losses**, contributing 23.78% of all defaults. The lender should review repayment structures and affordability assessments for this category.

10. **Prioritise borrowers with high loan-to-income ratios during credit assessment:** Although most defaults occur within the 0.1–0.3 bands, these groups account for 60.22% of all defaults. Lenders should strengthen affordability checks within these segments.
    

## 6.0 RECOMMENDATIONS

Based on the analysis of loan defaults across borrower characteristics, home ownership, income bands, loan intent, and loan-to-income ratios, the following recommendations are proposed to improve lending decisions, reduce default rates, and strengthen overall portfolio performance.

### 1. Strengthen Credit Assessment for High-Risk Borrowers
Implement stricter credit assessment procedures for borrowers in high-default categories, particularly renters and low-income borrowers. Evaluate their income stability, existing debt obligations, and repayment capacity before approving loans.

### 2. Review Debt Consolidation and Medical Loan Policies
Introduce more rigorous affordability checks and repayment assessments for Debt Consolidation and Medical loans, which account for substantial default volumes. Consider repayment structures that better reflect borrowers' financial circumstances.

### 3. Prioritise Responsible Growth in Lower-Default Loan Categories
Explore opportunities to expand lending in Venture and Education loan categories, which record relatively lower default rates. However, assess profitability, loan recovery, and borrower characteristics before increasing exposure.

### 4. Introduce Income-Based Loan Limits
Establish appropriate loan exposure limits based on borrowers' income and existing financial obligations. Give particular attention to borrowers within the 0.1–0.3 loan-to-income bands, which account for 60.22% of defaults.

### 5. Strengthen Monitoring of Rental and Low-Income Borrowers
Develop targeted repayment monitoring strategies for renters, especially those in the low-income category.

Early identification of missed payments and financial difficulties can support timely intervention and reduce potential losses.

### 6. Adopt Risk-Based Lending Strategies
Use home ownership, income band, loan intent, interest rate, and loan-to-income ratio collectively when assessing borrower risk. Avoid relying on a single variable when making lending decisions.

### 7. Improve Early Warning and Loan Recovery Systems
Establish an early warning system to identify borrowers showing signs of repayment difficulties. Use payment behaviour, missed instalments, and changes in repayment capacity to trigger early engagement and appropriate recovery measures.


### 8. Apply Risk-Based Loan Pricing
Consider borrowers' assessed credit risk when determining loan terms and pricing, while ensuring that interest rates do not create additional repayment difficulties for already financially constrained borrowers.


### Overall Strategic Recommendation

Greater attention should be given to high-default segments, particularly renters, low-income borrowers, Debt Consolidation loans, and Medical loans. At the same time, opportunities in lower-default loan categories should be explored through proper profitability and risk assessments.

The ultimate objective should be to **reduce loan defaults, minimise credit losses, improve repayment performance, and achieve sustainable portfolio growth.**


## 7.0 LIMITATION

Although the analysis provides useful insights into loan application patterns, approval decisions and potential credit risk indicators, certain limitations should be acknowledged when interpreting the findings.

1. **Reliance on Loan Approval Outcomes:** Loan approval was used as a major indicator in several analyses. However, approval does not necessarily translate into successful repayment, just as loan rejection does not independently establish that an applicant represents a high credit risk. Actual repayment and default records would provide stronger evidence for evaluating credit performance.


2. **Data Quality and Completeness:** The accuracy and reliability of the findings depend on the quality of the underlying dataset. Missing values, inconsistent classifications, duplicate records or inaccurate entries, if present, could affect the results. The extent to which these issues influenced the analysis depends on the effectiveness of the data cleaning and validation processes.

8. **Limited Statistical Testing:** The analysis relied mainly on descriptive comparisons without formal statistical tests to establish whether observed differences between applicant categories were statistically significant. Therefore, the findings should be interpreted as observed patterns rather than statistically confirmed relationships.

Despite these limitations, the analysis provides a useful foundation for understanding the distribution of loan applications, identifying potential risk indicators and highlighting areas that may require further investigation. 



## 8. Conclusion

This project examined loan default patterns across a portfolio of 44,993 loans, of which 10,000 resulted in defaults, representing an overall default rate of 22.23%. 

The analysis explored how borrower characteristics, income levels, home ownership status, loan purposes, and loan-to-income ratios relate to loan default patterns, with the aim of identifying major credit-risk segments and providing actionable business recommendations.


The findings revealed that **income level and home ownership status are important indicators of borrower risk**. Low-income borrowers recorded a considerably higher default rate than high-income borrowers, while renters accounted for 75.95% of all defaults, with a default rate of 32.40%.
In terms of loan purpose, Debt Consolidation recorded the highest default rate at 30.27%, while Medical loans contributed the largest share of defaults at 23.78%. Furthermore, borrowers within the 0.1–0.3 loan-to-income bands accounted for 60.22% of all defaults, highlighting the importance of assessing borrowers' existing financial commitments and repayment capacity.


Overall, the analysis demonstrates the importance of understanding the relationship between borrower characteristics and loan performance when making lending decisions. It highlights the need for stronger credit assessment procedures, targeted monitoring of high-risk borrower segments, improved affordability checks, and effective loan recovery strategies. It also identifies opportunities to explore lower-default loan categories, particularly Venture and Education loans, for responsible portfolio growth.

In conclusion, adopting a data-driven and risk-based lending approach will enable the lending institution to make better-informed credit decisions, minimise default-related losses, improve repayment performance, and achieve sustainable portfolio growth. The insights and recommendations from this project provide a foundation for strengthening credit-risk management and improving the overall quality of the loan portfolio.


## 9. Appendix
### **9.1 DATA DICTIONARY**

The data dictionary provides descriptions of the key variables, metrics, and calculated fields used in the loan default analysis.

| Variable / Metric | Description |
|---|---|
| `Loan_ID` | Unique identifier assigned to each loan application. |
| `Loan_Intent` | Purpose for which the borrower requested the loan, such as Education, Medical, Venture, Personal, Debt Consolidation, or Home Improvement. |
| `Loan_Amount` | Amount of money requested by the borrower. |
| `Loan_Status` | Indicates whether a loan application was approved or not approved. |
| `Interest_Rate` | Interest rate charged or assigned to the loan. |
| `Loan_Percent_Income` | Proportion of the borrower's income represented by the loan amount. |
| `Home_Ownership` | Borrower's housing status, categorised as Rent, Mortgage, Own, or Other. |
| `Income_Band` | Classification of borrowers based on their income levels, including Low, Lower-Middle, Upper-Middle, and High Income. |
| `Credit_Score_Band` | Classification of borrowers according to their credit score ranges. |
| `Previous_Default` | Indicates whether the borrower has a history of previous loan default. |
| `Education` | Borrower's educational qualification or category. |
| `Total_Applications` | Total number of loan applications within a particular category or segment. |
| `Total_Loans` | Total number of loans considered within a particular segment. |
| `Defaults` | Number of loans that resulted in default within a particular category or segment. |
| `Default_Rate` | Percentage of loans that defaulted within a segment, calculated by dividing defaults by total loans in that segment and multiplying by 100. |
| `Share_of_Defaults` | Percentage contribution of a particular segment to all recorded defaults, calculated by dividing segment defaults by 10,000 total defaults and multiplying by 100. |
| `Approved_Count` | Total number of loan applications approved within a particular category. |
| `Approval_Rate` | Percentage of loan applications approved out of total applications within a category. |
| `Avg_Interest_Rate` | Average interest rate recorded within a particular category or segment. |
| `Avg_Loan_Amount` | Average loan amount within a particular category or segment. |
| `Avg_Loan_Percent_Income` | Average loan-to-income ratio expressed as a percentage. |
| `Total_Approved_Amount` | Sum of loan amounts associated with approved applications. |
| `Total_Not_Approved_Amount` | Sum of loan amounts associated with applications that were not approved. |

### **9.2 KEY CREDIT RISK METRICS AND DEFINITIONS**

Two important metrics were used to evaluate loan default patterns and understand the contribution of different borrower segments to overall portfolio risk.

**1. Default Rate (Defaults ÷ Total Loans in Segment)**

Measures risk intensity by showing the proportion of loans within a particular segment that resulted in default. It indicates how likely a loan in that segment is to default compared with the overall portfolio default rate of **22.23%**.

2. Share of Defaults (Segment Defaults ÷ 10,000 Total Defaults): Measures portfolio impact how much of the total default pool that segment actually drives. 

Combining both prevents over-reacting to small segments with high rates while ensuring high-volume risk drivers are prioritized.


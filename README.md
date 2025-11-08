# Loan Default Analysis Dashboard

### Dashboard Link : https://app.powerbi.com/links/KAVVpegghR?ctid=05fe62bb-3ba9-472e-85dd-dd419706724a&pbi_source=linkShare


## Problem Statement

This dashboard helps financial institutions and lending companies understand the risk factors associated with loan defaults.  
It provides insights into borrower demographics, credit scores, income levels, and employment types to analyze how these factors impact default rates.  

Through various financial and demographic KPIs, the dashboard allows stakeholders to:
- Identify patterns in defaults over time.
- Detect high-risk borrower segments.
- Understand how income, credit score, and employment affect loan performance.
- Improve credit approval policies and reduce overall risk exposure.

It also highlights year-over-year (YOY) loan and default trends, enabling analysts to make data-driven strategic decisions for improving lending performance.

---

### Steps followed 

- **Step 1 :** Load data into **Power BI Desktop**. Dataset used – `Loan_default.csv`.
  
- **Step 2 :** Open **Power Query Editor** and in the **View tab** under Data preview section, check:
  - "Column distribution"
  - "Column quality"
  - "Column profile"

- **Step 3 :** By default, Power BI profiles only 1000 rows, so “Column profiling based on entire dataset” was selected for accurate analysis.

- **Step 4 :** Data cleaning was performed in Power Query.  
  Missing values were found in columns such as *HasCoSigner* and *HasMortgage*. Since they were very few, nulls were ignored for visualizations and calculations.

- **Step 5 :** Data types were verified — numeric fields like *LoanAmount*, *Income*, *InterestRate*, and *CreditScore* were converted to proper numeric formats.

- **Step 6 :** New **calculated columns** were created for easier segmentation:
  
      Age Groups = 
      IF(Loan_default[Age] <= 25, "Teen",
      IF(Loan_default[Age] <= 50, "Adults",
      IF(Loan_default[Age] <= 75, "Middle Age Adults", 
      "Senior Citizens")))

  Similarly, columns for **Credit Score Bins** and **Income Brackets** were created to group numeric data.

- **Step 7 :** The report theme was applied under the **View tab** for consistent color and design.

- **Step 8 :** Visual filters (Slicers) were added for:
  - **Income Bracket**
  - **Employment Type**
  - **Marital Status**
  - **Credit Score Bin**
  
  These allow users to slice the data interactively.

- **Step 9 :** **Measures Table 1** was created to calculate key performance metrics using DAX.  
  The following DAX expressions were written:

        Loan Amount by Purpose = SUMX(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanAmount]))),'Loan_default'[LoanAmount])

        Average Income by Employment Type = CALCULATE(AVERAGE('Loan_default'[Income]),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType]))

        Average Loan by Age Group = AVERAGEX(VALUES('Loan_default'[Age Groups]),AVERAGE('Loan_default'[LoanAmount]))

        Default Rate by Employment Type = 
        var totalrecords= COUNTROWS(ALL('Loan_default')) 
        var defaultcases=COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())) RETURN 
        CALCULATE(DIVIDE(defaultcases,totalrecords),ALLEXCEPT('Loan_default',Loan_default[EmploymentType])) * 100

        Default Rate by Year = 
        VAR totalloans= CALCULATE(COUNTROWS('Loan_default'),ALLEXCEPT('Loan_default',Loan_default[Year]))
        var default= CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),ALLEXCEPT('Loan_default',Loan_default[Year]))
        RETURN 
        DIVIDE(default,totalloans)*100


- **Step 10 :** **Measures Table 2** was created for further analysis:

        Average Loan Amount (High Credit) = 
        AVERAGEX(FILTER('Loan_default','Loan_default'[Credit Score Bins]="High"),'Loan_default'[LoanAmount])

        Loans by Eductaion Type = 
        COUNTROWS(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanID]))))

        Median by credit score bins = 
        MEDIANX('Loan_default','Loan_default'[LoanAmount])

        Total Loan (Credit Bins) = 
        CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Age Groups]="Adults",
        ALLEXCEPT('Loan_default','Loan_default'[Age],'Loan_default'[Age Groups],'Loan_default'[CreditScore],'Loan_default'[Credit Score Bins]))

        Total Loan (Middle Age Adults) = 
        SUMX(FILTER('Loan_default','Loan_default'[Age Groups]="Middle Age Adults"),'Loan_default'[LoanAmount])

- **Step 11 :** **Measures Table 3** was created for YOY (Year Over Year) and YTD (Year To Date) comparisons:
(YOY Change=(Current-Previous)/Previous)

        YOY Loan Amount Change = DIVIDE(
                CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])))-
                CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),
                CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0) *100

        YOY Default Loans Change = 
        DIVIDE(
                CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])))-
                CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),
                CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0) * 100

        YTD Loan Amount = CALCULATE(SUM('Loan_default'[LoanAmount]),DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY].[Date]),
        ALLEXCEPT('Loan_default',Loan_default[Credit Score Bins],Loan_default[MaritalStatus]))

- **Step 12 :** Visuals were added for major KPIs and grouped under three report pages:

  1. **Loan Default & Overview**
  2. **Applicant Demographics & Financial Profile**
  3. **Financial Risk Metrics**

- **Step 13 :** Text boxes were inserted for report title and section headers.  
  Background rectangles and theme color palettes were used for visual consistency.

- **Step 14 :** The report was published to **Power BI Service**.

---

# Snapshot of Dashboard (Power BI Service)

### Page 1: Loan Default & Overview

![Loan Default & Overview](https://drive.google.com/thumbnail?id=1Ytr7Z621Sij0aig56pZuJKwSZhsh0eMw&sz=w1000)


### Page 2: Applicant Demographics & Financial Profile

![Applicant Demographics & Financial Profile](https://drive.google.com/thumbnail?id=1x8FhuCZjugCnWmJrONXNW5zEYOngfMzY&sz=w1000)


### Page 3: Financial Risk Metrics

![Financial Risk Metrics](https://drive.google.com/thumbnail?id=1wx2rbJmP1DHvLnR7oGxiPzk1dH4NiZCp&sz=w1000)



# Insights

A three-page report was created in Power BI Desktop and published to Power BI Service.

Following insights can be drawn from the dashboard:

---

### [1] Loan Default Overview

- **Home loans** have the highest total loan amount (₹6545M).
- **Average income** is highest for full-time employees (₹82,890) and lowest for unemployed (₹82,272).
- **Default rate** is highest among **unemployed (3.4%)** and lowest for **full-time employees (2.4%)**.
- **Average Loan Amount by Age Group:**
  - Adults – ₹127,901  
  - Middle Age Adults – ₹127,459  
  - Senior Citizens – ₹127,355  
  - Teen – ₹126,674
- Default Rate by Year peaked in **2016 (11.75%)** and was lowest in **2014 (11.50%)**.

---

### [2] Applicant Demographics & Financial Profile

- Median Loan Amount by Credit Score:
  - Low: ₹128,705  
  - Medium: ₹127,612  
  - Very Low: ₹127,515  
  - High: ₹127,468
- Married applicants have slightly higher average loans (~8.3%).
- Loans are highest among **Bachelor’s degree holders**.
- Total loan volume for adults by credit score bins:
  - High – ₹5.4B  
  - Medium – ₹4.5B  
  - Very Low – ₹2.3B  
  - Low – ₹0.3B

---

### [3] Financial Risk Metrics

- YOY Loan Amount Change was **highest in 2018 (+1.73%)**.
- YOY Default Loan Change was **highest in 2015 (+2.7%)** and **2018 (+1.9%)**.
- YTD Loan Amount by Credit Score and Marital Status:
  - High credit borrowers (Married/Divorced) dominate with ~₹0.8B each.
- High-income full-time employees account for the largest loan share (~₹22B out of ₹33B total).

---

# Summary Table of Key Metrics

| Metric | Insight |
|--------|----------|
| Highest Loan Purpose | Home (₹6545M) |
| Highest Default Group | Unemployed (3.4%) |
| Lowest Default Group | Full-time employed (2.4%) |
| Peak Default Year | 2016 (11.75%) |
| Highest Loan Age Group | Adults (~₹127K) |
| Top Credit Category | Low Credit (₹128K Median) |
| YOY Loan Growth Peak | 2018 (+1.73%) |

---

# Conclusion

The **Loan Default Analysis Dashboard** provides an end-to-end view of borrower risk profiles and loan performance.  
It helps stakeholders identify high-risk borrowers, understand financial trends, and improve credit evaluation policies.

By monitoring income brackets, credit scores, and loan purposes, financial institutions can make data-driven decisions to reduce defaults and enhance lending efficiency.

---

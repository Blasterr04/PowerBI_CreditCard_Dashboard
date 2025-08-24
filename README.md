# 💳 Credit Card Analytics — Power BI Dashboard Project

## 📌 Introduction
This Power BI project transforms raw Excel transaction and customer data into a comprehensive analytics solution with SQL backend processing. The dashboard provides executives and analysts with clear KPIs, trends, and customer segmentation to support data-driven business decisions.

## 📂 Project Overview

### Data Pipeline
1. **Excel Data Extraction**: Raw data sourced from Excel sheets
2. **SQL Database Storage**: Data loaded into structured SQL tables
3. **Power BI Processing**: Connection to SQL for modeling and visualization
4. **Dashboard Creation**: Development of two interactive dashboards

### Primary Objectives
- Monitor revenue growth (WoW, YTD trends)
- Analyze transaction volume and card performance
- Segment customers by demographics and behavior
- Identify top-performing geographies and card categories

## 🛠️ Technical Implementation

### SQL Database Schema
```sql
CREATE TABLE creditcard_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date DATE,
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);

CREATE TABLE customer_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);
```
### ⚙️ DAX Measures & Calculations
```sql
🧑‍🤝‍🧑 Customer Segmentation
AgeGroup = 
SWITCH(
    TRUE(),
     'public customer_detail'[customer_age] < 30, "20-30",
     'public customer_detail'[customer_age] >= 30 && 'public customer_detail'[customer_age] < 40, "30-40",
     'public customer_detail'[customer_age] >= 40 && 'public customer_detail'[customer_age] < 50, "40-50",
     'public customer_detail'[customer_age] >= 50 && 'public customer_detail'[customer_age] < 60, "50-60",
     'public customer_detail'[customer_age] >= 60, "60+",
     "unknown"
)

Income Group = 
SWITCH(
    TRUE(),
    'public customer_detail'[income] < 35000, "Low",
    'public customer_detail'[income] >= 35000 && 'public customer_detail'[income] <= 70000, "Medium",
    'public customer_detail'[income] > 70000 && 'public customer_detail'[income] <= 100000, "High",
    'public customer_detail'[income] > 100000, "Very High",
    "Unknown"
)


💰 Revenue Calculations
Revenue = 
VAR InterchangeRevenue = 'public creditcard_detail'[total_trans_amt] * 0.02
VAR AnnualFeeRevenue = 'public creditcard_detail'[annual_fees]
VAR TotalRevenue = 'public creditcard_detail'[interest_earned] 
                 + InterchangeRevenue 
                 + AnnualFeeRevenue
RETURN TotalRevenue

Current_Week_Revenue = 
CALCULATE(
    SUM('public creditcard_detail'[Revenue]),
    FILTER(
        ALL('public creditcard_detail'),
        'public creditcard_detail'[week_num2] = MAX('public creditcard_detail'[week_num2])
    )
)

Previous_Week_Revenue = 
CALCULATE(
    SUM('public creditcard_detail'[Revenue]),
    FILTER(
        ALL('public creditcard_detail'),
        'public creditcard_detail'[week_num2] = MAX('public creditcard_detail'[week_num2]) - 1
    )
)

wow_revenue = 
DIVIDE(
    ([Current_Week_Revenue] - [Previous_Week_Revenue]),
    [Previous_Week_Revenue]
)
```
## 📊 Dashboard Components

### 💳 Credit Card Transactions Dashboard

| **Metric**              | **Value**   |
|--------------------------|-------------|
| Overall Revenue          | $57M        |
| Total Interest Earned    | $8M         |
| Total Transaction Amount | $46M        |
| Activation Rate          | 57.5%       |
| Delinquent Rate          | 6.06%       |

#### 🔍 Transaction Analysis
- Weekly revenue trends and comparisons  
- Transaction volume by card category  
- Utilization patterns across demographics  
- Expense type distribution  
- Chip usage vs. other payment methods  

---

### 👥 Credit Card Customers Dashboard

#### 📌 Customer Demographics

| **Segment**                     | **Value**   |
|---------------------------------|-------------|
| Male Customers Contribution     | $31M        |
| Female Customers Contribution   | $26M        |
| Blue & Silver Card Dominance    | 93% of transactions |
| Top States (TX, NY, CA)         | 68% of revenue |

#### 🧑‍🤝‍🧑 Customer Segmentation
- Age group distribution and spending patterns  
- Income segmentation (Low, Medium, High, Very High)  
- Geographic performance by state  
- Education and occupation analysis  
- Ownership status (Home, Car, Loans)  

---

### 💡 Key Insights

#### 📈 Performance Trends
- Revenue increased by **28.8% Week-over-Week (Week 53)**  
- Blue and Silver cards dominate with **93% of total transactions**  
- Top 3 states (**TX, NY, CA**) contribute **68% of total revenue**  
- Healthy activation rate of **57.5% within 30 days**  

#### 👥 Customer Behavior
- Male customers contribute **$31M** vs **$26M** from female customers  
- Age groups **30-50** show highest spending patterns  
- High-income customers demonstrate superior credit utilization  
- Homeowners show better repayment behavior than non-homeowners  

#### 📋 Operational Metrics
- Overall delinquent rate maintained at **6.06%**  
- Customer acquisition cost optimized through targeted campaigns  
- Chip usage shows higher transaction security and customer trust  
- Satisfaction scores correlate with higher credit limits and spending  

---


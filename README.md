# Telco Customer Churn Analysis: Python & SQL Pipeline

## 📌 Project Overview
This project analyzes a Telco customer dataset to identify the primary drivers of customer churn. By leveraging SQL for data processing and Python (Pandas/Matplotlib) for data analysis and visualization—this project uncovers high-risk customer segments and provides actionable business insights.

## 🛠️ Tech Stack
* **Database Engine:** MySQL
* **Data Processing & Cleaning:** Python (Pandas)
* **Data Aggregation & Metrics:** SQL
* **Data Visualization:** Python (Matplotlib)
* **Environment:** Jupyter Notebook

## 📂 Dataset & Database Schema
The dataset originates from Kaggle and contains customer-level information, including demographic data, account information, subscribed services, and churn status. 

* **Data Cleaning:** Missing values were handled, formatting was standardized, and binary columns were mapped in Python.
* **Database Integration:** The cleaned DataFrame was pushed to a MySQL database for structured querying. 

### Base Table Snapshot

<img width="896" height="47" alt="Screenshot_2026-10-17_13-58-28" src="https://github.com/user-attachments/assets/5488ff3a-5474-42c4-abde-eb02fcdbc900" />
<img width="1127" height="44" alt="Screenshot_2026-10-17_14-00-34" src="https://github.com/user-attachments/assets/a24e6db5-a21d-4fb9-929d-9a6905a03b11" />

---

## 📊 Key Performance Indicators (KPIs) & SQL Analysis

### KPI 1: Churn by Contract Type
**Objective:** Compare the churn volume and percentage of month-to-month contracts against 1-year and 2-year contracts.

**SQL Query:**
```sql
[SELECT 
    Contract, 
    COUNT(Churn) AS LeaveCount, 
    ROUND((COUNT(Churn) * 100.0) / SUM(COUNT(Churn)) OVER(), 2) AS PercentChurn
FROM telco.telco_customers
WHERE Churn = 1
GROUP BY Contract]
```

**Insight:** Customers on month-to-month contracts account for the overwhelming majority of churn (88.55%), indicating high volatility in short-term commitments.

**Visualization:**

<img width="787" height="495" alt="Screenshot_2026-10-17_14-03-18" src="https://github.com/user-attachments/assets/f13ee6cb-b71f-4774-afda-6d14ecc3eb71" />


---

### KPI 2: Churn by Tenure Risk
**Objective:** Group customers by the number of months they stayed to identify the highest risk period.

**SQL Query:**
```sql
[SELECT
	CASE
		WHEN Tenure <= 6 THEN '0-6 Months'
		WHEN Tenure > 6 AND Tenure <= 12 THEN '6-12 Months'
		WHEN Tenure >12 AND Tenure <= 18 THEN '12-18 Months'
		WHEN Tenure >18 AND Tenure <= 24 THEN '18-24 Months'
		WHEN Tenure >24 AND Tenure <= 30 THEN '24-30 Months'
		WHEN Tenure >30 AND Tenure <= 36 THEN '30-36 Months'
		WHEN Tenure >36 AND Tenure <= 42 THEN '36-42 Months'
		WHEN Tenure >42 AND Tenure <= 48 THEN '42-48 Months'
		WHEN Tenure >48 AND Tenure <= 54 THEN '48-54 Months'
		WHEN Tenure >54 AND Tenure <= 60 THEN '54-60 Months'
		WHEN Tenure >60 AND Tenure <= 66 THEN '60-66 Months'
		WHEN Tenure >66 AND Tenure <= 72 THEN '66-72 Months'
	END AS 'Months_Interval',
	
	COUNT(Churn) As Churn
FROM telco.telco_customers
where Churn=1
group by Months_Interval
order by MIN(Tenure)]
```

**Insight:** The highest risk of attrition occurs in the first 6 months. If a customer can be retained past the 6-month mark, churn volume drops drastically.

**Visualization:**

<img width="995" height="601" alt="Screenshot_2026-10-17_14-06-02" src="https://github.com/user-attachments/assets/3a9479f1-b13d-4b6d-932f-048bb04a1d11" />


---

### KPI 3: Monthly Charges vs. Retention
**Objective:** Compare the average monthly bill of customers who left versus those who stayed.

**SQL Query:**
```sql
[SELECT 
	CASE
		WHEN churn = 1 THEN 'Churn Customers'
		WHEN churn = 0 THEN 'Non Churn Customers'
	END AS 'Customer_Type',
	
	ROUND(AVG(MonthlyCharges), 2) as Avg_Monthly_Billing 
	
FROM telco.telco_customers
WHERE Churn = 1 or Churn = 0
GROUP BY Customer_Type]
```

**Insight:** Customers who churned paid an average of **$74.44/month**, roughly **$13 higher** than the average bill of retained customers ($61.27/month). Price sensitivity is a clear driver of attrition.

**Visualization:**

<img width="703" height="504" alt="Screenshot_2026-10-17_14-08-07" src="https://github.com/user-attachments/assets/c2ee78b2-1a04-4306-b263-9fbe476a2eec" />


---

### KPI 4: Impact of Tech Support
**Objective:** Contrast the churn rate of customers utilizing tech support services against those who do not.

**SQL Query:**
```sql
[SELECT 
	CASE
		WHEN TechSupport = "Yes" THEN 'TechSupport'
		WHEN TechSupport = "No" THEN 'Non TechSupport'
		ELSE 'No Internet'
	END AS 'UsingTechSupport',
	
	ROUND(AVG(Churn)* 100, 2) as Churn_Percentage
	
FROM telco.telco_customers

GROUP BY UsingTechSupport]
```

**Insight:** Customers without tech support have a **41.64%** churn rate, compared to just **15.17%** for those utilizing the service. A lack of issue resolution is highly correlated with cancellation.

**Visualization:**

<img width="796" height="501" alt="Screenshot_2026-10-17_14-09-53" src="https://github.com/user-attachments/assets/51b75196-a47a-43c5-b8ec-cd9071980bd6" />


---

### KPI 5: Churn by Service Type
**Objective:** Break down churn percentages across different internet setups (Fiber Optic vs. DSL).

**SQL Query:**
```sql
[SELECT InternetService, ROUND(AVG(Churn)*100, 2) AS ChurnPercentage
                                    FROM telco.telco_customers
                                    GROUP BY InternetService]
```

**Insight:** Fiber Optic customers are churning at an alarming rate of **41.89%**, more than double the rate of DSL customers (18.96%). This suggests potential reliability or pricing issues specific to the fiber optic service line.

**Visualization:**

<img width="697" height="502" alt="Screenshot_2026-10-17_14-11-59" src="https://github.com/user-attachments/assets/76f6d3b2-1758-4819-a8de-5cf5abd6f3c8" />


---


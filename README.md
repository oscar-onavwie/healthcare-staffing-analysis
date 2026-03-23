# Healthcare Staffing and Incident Analysis (SQL + Power BI)

## Business Problem

Care home management needs to understand whether staffing shortages, overtime, and agency staff usage are contributing to higher incident rates across wards and shifts.
The goal of this project is to analyse operational data and identify high-risk areas that may require staffing or management intervention.

This type of analysis is commonly required in healthcare environments to support safer staffing decisions and reduce incident occurrences.

---

## Key Questions

1. Does staffing level affect incident rates?
2. Which wards or shifts show the highest operational risk?

---

## Dataset

The dataset simulates daily care home operational records, including:

* Date
* Shift (Day / Night)
* Ward
* Staff count
* Resident count
* Agency staff usage
* Overtime hours
* Incident counts
* Incident severity
* Falls
* Medication errors
* Safeguarding incidents

Additional calculated fields were created in SQL:

* Resident-to-staff ratio
* Low staffing flag
* Month and year fields

---

## SQL Data Preparation

A cleaned table was created from the raw data to allow analysis.

```sql
SELECT
    Date,
    Shift,
    Ward,
    Staff_Count,
    Residents_Count,
    Agency_Staff,
    Falls,
    Medication_Errors,
    Pressure_Sores,
    Safeguarding_Incidents,
    Total_Incidents,
    Staff_Hours,
    Overtime_Hours,
    Incident_Severity,

    CAST(Residents_Count * 1.0 / NULLIF(Staff_Count, 0) AS DECIMAL(10,2)) 
    AS Resident_Staff_Ratio,

    CASE
        WHEN Staff_Count < 5 THEN 'Yes'
        ELSE 'No'
    END AS Low_Staffing_Flag,

    DATENAME(MONTH, Date) AS Month_Name,
    YEAR(Date) AS Year_Number,
    MONTH(Date) AS Month_Number

INTO healthcare_data
FROM healthcare_data_raw;
```

---

## Analysis

### 1. Are low staffing shifts linked to more incidents?

```sql
SELECT
    Low_Staffing_Flag,
    AVG(CAST(Total_Incidents AS DECIMAL(10,2))) AS Avg_Incidents,
    AVG(CAST(Overtime_Hours AS DECIMAL(10,2))) AS Avg_Overtime
FROM healthcare_data
GROUP BY Low_Staffing_Flag;
```

Finding:
Low staffing shifts account for a higher average of incidents and higher overtime hours.

---

### 2. Does agency staff usage increase incidents?

```sql
SELECT
    Agency_Staff,
    AVG(CAST(Total_Incidents AS DECIMAL(10,2))) AS Avg_Incidents
FROM healthcare_data
GROUP BY Agency_Staff
ORDER BY Agency_Staff;
```

Finding:
Higher agency staff usage was associated with higher incident averages.

---

### 3. Which ward has the highest incident rate?

```sql
SELECT
    Ward,
    AVG(CAST(Total_Incidents AS DECIMAL(10,2))) AS Avg_Incidents
FROM healthcare_data
GROUP BY Ward
ORDER BY Avg_Incidents DESC;
```

Finding:
Ward B recorded the highest average incident rate.

---

### 4. Which shift has the most incidents?

```sql
SELECT
    Shift,
    SUM(Total_Incidents) AS Total_Incidents
FROM healthcare_data
GROUP BY Shift
ORDER BY Total_Incidents DESC;
```

Finding:
Night shifts shown the highest total incidents.

---

### 5. Which wards are under the most staffing pressure?

```sql
SELECT
    Ward,
    AVG(Resident_Staff_Ratio) AS Avg_Resident_Staff_Ratio,
    AVG(CAST(Total_Incidents AS DECIMAL(10,2))) AS Avg_Incidents,
    AVG(CAST(Overtime_Hours AS DECIMAL(10,2))) AS Avg_Overtime
FROM healthcare_data
GROUP BY Ward
ORDER BY Avg_Resident_Staff_Ratio DESC;
```

Finding:
Wards with higher resident-to-staff ratios also shown higher incidents and overtime.

---

## Dashboard

A Power BI dashboard was created to monitor:

* Incidents by ward
* Incidents by shift
* Staffing ratio
* Overtime hours
* Monthly incident trends
* Incident severity

Dashboard screenshot will be added here.

---

## Key Insights

* Low staffing shifts had higher incident averages
* Ward B showed the highest operational risk
* Night shifts recorded the most incidents
* Higher overtime correlated with more incidents
* Higher resident-to-staff ratios were linked to increased risk

---

## Tools Used

* SQL Server Express
* SQL Server Management Studio
* Power BI
* CSV dataset
* GitHub

---

## Author

Oscar Onavwie
Data Analyst | SQL | Power BI | Data Analytics

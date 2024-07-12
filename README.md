# **Global Education Analysis by SQL**

## Question 1: Identify the country with the highest completion rates for both primary and upper secondary education for both males and females.
```sql
SELECT TOP 1 
    Countries_and_areas,
    Completion_Rate_Primary_Male,
    Completion_Rate_Primary_Female,
    Completion_Rate_Upper_Secondary_Male,
    Completion_Rate_Upper_Secondary_Female
FROM Global_Education
ORDER BY
    Completion_Rate_Primary_Male DESC,
    Completion_Rate_Primary_Female DESC,
    Completion_Rate_Upper_Secondary_Male DESC,
    Completion_Rate_Upper_Secondary_Female DESC
```

## Question 2: Compare literacy rates between males and females in the 15-24 age group and determine the country with the largest disparity.

```sql
SELECT
    Countries_and_areas,
    ABS(CAST(Youth_15_24_Literacy_Rate_Male AS int) - CAST(Youth_15_24_Literacy_Rate_Female AS int)) AS Disparity 
FROM Global_Education
ORDER BY Disparity DESC 
```

##  Question 3: Find countries with high birth rates but low unemployment rates, and vice versa.
###  Countries with high birth rates but low unemployment rates

```sql
SELECT
	Countries_and_areas,
	Birth_Rate,
	(SELECT AVG(Birth_Rate) FROM Global_Education) AS AVG_Birth_rate ,
	Unemployment_Rate,
	(SELECT AVG(Unemployment_Rate) FROM Global_Education) AS AVG_Unemployment_rate
FROM Global_Education
WHERE Birth_Rate > (SELECT AVG(Birth_Rate) AS Birt_rate FROM Global_Education)
AND Unemployment_Rate < (SELECT AVG(Unemployment_Rate) AS Unemployment_rate FROM Global_Education)
```
###  Countries with high unemployment rates but low birth rates

```sql
SELECT
	Countries_and_areas,
	Birth_Rate,
	(SELECT AVG(Birth_Rate) FROM Global_Education) AS AVG_Birth_rate ,
	Unemployment_Rate,
	(SELECT AVG(Unemployment_Rate) FROM Global_Education) AS AVG_Unemployment_rate
FROM Global_Education
WHERE Birth_Rate < (SELECT AVG(Birth_Rate) AS Birt_rate FROM Global_Education)
AND Unemployment_Rate > (SELECT AVG(Unemployment_Rate) AS Unemployment_rate FROM Global_Education)
```

## Question 4: Analyze the correlation between gross primary education enrollment and primary education completion rates.

```sql
WITH stats AS (
    SELECT
        COUNT(*) AS n,
        SUM(Gross_Primary_Education_Enrollment) AS sum_x,
        SUM(CAST(Completion_Rate_Primary_Female AS int) + CAST(Completion_Rate_Primary_Male AS int)) AS sum_y,
        SUM(CAST(Gross_Primary_Education_Enrollment AS int) * (CAST(Completion_Rate_Primary_Female AS int) + CAST(Completion_Rate_Primary_Male AS int))) AS sum_xy,
        SUM(CAST(Gross_Primary_Education_Enrollment AS int) * CAST(Gross_Primary_Education_Enrollment AS int)) AS sum_x2,
        SUM((CAST(Completion_Rate_Primary_Female AS int) + CAST(Completion_Rate_Primary_Male AS int)) * (CAST(Completion_Rate_Primary_Female AS int) + CAST(Completion_Rate_Primary_Male AS int))) AS sum_y2
    FROM Global_Education
)
SELECT
    (n * sum_xy - sum_x * sum_y) / 
    SQRT((n * sum_x2 - sum_x * sum_x) * (n * sum_y2 - sum_y * sum_y)) AS correlation
FROM stats;
```


## Question 5: Identify countries with a significant discrepancy between reading and math proficiency rates at the end of primary education.

```sql
SELECT
	Countries_and_areas,
	Primary_End_Proficiency_Math,
	Primary_End_Proficiency_Reading,
	ABS(CAST(Primary_End_Proficiency_Math AS int) - CAST(Primary_End_Proficiency_Reading AS int)) AS Discrepancy
FROM Global_Education
WHERE ABS(CAST(Primary_End_Proficiency_Math AS int) - CAST(Primary_End_Proficiency_Reading AS int)) > 10
ORDER BY Discrepancy DESC
```

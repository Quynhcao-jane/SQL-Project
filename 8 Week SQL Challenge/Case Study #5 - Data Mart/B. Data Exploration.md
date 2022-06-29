#B. Data Exploration

**1. What day of the week is used for each week_date value?**

````sql
SELECT datename(dw,week_date) AS week_day 
FROM clean_weekly_sales 
GROUP BY datename(dw,week_date)
````

**Answer:**

<img width="110" alt="image" src="https://user-images.githubusercontent.com/81607668/131616348-81580d0e-b919-439a-821d-7997d958f59e.png">

- Monday is used for each `week_date` value.

**2. What range of week numbers are missing from the dataset?**
- First, generate the full range of week numbers for the entire year from 1st week to 52nd week.
- Then, do a LEFT JOIN of `week_of_year` with `clean_weekly_sales` - make sure that the join sequence is CTE followed by the temp table as doing this on reverse would result in null result (unless you run a RIGHT OUTER JOIN instead!).

````sql
CREATE TABLE week_of_year
(week_of_year int)

declare @COUNT int
SET @COUNT = 1
WHILE @COUNT<=52
begin
	INSERT INTO week_of_year values (@COUNT)
	SET @COUNT+=1
END

SELECT a.week_of_year
FROM week_of_year a
LEFT JOIN clean_weekly_sales b
ON b.week_number = a.week_of_year
WHERE b.week_number is null -- Filter for the missing week numbers whereby the values would be `null`
````

**Answer:**

#Weeks 1 - 12 and 37 - 52 are missing from the dataset

**3. How many total transactions were there for each year in the dataset?**

````sql
SELECT 
  calendar_year, 
  SUM(transactions) AS total_transactions 
FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY calendar_year

````

**Answer:**

| calendar_year | total_transactions  |
|---------------|-------------------------------|
| 2018          | 346406460                     |
| 2019          | 365639285                     |
| 2020          | 375813651                     |

**4. What is the total sales for each region for each month?**

I think this question presumes not only grouping by months but grouping by months and years. This kind of grouping allows us to see year over year trends.
````sql
SELECT 
  region, 
  month_number, 
  calendar_year,
  SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY region, month_number, calendar_year
ORDER BY region, month_number,calendar_year
````

**Answer:**
Here is a visualisation for this query:

![index1](https://user-images.githubusercontent.com/108384522/176493264-c8062387-f561-44b0-a654-0d8de0f081e3.png)

We can see that the Oceania, Africa and Asia have the most total sales. The peak period is from Apr to Aug each year.
**5. What is the total count of transactions for each platform?**

````sql
SELECT 
  platform, 
  SUM(transactions) AS total_transactions
FROM clean_weekly_sales
GROUP BY platform
ORDER BY platform
````

**Answer:**

| platform | total_transactions  |
|----------|---------------------|
| Retail   | 1081934227          |
| Shopify  | 5925169             |

**6. What is the percentage of sales for Retail vs Shopify for each month?**

I think this question presumes not only grouping by months but grouping by months and years. This kind of grouping allows us to see year over year trends.
````sql
WITH A1 AS (
	SELECT 
    calendar_year, 
    month_number, 
    platform, 
    SUM(sales) AS monthly_sales
	FROM clean_weekly_sales
	GROUP BY calendar_year, month_number, platform
)
SELECT calendar_year, month_number,platform,
	    round(100 * monthly_sales / SUM(monthly_sales) 
		OVER (PARTITION by  calendar_year, month_number ORDER BY  calendar_year, month_number),2) AS Percentage
FROM A1
````

**Answer:**

We can see that sales in retail is the primary platform.

<details><summary> Click to expand :arrow_down: </summary>
Top 20 rows of the result
  
![index1](https://user-images.githubusercontent.com/108384522/176430885-ad46598b-290d-4536-ac86-14208dcc8770.png)
</details>

**7. Which age_band and demographic values contribute the most to Retail sales?**

#The combination of age_band and demographic values contribute the most to Retail sales:

````sql
WITH A AS(
SELECT
  age_band,
  demographic,
  SUM(sales) AS total_sales
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY age_band, demographic)

SELECT TOP 1 * FROM A
WHERE age_band !='unknown' and demographic !='unknown'
ORDER BY total_sales desc
````

| demographic | age_band | total_sales |
|-------------|----------|-------------|
| Families    | Retirees | 6634686916  | 

  
#The age_band value contribute the most to Retail sales:

````sql
WITH B AS(
SELECT
  age_band,
  SUM(sales) AS total_sales
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY age_band)

SELECT top 1 * FROM B
WHERE age_band !='unknown'
ORDER BY total_sales desc
````

| age_band | total_sales |
|----------|-------------|
| Retirees | 13005266930 |
  
  
#The demographic value contribute the most to Retail sales:

````sql
WITH C AS(
SELECT
  demographic,
  SUM(sales) AS total_sales
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY demographic)

SELECT TOP 1 * FROM C
WHERE demographic !='unknown'
ORDER BY total_sales desc
````

| demographic | total_sales |
|-------------|-------------|
| Families    | 12759667763 | 

**Answer:**
We can conclude that the age group of retirees and the demographic group of families and the combined group of retired families contribute the most to retail sales.

***

Click [here](https://github.com/katiehuangx/8-Week-SQL-Challenge/blob/main/Case%20Study%20%235%20-%20Data%20Mart/C.%20Before%20%26%20After%20Analysis.md) for solution to **C. Before & After Analysis**!

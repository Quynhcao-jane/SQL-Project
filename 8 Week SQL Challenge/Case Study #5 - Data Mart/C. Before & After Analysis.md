# C. Before & After Analysis

This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the `week_date` value of `2020-06-15` as the baseline week where the Data Mart sustainable packaging changes came into effect. We would include all `week_date` values for `2020-06-15` as the start of the period after the change and the previous week_date values would be before.

Using this analysis approach - answer the following questions:

**1. What is the total sales for the 4 weeks before and after `2020-06-15`? What is the growth or reduction rate in actual values and percentage of sales?**

Before we start, we find out the week_number of `'2020-06-15'` so that we can use it for filtering. 

````sql
SELECT 
  DISTINCT week_number
FROM clean_weekly_sales
WHERE week_date = '2020-06-15' 
  AND calendar_year = '2020'
````

<img width="138" alt="image" src="https://user-images.githubusercontent.com/81607668/131943472-5de6c243-c8e9-490d-8a4d-7bf990b4fd21.png">

````sql
WITH changes AS (
  SELECT 
    week_date, 
    week_number, 
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 21 AND 28) 
    AND (calendar_year = 2020)
  GROUP BY week_date, week_number
),
changes_2 AS (
  SELECT 
    SUM(CASE WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_change
  FROM changes)

SELECT 
  before_change, 
  after_change, 
  after_change - before_change AS variance, 
  ROUND(100 * (after_change - before_change) / before_change,2) AS percentage
FROM changes_2
````

**Answer:**

<img width="528" alt="image" src="https://user-images.githubusercontent.com/81607668/131943973-1406a95d-8fde-4b12-9390-d91c22d7ddff.png">

Let's vsee the visualisation of sales for the period 4 weeks before and after:

 ````sql
SELECT week_number,SUM(sales) sales
FROM clean_weekly_sales
WHERE 
  ((week_number BETWEEN 21 AND 24) AND(calendar_year = 2020)) OR 
  ((week_number BETWEEN 25 AND 28) AND (calendar_year = 2020))
GROUP BY week_number, calendar_year
ORDER BY week_number
````

![index](https://user-images.githubusercontent.com/108384522/176660286-96eeb38e-2f5f-419f-bb22-f1ac604f2a34.png)

Since the new sustainable packaging came into effect, the sales has dropped by $26,884,188 at a negative 1.15%. The sales has dropped from week 25 and risen again from week 28. 
***

**2. What about the entire 12 weeks before and after?**

We can apply the same logic and solution to this question. 

````sql
WITH changes AS (
  SELECT 
    week_date, 
    week_number, 
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 13 AND 37) 
    AND (calendar_year = 2020)
  GROUP BY week_date, week_number
),
changes_2 AS (
  SELECT 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN total_sales END) AS after_change
  FROM changes)

SELECT 
  before_change, 
  after_change, 
  after_change - before_change AS variance, 
  ROUND(100 * (after_change - before_change) / before_change,2) AS percentage
FROM changes_2
````

**Answer:**

<img width="582" alt="image" src="https://user-images.githubusercontent.com/81607668/131946233-45fa874e-0632-462d-9451-5ed4299b6183.png">

The sales has gone down with a negative 2.14%.

***

**3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?**

I'm breaking down this question to 2 parts.

**Part 1: How do the sale metrics for 4 weeks before and after compare with the previous years in 2018 and 2019?**

````sql
WITH summary AS (
  SELECT 
    calendar_year, -- added new column
    week_number, 
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 21 AND 28) 
  GROUP BY calendar_year, week_number
),
summary_2 AS (
  SELECT 
    calendar_year,
    SUM(CASE WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_sales,
    SUM(CASE WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_sales
  FROM summary
  GROUP BY calendar_year
)

SELECT 
  calendar_year, 
  before_sales, 
  after_sales, 
  after_sales - before_sales AS sales_variance, 
  ROUND(100 * (after_sales - before_sales) / before_sales,2) AS percentage
FROM summary_2
````

**Answer:**

<img width="735" alt="image" src="https://user-images.githubusercontent.com/81607668/131950161-371052e1-ad8b-4fe7-a1a1-97b968416d1d.png">

Let's take a look at the visualization of total sales 4 weeks before and after in each year.

 ````sql
SELECT 
  calendar_year, 
  week_number,
  SUM(sales) sales
FROM clean_weekly_sales
WHERE 
  (week_number BETWEEN 21 AND 24) OR 
  (week_number BETWEEN 25 AND 28)
GROUP BY calendar_year, week_number
ORDER BY calendar_year, week_number
````

![index](https://user-images.githubusercontent.com/108384522/176726243-6ebdb872-465c-47c0-b12f-c00b2bbe9960.png)

In general, we can see that the total sales 4 weeks before and after in 2020 is higher than 2 years ago. 
However, in 2018 and 2019 the total sales 4 weeks before and after tend to increase, and in 2020 it decreases by 1.15%. One of the times when 2020 sales fell sharply was week 25, when the packaging change was introduced.

**Part 2: How do the sale metrics for 12 weeks before and after compare with the previous years in 2018 and 2019?**
- Use the same solution above and change to week 13 to 24 for before and week 25 to 37 for after.

````sql
WITH summary AS (
  SELECT 
    calendar_year, -- added new column
    week_number, 
    SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 13 AND 37) 
  GROUP BY calendar_year, week_number
),
summary_2 AS (
  SELECT 
    calendar_year,
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_sales,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN total_sales END) AS after_sales
  FROM summary
  GROUP BY calendar_year
)

SELECT 
  calendar_year, 
  before_sales, 
  after_sales, 
  after_sales - before_sales AS sales_variance, 
  ROUND(100 * (after_sales - before_sales) / before_sales,2) AS percentage
FROM summary_2
````

**Answer:**

<img width="719" alt="image" src="https://user-images.githubusercontent.com/81607668/131950689-0241db95-6e4b-4b86-80cb-5cd2eada23cc.png">

Let's take a look at the visualization of total sales 12 weeks before and after in each year.
 ````sql
SELECT 
  calendar_year, 
  week_number,
  SUM(sales) sales
FROM clean_weekly_sales
WHERE 
  (week_number BETWEEN 13 AND 24) OR 
  (week_number BETWEEN 25 AND 36)
GROUP BY calendar_year, week_number
ORDER BY calendar_year, week_number
````

![index](https://user-images.githubusercontent.com/108384522/176727223-0bcd3a24-c987-4334-8e5b-cadc8f41b573.png)

Sales in 2018 tend to rise, in 2019 sales look like a plateau, and sales in 2020 are falling. Weeks 15, 17 and 32 in 2020 had less sales than the same weeks in 2019. Actually we can not say that the week 25 in 2020 was the worst one.

***

Click [here](https://github.com/Quynhcao-jane/SQL-Project/blob/main/8%20Week%20SQL%20Challenge/Case%20Study%20%235%20-%20Data%20Mart/D.%20Bonus%20Question:%20Which%20areas%20of%20the%20business%20have%20the%20highest%20negative%20impact%20in%20sales%20metrics.md) for **D. Bonus Question** solution! 🙌🏻

#D. Bonus Question: Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?
Let's start from each area first and then compare performance of all areas for the 12 week before and after period.
- **region:**

````sql
WITH before_sales AS
(
SELECT 
  region,
  week_number,
  SUM(sales) AS before_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 13 AND 24) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  region
 ),
 after_sales AS
(
SELECT 
  region,
  week_number,
  SUM(sales) AS after_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 25 AND 36) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  region
 )
 
 SELECT 
   A.region, 
   SUM(A.before_sales) AS total_sales_before, 
   SUM(B.after_sales) AS total_sales_after, 
   SUM(B.after_sales)-SUM(A.before_sales) as change_in_sales,
   round(100*(SUM(B.after_sales)-SUM(A.before_sales))/SUM(A.before_sales),2) AS percentage_of_change
 FROM before_sales A
 LEFT JOIN after_sales B
 ON A.region=B.region
 GROUP BY A.region
````

| region        | total_sales_before | total_sales_after | change_in_sales | percentage_of_change  |
|---------------|--------------------|-------------------|-----------------|-----------------------|
| ASIA          | 1637244466         | 1583807621        | -53436845       | -3.26                 |
| OCEANIA       | 2354116790         | 2282795690        | -71321100       | -3.03                 |
| SOUTH AMERICA | 213036207          | 208452033         | -4584174        | -2.15                 |
| CANADA        | 426438454          | 418264441         | -8174013        | -1.92                 |
| USA           | 677013558          | 666198715         | -10814843       | -1.60                 |
| AFRICA        | 1709537105         | 1700390294        | -9146811        | -0.54                 |
| EUROPE        | 108886567          | 114038959         | 5152392         | 4.73                  |

Before and after analysis shows that, except for Europe, the rest of the regions experienced a decrease in total sales. Sales drop 3.26% in Asia and $71,321,100 in Oceania had the highest negative impact in sales metrics performance in 2020.

- **platforms:**

````sql
WITH before_sales AS
(
SELECT 
  platform,
  week_number,
  SUM(sales) AS before_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 13 AND 24) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  platform
 ),
 after_sales AS
(
SELECT 
  platform,
  week_number,
  SUM(sales) AS after_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 25 AND 36) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  platform
 )
 
 SELECT 
   A.platform, 
   SUM(A.before_sales) AS total_sales_before, 
   SUM(B.after_sales) AS total_sales_after, 
   SUM(B.after_sales)-SUM(A.before_sales) as change_in_sales,
   round(100*(SUM(B.after_sales)-SUM(A.before_sales))/SUM(A.before_sales),2) AS percentage_of_change
 FROM before_sales A
 LEFT JOIN after_sales B
 ON A.platform=B.platform
 GROUP BY A.platform
````

| platform | total_sales_before |total_sales_after | change_in_sales | percentage_of_change  |
|----------|--------------------|-------------------|-----------------|-----------------------|
| Retail   | 6906861113         | 6738777279        | -168083834      | -2.43                 |
| Shopify  | 219412034          | 235170474         | 15758440        | 7.18                  |

Sales drop 2.43% in Retail had the highest negative impact in sales metrics performance in 2020. And we can see that the Shopify platform has showed 7.18% growth. However, the growth of the Shopify platform did not compensate for the drop in Retail platform

- **age_band:**

````sql
WITH before_sales AS
(
SELECT 
  age_band,
  week_number,
  SUM(sales) AS before_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 13 AND 24) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  age_band
 ),
 after_sales AS
(
SELECT 
  age_band,
  week_number,
  SUM(sales) AS after_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 25 AND 36) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  age_band
 )
 
 SELECT 
   A.age_band, 
   SUM(A.before_sales) AS total_sales_before, 
   SUM(B.after_sales) AS total_sales_after, 
   SUM(B.after_sales)-SUM(A.before_sales) as change_in_sales,
   round(100*(SUM(B.after_sales)-SUM(A.before_sales))/SUM(A.before_sales),2) AS percentage_of_change
 FROM before_sales A
 LEFT JOIN after_sales B
 ON A.age_band=B.age_band
 GROUP BY A.age_band
````

| age_band     | total_sales_before | total_sales_after | change_in_sales | percentage_of_change  |
|--------------|--------------------|-------------------|-----------------|-----------------------|
| unknown      | 2764354464         | 2671961443        | -92393021       | -3.34                 |
| Middle Aged  | 1164847640         | 1141853348        | -22994292       | -1.97                 |
| Retirees     | 2395264515         | 2365714994        | -29549521       | -1.23                 |
| Young Adults | 801806528          | 794417968         | -7388560        | -0.92                 |

Sales drop 3.14% in unknown and 1.97% Middle Age have the highest negative impact in sales metrics performance in 2020. We can also see that the sales of all age_band values have dropped.

- **demographic:**

````sql
WITH before_sales AS
(
SELECT 
  demographic,
  week_number,
  SUM(sales) AS before_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 13 AND 24) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  demographic
 ),
 after_sales AS
(
SELECT 
  demographic,
  week_number,
  SUM(sales) AS after_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 25 AND 36) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  demographic
 )
 
 SELECT 
   A.demographic, 
   SUM(A.before_sales) AS total_sales_before, 
   SUM(B.after_sales) AS total_sales_after, 
   SUM(B.after_sales)-SUM(A.before_sales) as change_in_sales,
   round(100*(SUM(B.after_sales)-SUM(A.before_sales))/SUM(A.before_sales),2) AS percentage_of_change
 FROM before_sales A
 LEFT JOIN after_sales B
 ON A.demographic=B.demographic
 GROUP BY A.demographic
````

| age_band    | total_sales_before | total_sales_after | change_in_sales | percentage_of_change  |
|-------------|--------------------|-------------------|-----------------|-----------------------|
| unknown     | 2764354464         | 2671961443        | -92393021       | -3.34                 |
| Families    | 2328329040         | 2286009025        | -42320015       | -1.82                 |
| Couples     | 2033589643         | 2015977285        | -17612358       | -0.87                 |

Sales drop 3.34% in unknown and 1.82% in Families have the highest negative impact in sales metrics performance in 2020. We can also see that the sales of all age_band values have dropped.

- **customer_type:**

````sql
WITH before_sales AS
(
SELECT 
  customer_type,
  week_number,
  SUM(sales) AS before_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 13 AND 24) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  customer_type
 ),
 after_sales AS
(
SELECT 
  customer_type,
  week_number,
  SUM(sales) AS after_sales
FROM clean_weekly_sales
WHERE (week_number BETWEEN 25 AND 36) AND (calendar_year = 2020)
GROUP BY 
  week_number, 
  calendar_year,  
  customer_type
 )
 
 SELECT 
   A.customer_type, 
   SUM(A.before_sales) AS total_sales_before, 
   SUM(B.after_sales) AS total_sales_after, 
   SUM(B.after_sales)-SUM(A.before_sales) as change_in_sales,
   round(100*(SUM(B.after_sales)-SUM(A.before_sales))/SUM(A.before_sales),2) AS percentage_of_change
 FROM before_sales A
 LEFT JOIN after_sales B
 ON A.customer_type=B.customer_type
 GROUP BY A.customer_type
````

| customer_type | total_sales_before | total_sales_after | change_in_sales | percentage_of_change  |
|---------------|--------------------|-------------------|-----------------|-----------------------|
| Guest         | 2573436301         | 2496233635        | -77202666       | -3.00                 |
| Existing      | 3690116427         | 3606243454        | -83872973       | -2.27                 |
| New           | 862720419          | 871470664         | 8750245         | 1.01                  |

Sales drop 3% in Guest has the highest negative impact in sales metrics performance in 2020. However, the change_in_sales of existing customers has dropped 83872973, more than guest customers.

We can conclude that these areas of business had the highest negative impact in sales metrics performance in 2020: sales in Asia and Oceania, retail platform, Middle age and Families demographic group and guest customers. We can exclude the unknown values because we can't make decisions from this one.

In general, packaging issues had a negative sales impact but it was not the only reason for decreasing sales in 2020. Because when we look at the chart of 12 weeks before and after that, we can see there are some points the sales has dropped significantly than week 25 of 2020. Espacially in week 32, when the sales before that already increase but then drop deeply.
 
![index](https://user-images.githubusercontent.com/108384522/176727223-0bcd3a24-c987-4334-8e5b-cadc8f41b573.png)


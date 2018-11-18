### Teradata SQL - Exercise 03


#### EX01: How many distinct dates are there in the saledate column of the transaction table for each month/year combination in the database? 

SELECT EXTRACT(MONTH FROM t.saledate) as month_num,

	   EXTRACT(YEAR FROM t.saledate) as year_num,
	   
	   COUNT(DISTINCT t.saledate) as nb_distinct_dates

FROM trnsact t

GROUP BY month_num, year_num

ORDER BY year_num ASC, month_num ASC;


#### EX02: Use a CASE statement within an aggregate function to determine which sku had the greatest total sales during the combined summer months of June, July and August.

SELECT CASE WHEN EXTRACT(MONTH from t.saledate)=6 THEN 1

			WHEN EXTRACT(MONTH from t.saledate)=7 THEN 1
			
			WHEN EXTRACT(MONTH from t.saledate)=8 THEN 1 END AS month_group,
			
	   SUM(t.amt) as total_value_sold,
	   
	   t.sku as sku
	   
FROM trnsact t

WHERE t.stype='P'

HAVING month_group <> ''

GROUP BY sku, month_group

ORDER BY total_value_sold DESC;


#### EX03: How many distinct dates are there in the saledate column of the transaction table for each month/year/store combination in the database? Sort your results by the number of days per combination in ascending order.

SELECT t.store,

	   EXTRACT(YEAR FROM t.saledate) AS year_group,
	   
	   EXTRACT(MONTH FROM t.saledate) AS month_group,
	   
	   COUNT(DISTINCT t.saledate) AS nb_distinct_dates

FROM trnsact t

GROUP BY t.store, year_group, month_group

ORDER BY nb_distinct_dates ASC;


#### EX04: What is the average daily revenue for each store/month/year combination in the database? Calculate this by dividing the total revenue for a group by the number of sales days available in the transaction table for that group.

SELECT t.store,

	   EXTRACT(YEAR FROM t.saledate) AS year_group,
	   
	   EXTRACT(MONTH FROM t.saledate) AS month_group,
	   
	   SUM(t.amt)/COUNT(DISTINCT t.saledate) AS avg_revenue,
	   
	   COUNT(DISTINCT t.saledate) as nb_days
	   
FROM trnsact t

WHERE (t.stype='P') AND (EXTRACT(YEAR FROM t.saledate) <> 2015 AND EXTRACT(MONTH FROM t.saledate) <> 8)

HAVING nb_days > 19

GROUP BY t.store, year_group, month_group

ORDER BY t.store ASC, year_group ASC, month_group ASC;


#### EX05: What is the average daily revenue brought in by Dillard's stores in areas of high, medium or low levels of high school education?

SELECT (SUM(t.amt)/COUNT(DISTINCT t.saledate))/COUNT(DISTINCT t.store) AS avg_revenue,

	   COUNT(DISTINCT t.saledate) as nb_days,
	   
	   CASE WHEN s.msa_high >= 50.0  AND s.msa_high <= 60.0 THEN 'low'
	   
			WHEN s.msa_high >= 60.01 AND s.msa_high <= 70.0 THEN 'medium'
			
			WHEN s.msa_high >= 70.01 THEN 'high' END AS school_group

FROM trnsact t JOIN store_msa s ON (t.store=s.store)

WHERE (t.stype='P') AND (EXTRACT(YEAR FROM t.saledate) <> 2015 AND EXTRACT(MONTH FROM t.saledate) <> 8)

HAVING nb_days > 19

GROUP BY school_group;


#### EX06: Compare the average daily revenues of the stores with the highest median msa_income and the lowest median msa_income. In what city and state were these stores, and which store had a higher average daily revenue?

SELECT t.store,

	   (SUM(t.amt)/COUNT(DISTINCT t.saledate)) AS avg_revenue,
	   
	   COUNT(DISTINCT t.saledate) as nb_days,
	   
	   s.city,
	   
	   s.state,
	   
	   s.msa_income

FROM trnsact t JOIN store_msa s ON (t.store=s.store)

WHERE (t.stype='P')

  AND (EXTRACT(YEAR FROM t.saledate) <> 2015)
  
  AND (EXTRACT(MONTH FROM t.saledate) <> 8)
  
  AND ((msa_income=(SELECT MAX(msa_income) FROM store_msa)) OR (msa_income=(SELECT MIN(msa_income) FROM store_msa)))

HAVING nb_days > 19

GROUP BY t.store, s.city, s.state, s.msa_income;


#### EX07: What is the brand of the sku with the greatest standard deviation in sprice? Only examine skus that have been part of over 100 transactions.

SELECT s.sku AS x_sku,

	   s.brand AS brand,
	   
	   COUNT(t.saledate) AS nb_transactions,
	   
	   STDDEV_SAMP(t.sprice) as std_price

FROM trnsact t JOIN skuinfo s ON (t.sku=s.sku)

WHERE t.stype='P'

HAVING nb_transactions > 100

GROUP BY x_sku, brand

ORDER BY std_price DESC;


#### EX09: What was the average daily revenue Dillard's brought in during each month of the year?


SELECT EXTRACT(YEAR FROM t.saledate) AS year_group,

	   EXTRACT(MONTH FROM t.saledate) AS month_group,

	   (SUM(t.amt)/COUNT(DISTINCT t.saledate))/COUNT(DISTINCT t.store) AS avg_revenue,

       COUNT(DISTINCT t.saledate) as nb_days
	   
FROM trnsact t

WHERE (t.stype='P') AND (EXTRACT(YEAR FROM t.saledate) <> 2015 AND EXTRACT(MONTH FROM t.saledate) <> 8)

HAVING nb_days > 19

GROUP BY year_group, month_group

ORDER BY year_group ASC, month_group ASC;


#### EX10: Which department, in which city and state of what store, had the greatest % increase in average daily sales revenue from November to December?

SELECT df.dept,

       df.store,
	   
       s.city,
	   
       s.state,
	   
       SUM(CASE WHEN EXTRACT(MONTH FROM df.saledate)=11 THEN df.amt END) AS Sales_11,
	   
       SUM(CASE WHEN EXTRACT(MONTH FROM df.saledate)=12 THEN df.amt END) AS Sales_12,
	   
       COUNT(DISTINCT CASE WHEN EXTRACT(MONTH FROM df.saledate)=11 THEN df.saledate END) AS Days_11,
	   
       COUNT(DISTINCT CASE WHEN EXTRACT(MONTH FROM df.saledate)=12 THEN df.saledate END) AS Days_12,
	   
       Sales_11/Days_11 AS DailySales_11,
	   
       Sales_12/Days_12 AS DailySales_12,
	   
       (DailySales_12/DailySales_11 - 1) AS d_DailySales

FROM
(
SELECT s.dept, t.store, t.amt, t.saledate

FROM skuinfo s INNER JOIN trnsact t ON (t.sku=s.sku)

WHERE (t.stype='P') AND (EXTRACT(YEAR FROM t.saledate) <> 2015 AND EXTRACT(MONTH FROM t.saledate) <> 8)

) AS df

INNER JOIN strinfo s ON (s.store=df.store)

GROUP BY df.dept, df.store, s.city, s.state

HAVING (Days_11>19) AND (Days_12>19)

ORDER BY d_DailySales DESC;


#### EX11: What is the city and state of the store that had the greatest decrease in average daily revenue from August to September?

SELECT df.store,

       s.city,
	   
       s.state,
	   
       SUM(CASE WHEN EXTRACT(MONTH FROM df.saledate)=8 THEN df.amt END) AS Sales_8,
	   
       SUM(CASE WHEN EXTRACT(MONTH FROM df.saledate)=9 THEN df.amt END) AS Sales_9,
	   
       COUNT(DISTINCT CASE WHEN EXTRACT(MONTH FROM df.saledate)=8 THEN df.saledate END) AS Days_8,
	   
       COUNT(DISTINCT CASE WHEN EXTRACT(MONTH FROM df.saledate)=9 THEN df.saledate END) AS Days_9,
	   
       Sales_8/Days_8 AS DailySales_8,
	   
       Sales_9/Days_9 AS DailySales_9,
	   
       (DailySales_9 - DailySales_8) AS d_DailySales

FROM
(
SELECT t.store, t.amt, t.saledate

FROM trnsact t

WHERE (t.stype='P') AND t.saledate<'2005-08-01'

) AS df

INNER JOIN strinfo s ON (s.store=df.store)

GROUP BY df.store, s.city, s.state

HAVING (Days_9>19) AND (Days_8>19)

ORDER BY d_DailySales ASC;
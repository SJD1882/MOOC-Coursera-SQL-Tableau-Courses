### Teradata SQL - Quiz 02


#### Basics

		DATABASE ua_dillards;

		SELECT TOP 5 * FROM STRINFO;

		SELECT TOP 5 * FROM SKSTINFO;

		SELECT TOP 5 * FROM SKUINFO;

		SELECT TOP 5 * FROM DEPTINFO;

		SELECT TOP 5 * FROM TRNSACT;

		SELECT TOP 5 * FROM STORE_MSA;


#### Q3: On what day was Dillard’s income based on total sum of purchases the greatest

		SELECT saledate, SUM(QUANTITY) AS nb_purchases, SUM(QUANTITY*SPRICE) AS total_income

		FROM trnsact

		GROUP BY saledate

		WHERE stype='P'

		ORDER BY total_income DESC;


#### Q4: What is the deptdesc of the departments that have the top 3 greatest numbers of skus from the skuinfo table associated with them?

		SELECT d.dept AS dept_1, d.deptdesc, s.dept AS dept_2, COUNT(s.sku) AS nb_sku

		FROM deptinfo d

		LEFT JOIN skuinfo s ON (d.dept=s.dept)

		GROUP BY d.dept, d.deptdesc, s.dept

		ORDER BY nb_sku DESC;


#### Q7: What is the average amount of profit Dillard’s made per day?

		SELECT SUM(t.amt - t.quantity*s.cost)/COUNT(DISTINCT t.saledate) AS AVGProfit

		FROM trnsact t LEFT JOIN SKSTINFO s

		ON (t.sku=s.sku) AND (t.store=s.store)

		WHERE t.stype = 'p';


#### Q10: How many stores have more than 180,000 distinct skus associated with them in the skstinfo table?

		SELECT a.store, COUNT(DISTINCT b.sku) nb_distinct_stores
		
		FROM strinfo a, skstinfo b
		
		WHERE a.store=b.store
		
		GROUP BY a.store
		
		HAVING nb_distinct_stores > 180000
		
		ORDER BY nb_distinct_stores DESC;

		
#### Q13: In what city and state is the store that had the greatest total sum of sales?
		

		SELECT a.city, a.state, a.store, SUM(b.AMT) AS total_sales
		
		FROM store_msa a
		
		LEFT JOIN trnsact b ON (a.store=b.store)
		
		GROUP BY a.city, a.state, a.store
		
		ORDER BY total_sales DESC;
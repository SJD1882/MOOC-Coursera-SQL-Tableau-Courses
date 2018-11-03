### Teradata SQL - Exercise 02


#### EX01: Use COUNT and DISTINCT to determine how many distinct skus there are in pairs of the skuinfo, skstinfo and trnsact tables. Which skus are common to pairs of tables or unique to specific tables?

		DATABASE ua_dillards;

		SELECT a.sku AS skuinfo_sku, b.sku AS skstinfo_sku, c.sku AS trnsact_sku, COUNT(*) AS Nb_Distinct_Pairs

		FROM skuinfo a

		LEFT JOIN skstinfo b ON (a.sku = b.sku)

		LEFT JOIN trnsact c  ON (a.sku = c.sku)

		GROUP BY a.sku, b.sku, c.sku

		ORDER BY Nb_Distinct_Pairs DESC

		HAVING Nb_Distinct_Pairs = 1

		;


#### EX02: Use COUNT to determine how many instances there are of each sku associated with each store in the skstinfo and the trnsact table?

		DATABASE ua_dillards;

		SELECT a.sku AS skstinfo_sku, a.store AS skstinfo_store, b.sku AS trnsact_sku, b.store AS trnsact_store, COUNT(*) AS Nb_Counts

		FROM skstinfo a

		LEFT JOIN trnsact b ON (a.sku=b.sku) AND (a.store=b.store)

		GROUP BY a.sku, b.sku, a.store, b.store

		ORDER BY Nb_Counts DESC;


#### EX03: Examine some of the rows in the trnsact table that are not in the skstinfo table; can you find any common features that could explain why the cost information is missing?

		DATABASE ua_dillards;

		SELECT TOP 100 *

		FROM skstinfo a RIGHT JOIN trnsact b ON (a.sku=b.sku) AND (a.store=b.store)

		WHERE (a.sku IS NULL) AND (a.store IS NULL);


#### EX04: What is Dillard's average profit per day?

		DATABASE ua_dillards;

		SELECT SUM(t.amt - t.quantity*s.cost)/COUNT(DISTINCT t.saledate) AS AVGProfit

		FROM trnsact t LEFT JOIN SKSTINFO s

		ON (t.sku=s.sku) AND (t.store=s.store)

		WHERE t.stype = 'p';


#### EX05: On what day was the total value (in $) of returned goods the greatest? On what day was the total number of individual returned items the greatest?

		DATABASE ua_dillards;

		SELECT t.saledate, SUM(t.AMT) AS sum_amount
		FROM trnsact t
		GROUP BY t.saledate
		WHERE stype='r'
		ORDER BY sum_amount DESC;

		SELECT t.saledate, SUM(t.QUANTITY) AS sum_amount
		FROM trnsact t
		GROUP BY t.saledate
		WHERE stype='r'
		ORDER BY sum_amount DESC;


#### EX06: What is the maximum price paid for an item in our database? What is the minimum price paid for an item in our database?

		DATABASE ua_dillards;

		SELECT MAX(t.sprice) AS max_price, MIN(t.sprice) AS min_price, t.sku
		FROM trnsact t
		GROUP BY t.sku
		ORDER BY min_price ASC;


#### EX07: How many departments have more than 100 brands associated with them, and what are their descriptions?

		DATABASE ua_dillards;

		SELECT s.dept as department, COUNT(DISTINCT s.brand) as nb_distinct_brands

		FROM skuinfo s

		GROUP BY s.dept

		HAVING nb_distinct_brands > 100

		ORDER BY nb_distinct_brands DESC;


#### EX08: Write a query that retrieves the department descriptions of each of the skus in the skstinfo table.

		DATABASE ua_dillards;

		SELECT a.sku AS skstinfo_sku, b.sku AS skuinfo_sku, b.dept AS skuinfo_dept, c.dept AS deptinfo_dept, c.deptdesc AS deptinfo

		FROM skstinfo a

		LEFT JOIN skuinfo  b ON (a.sku=b.sku)

		LEFT JOIN deptinfo c ON (b.dept=c.dept)

		WHERE a.sku=5020024;


#### EX09: What department (with department description), brand, style, and color had the greatest total value of returned items?

		DATABASE ua_dillards;

		SELECT TOP 5 * FROM trnsact;

		SELECT a.deptdesc, a.dept AS a_dept, b.dept AS b_dept, b.brand, b.style, b.color, b.sku AS b_sku, c.sku AS c_sku, sku_SUM(c.AMT) AS total_value

		FROM deptinfo a

		LEFT JOIN skuinfo b ON (a.dept=b.dept)

		LEFT JOIN trnsact c ON (b.sku=c.sku)

		WHERE c.stype='R'

		GROUP BY a.deptdesc, a.dept, b.dept, b.brand, b.style, b.color, b.sku, c.sku

		ORDER BY total_value DESC;
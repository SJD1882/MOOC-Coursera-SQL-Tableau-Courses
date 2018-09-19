### Teradata SQL - Exercise 01

DATABASE ua_dillards;

HELP TABLE strinfo;

SELECT * FROM strinfo WHERE city IS NULL;
SELECT * FROM strinfo WHERE state IS NULL;
SELECT * FROM strinfo WHERE zip IS NULL;

SELECT MAX(orgprice) FROM trnsact WHERE sku=3631365;

SELECT color, sku
FROM skuinfo
WHERE brand='LIZ CLAI'
ORDER BY sku DESC;

SELECT sku, MAX(orgprice)
FROM trnsact
GROUP BY sku
ORDER BY MAX(orgprice) DESC;

SELECT DISTINCT state FROM strinfo;

SELECT * FROM deptinfo WHERE deptdesc LIKE 'e%';

SELECT TOP 5 saledate, orgprice, sprice, orgprice - sprice AS diffprice
FROM trnsact
WHERE (orgprice <> sprice)
ORDER BY saledate ASC, diffprice DESC;

SELECT TOP 5 saledate, register, orgprice, sprice
FROM trnsact
WHERE saledate BETWEEN '2004-08-01' AND '2004-08-10'
ORDER BY orgprice DESC, sprice DESC;

SELECT DISTINCT brand
FROM skuinfo
WHERE brand LIKE '%liz%';

SELECT *
FROM store_msa
WHERE city IN ('little rock', 'memphis', 'tulsa')
ORDER BY store;
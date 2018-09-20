### Teradata SQL - Exercise 01

#### EX01

DATABASE ua_dillards;

#### EX02

HELP TABLE strinfo;

#### EX03

SELECT * FROM strinfo WHERE city IS NULL;

SELECT * FROM strinfo WHERE state IS NULL;

SELECT * FROM strinfo WHERE zip IS NULL;

#### EX04

SELECT MAX(orgprice) FROM trnsact WHERE sku=3631365;

#### EX05

SELECT color, sku

FROM skuinfo

WHERE brand='LIZ CLAI'

ORDER BY sku DESC;

#### EX06

SELECT sku, MAX(orgprice)

FROM trnsact

GROUP BY sku

ORDER BY MAX(orgprice) DESC;

#### EX07

SELECT DISTINCT state FROM strinfo;

SELECT * FROM deptinfo WHERE deptdesc LIKE 'e%';

#### EX08

SELECT TOP 5 saledate, orgprice, sprice, orgprice - sprice AS diffprice
FROM trnsact
WHERE (orgprice <> sprice)
ORDER BY saledate ASC, diffprice DESC;

#### EX09

SELECT TOP 5 saledate, register, orgprice, sprice
FROM trnsact
WHERE saledate BETWEEN '2004-08-01' AND '2004-08-10'
ORDER BY orgprice DESC, sprice DESC;

#### EX10

SELECT DISTINCT brand
FROM skuinfo
WHERE brand LIKE '%liz%';

#### EX11

SELECT *
FROM store_msa
WHERE city IN ('little rock', 'memphis', 'tulsa')
ORDER BY store;
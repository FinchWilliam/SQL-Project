# What issues will you address by cleaning the data?
1. One of the first things that jumped out at me was the cities being unavailable in this dataset, I started by adjusting it so that whenever there was not city set I would look at the country instead, however that throws off averages when looking at individual cities, So I will have to remove those results for when we are looking at the city level of things.

2. Checking through to find primary keys (if any) and seeing where I can connect tables together

3. double checking for duplicate data, for example the sentimentmagnitude and sentiment score columns

4. checking for different data in different tables, for example the sales_report table and the Sales_by_sku table have almost identical total_ordered columns, with a few datapoints in the sales_by_sku table that are not in the reports table

5. Checking wether fullvisitorid's are unique to a country and city (they are not)
## Queries:
### Below, provide the SQL queries you used to clean your data.
--1.
~~~SQL
WITH cities_cleaned AS /** This will remove the non-set citys from the equation **/
(
	SELECT 
		country,
		CASE
			WHEN city LIKE 'not avail%' THEN country
			WHEN city LIKE '%not se%' THEN country
			ELSE city
		END AS cities
	FROM
		all_sessions
)
SELECT 
	country,
	cities,
	COUNT(cities)
FROM
	cities_cleaned
WHERE
	cities != country
	/** this step is only included when I am looking for averages across the city level as this will throw off the averages **/
GROUP BY 
	country, cities;
~~~

--2.
~~~SQL
SELECT /** This query will tell me if there is more than one of any data point in the column so I know if it is a PK **/
	fullvisitorid, 
	COUNT(*)
FROM 
	all_sessions
GROUP BY 
	fullvisitorid
HAVING 
	COUNT(*) > 1;
~~~

--3. 
~~~SQL
SELECT /** checking if the "sentiment score" and "sentimentmagnitude" columns are just repeated data **/
	p.sku,
	p.productname,
	p.sentimentscore,
	sr.sentimentscore,
	p.sentimentmagnitude,
	sr.sentimentmagnitude
FROM 
	products p
JOIN 
	sales_report sr
	ON p.sku = sr.productsku
WHERE p.sentimentscore != sr.sentimentscore OR p.sentimentmagnitude != sr.sentimentmagnitude;
~~~

--4.
~~~SQL 
SELECT /** checking if the total_ordered is the same across the two tables **/
	sbs.productsku,
	sbs.total_ordered AS salestbl,
	sr.total_ordered AS reportstbl
FROM 
	sales_by_sku sbs
full JOIN 
	sales_report sr
	ON sr.productsku = sbs.productsku
WHERE 
	sbs.total_ordered IS NULL OR sr.total_ordered IS NULL;
~~~

--5.
~~~SQL
WITH cities_count AS
(
SELECT 
	fullvisitorid, 
	country,
	city,
	count(*)
FROM 
	all_sessions
GROUP BY 
	fullvisitorid, country, city
)
SELECT 
	fullvisitorid,
	count(*)
FROM 
	cities_count
GROUP BY
	fullvisitorid
HAVING count(*) > 1;
~~~

-- followed by spot checking the results to see where they differ
~~~SQL
select
	* 
FROM 
	all_sessions 
WHERE 
	fullvisitorid = 7.199240861547272e+18;
~~~

~~~SQL
CREATE OR REPLACE VIEW cl_analytics AS /** create a view of the analytics table without the null columns and with prices / 1000000 **/
SELECT 
	visitnumber,
	visitid,
	visitstarttime,
	date,
	fullvisitorid,
	channelgrouping,
	socialengagementtype,
	units_sold,
	pageviews,
	timeonsite,
	bounces,
	revenue / 1000000 AS revenue,
	unit_price / 1000000 AS unit_price
FROM analytics;
~~~

~~~SQL
CREATE OR REPLACE VIEW cl_all_sessions AS /** creating a cleaned up all_sessions page, moving some information to products page and fixing some nulls and prices. **/
SELECT 
	fullvisitorid,
	channelgrouping,
	time,
	country,
	CASE
		WHEN city LIKE 'not avail%' THEN country
		WHEN city LIKE '%not se%' THEN country
		ELSE city
		END AS city,
	COALESCE(totaltransactionrevenue, 0)/1000000 AS totaltransactionrevenue,
	COALESCE(transactions, 0)::boolean AS transactions,
	timeonsite,
	pageviews,
	sessionqualitydim,
	date,
	visitid,
	type_,
	COALESCE(productquantity, 0) AS productquantity,
	productprice / 1000000 AS productprice,
	COALESCE(productrevenue, 0) / 1000000 AS productrevenue,
	productsku,
	COALESCE(productvariant, '(not set)') AS productvariant,
	v2productname,
	v2productcategory,
	currencycode,
	COALESCE(itemquantity, 0) AS itemquantity,
	COALESCE(itemrevenue, 0) / 1000000 AS itemrevenue,
	COALESCE(transactionrevenue, 0) / 1000000 AS transactionrevenue,
	transactionid,
	pagetitle,
	searchkeyword,
	pagepathlevel1,
	ecommerceaction_type,
	ecommerceaction_step,
	ecommerceaction_option
FROM
	all_sessions;
~~~

~~~SQL
SELECT /** Cleaning the products category to remove the parent categories  and simplify for the sake of generalisations across countries **/
	DISTINCT v2productcategory,
	CASE
		WHEN LOWER(v2productcategory) LIKE '%apparel%' THEN 'Apparel'
		WHEN LOWER(v2productcategory) LIKE '%drinkware%' THEN 'Drinkware'
		WHEN LOWER(v2productcategory) LIKE '%bags%' THEN 'Bags'
		WHEN LOWER(v2productcategory) LIKE '%electronics%' THEN 'Electronics'
		WHEN LOWER(v2productcategory) LIKE '%accessories%' THEN 'Accessories'
		WHEN LOWER(v2productcategory) LIKE '%drinkware%' THEN 'Drinkware'
		WHEN LOWER(v2productcategory) LIKE '%nest%' THEN 'Nest'
		WHEN LOWER(v2productcategory) LIKE '%office%' THEN 'Office'
		WHEN LOWER(v2productcategory) LIKE '%housewares%' THEN 'Housewares'
		WHEN LOWER(v2productcategory) LIKE '%esccat%' THEN 'Misc'
		WHEN LOWER(v2productcategory) LIKE '%lifestyle%' THEN 'Lifestyle'
		WHEN LOWER(v2productcategory) LIKE '%sale%' THEN 'Sale'
		WHEN LOWER(v2productcategory) LIKE '%waze%' THEN 'Waze'
		WHEN LOWER(v2productcategory) LIKE '%android%' THEN 'Android'
		WHEN LOWER(v2productcategory) LIKE '%wearable%' THEN 'Apparel'
		WHEN LOWER(v2productcategory) LIKE '%gift%' THEN 'Gift Cards'
		WHEN LOWER(v2productcategory) LIKE '%youtube%' THEN 'Branded'
		WHEN LOWER(v2productcategory) LIKE '%google%' THEN 'Branded'
		WHEN LOWER(v2productcategory) LIKE '%brand%' THEN 'Misc'
		WHEN LOWER(v2productcategory) LIKE '%games%' THEN 'Games'
		WHEN LOWER(v2productcategory) LIKE '%fun%' THEN 'Games'
		WHEN LOWER(v2productcategory) LIKE '%limited%' THEN 'Limited'
		WHEN LOWER(v2productcategory) LIKE '%kids%' THEN 'Kids'
		ELSE regexp_replace(v2productcategory, '/', '','gi') /** this probably would have been simpler with a few regexp expressions to remove things however this method allowed me to see exactly what I was changing at every step. **/
	END AS clean_categories
FROM
	all_sessions
~~~
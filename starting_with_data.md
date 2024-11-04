## Question 1: How much does the average item cost in each city/country

### SQL Queries:
~~~SQL
WITH sell_count AS /** this first cte counts the amount of each item sold per city**/
(
	SELECT 
		p.productname,
		als.country,
		CASE /** to clean up the "not available in free data set" and "not set" options **/
			WHEN als.city LIKE 'not avail%' THEN als.country
			WHEN als.city LIKE '%not se%' THEN als.country
			ELSE als.city
		END AS city,
		AVG(als.productprice)/1000000 AS avg_price, /** gets the average price of all sold items and adjusts them **/
		count(*) AS number_sold
	FROM
		all_sessions als
	JOIN
		products p
	ON
		p.sku = als.productsku
	WHERE 
		als.transactions IS NOT NULL /** products that were actually purchased **/
	GROUP BY
		p.productname,
		als.city,
		als.country
),
ranked_count AS /** this cte will rank the items sold in each city **/
(
	SELECT
		*,
		DENSE_RANK() OVER (PARTITION BY city ORDER BY number_sold DESC) AS sell_rank_city
	FROM
		sell_count
)
SELECT /** finally we just select the top ranking items from each city **/
	rc.country, 
	rc.city,
	rc.productname,
	rc.number_sold,
	sc.avg_price AS avg_per_city,
	AVG(sc.avg_price) OVER (PARTITION BY rc.country) AS avg_per_country,
	SUM(rc.number_sold) OVER ()
FROM 
	ranked_count rc
JOIN
	sell_count sc
ON 
	rc.productname = sc.productname AND rc.city = sc.city AND rc.country = sc.country
WHERE
	sell_rank_city = 1 
ORDER BY
	country,
	city
~~~
### Answer: 

|	country	|	city	|	productname	|	number_sold	|	avg_per_city	|	avg_per_country	|	sum	|
|	---	|	---	|	---	|	---	|	---	|	---	|	---	|
|	Australia	|	Sydney	|	 Cam Indoor Security Camera - USA	|	1	|	119	|	119	|	51	|
|	Canada	|	New York	|	 Men's  Zip Hoodie	|	1	|	55.99	|	55.99	|	51	|
|	Switzerland	|	Zurich	|	 Men's 3/4 Sleeve Henley	|	1	|	24.99	|	24.99	|	51	|
|	United States	|	Atlanta	|	 Men's Short Sleeve Hero Tee Heather	|	1	|	18.99	|	65.02086957	|	51	|
|	United States	|	Austin	|	 Men's 100% Cotton Short Sleeve Hero Tee Black	|	1	|	13.59	|	65.02086957	|	51	|
|	United States	|	Austin	|	 Men's 100% Cotton Short Sleeve Hero Tee Navy	|	1	|	13.59	|	65.02086957	|	51	|
|	United States	|	Chicago	|	Rubber Grip Ballpoint Pen 4 Pack	|	1	|	3.99	|	65.02086957	|	51	|
|	United States	|	Chicago	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|	298	|	65.02086957	|	51	|
|	United States	|	Chicago	|	 Sunglasses	|	1	|	2.8	|	65.02086957	|	51	|
|	United States	|	Columbus	|	 Men's Short Sleeve Badge Tee Charcoal	|	1	|	18.99	|	65.02086957	|	51	|
|	United States	|	Houston	|	26 oz Double Wall Insulated Bottle	|	1	|	24.99	|	65.02086957	|	51	|
|	United States	|	Los Angeles	|	 Cam Outdoor Security Camera - USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	Los Angeles	|	 Women's Short Sleeve Hero Tee White	|	1	|	16.99	|	65.02086957	|	51	|
|	United States	|	Mountain View	|	 Mobile Phone Vent Mount	|	1	|	5.59	|	65.02086957	|	51	|
|	United States	|	Mountain View	|	 Men's Vintage Badge Tee Sage	|	1	|	10.63	|	65.02086957	|	51	|
|	United States	|	Mountain View	|	Android Women's Fleece Hoodie	|	1	|	55.99	|	65.02086957	|	51	|
|	United States	|	Mountain View	|	Android Infant Short Sleeve Tee Pewter	|	1	|	16.99	|	65.02086957	|	51	|
|	United States	|	Mountain View	|	 Men's Bike Short Sleeve Tee Charcoal	|	1	|	15.99	|	65.02086957	|	51	|
|	United States	|	Mountain View	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|	149	|	65.02086957	|	51	|
|	United States	|	Nashville	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|	149	|	65.02086957	|	51	|
|	United States	|	New York	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|	249	|	65.02086957	|	51	|
|	United States	|	New York	|	 Luggage Tag	|	1	|	8.99	|	65.02086957	|	51	|
|	United States	|	New York	|	 Men's 100% Cotton Short Sleeve Hero Tee White	|	1	|	13.59	|	65.02086957	|	51	|
|	United States	|	New York	|	 Laptop and Cell Phone Stickers	|	1	|	2.99	|	65.02086957	|	51	|
|	United States	|	New York	|	 Men's Short Sleeve Performance Badge Tee Pewter	|	1	|	21.99	|	65.02086957	|	51	|
|	United States	|	New York	|	 Onesie Red/Graphite	|	1	|	23.99	|	65.02086957	|	51	|
|	United States	|	New York	|	 Cam Outdoor Security Camera - USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	Palo Alto	|	 Learning Thermostat 3rd Gen-USA - White	|	1	|	149	|	65.02086957	|	51	|
|	United States	|	Palo Alto	|	 Cam Outdoor Security Camera - USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	Palo Alto	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|	149	|	65.02086957	|	51	|
|	United States	|	San Bruno	|	 Men's Vintage Badge Tee White	|	1	|	18.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	 Women's 3/4 Sleeve Baseball Raglan Heather/Black	|	1	|	19.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	 Women's Scoop Neck Tee White	|	1	|	15.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	 Protect Smoke + CO White Battery Alarm-USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	20 oz Stainless Steel Insulated Tumbler	|	1	|	19.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	Android 17oz Stainless Steel Sport Bottle	|	1	|	15.19	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	Windup Android	|	1	|	3.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	Android Rise 14 oz Mug	|	1	|	12.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	Waterproof Backpack	|	1	|	79.99	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	 Cam Outdoor Security Camera - USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	San Francisco	|	 Tri-blend Hoodie Grey	|	1	|	39.99	|	65.02086957	|	51	|
|	United States	|	San Jose	|	 Cam Outdoor Security Camera - USA	|	1	|	199	|	65.02086957	|	51	|
|	United States	|	San Jose	|	 Men's  Zip Hoodie	|	1	|	44.79	|	65.02086957	|	51	|
|	United States	|	Seattle	|	 Cam Indoor Security Camera - USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	Sunnyvale	|	SPF-15 Slim & Slender Lip Balm	|	1	|	1.4	|	65.02086957	|	51	|
|	United States	|	Sunnyvale	|	Android Men's Vintage Henley	|	1	|	23.99	|	65.02086957	|	51	|
|	United States	|	Sunnyvale	|	 Cam Indoor Security Camera - USA	|	1	|	119	|	65.02086957	|	51	|
|	United States	|	United States	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	2	|	149	|	65.02086957	|	51	|
|	United States	|	United States	|	 Protect Smoke + CO White Wired Alarm-USA	|	2	|	79	|	65.02086957	|	51	|




## Question 2: What is the max/min and average cost of each item, broken down by country and city

### SQL Queries:
~~~SQL
WITH cleaned AS
(
	SELECT 
		country,
		CASE /** to clean up the "not available in free data set" and "not set" options **/
			WHEN city LIKE 'not avail%' THEN country
			WHEN city LIKE '%not se%' THEN country
			ELSE city
		END AS city,
		CASE /** Clean up the product category section **/
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
			WHEN LOWER(v2productcategory) LIKE '%not set%' THEN 'Misc'
			ELSE regexp_replace(v2productcategory, '/', '','gi')
		END AS categories,
		productprice/1000000 AS productprice
	FROM
		all_sessions
),
avg_calculations AS /** to calculate my max and minimums and averages **/
( 
	SELECT
		country,
		city,
		categories,
		MAX(productprice) OVER (PARTITION BY categories) AS max_category,
		MIN(productprice) OVER (PARTITION BY categories) AS min_category,
		AVG(productprice) OVER (PARTITION BY categories) AS avg_category,
		MAX(productprice) OVER (PARTITION BY country, categories) AS max_country,
		MIN(productprice) OVER (PARTITION BY country, categories) AS min_country,
		AVG(productprice) OVER (PARTITION BY country, categories) AS avg_country,
		MAX(productprice) OVER (PARTITION BY city, categories) AS max_city,
		MIN(productprice) OVER (PARTITION BY city, categories) AS min_city,
		AVG(productprice) OVER (PARTITION BY city, categories) AS avg_city
	FROM
		cleaned
)
SELECT DISTINCT /** removes duplicate values for clean looking **/
	*
FROM
	avg_calculations
ORDER BY
	categories
~~~

### Answer:
We can see a big variation on the price of the products ordered in each category depending on what the country we're looking at is.


## Question 3: what does the spread of item cost look like according to different countries.

### SQL Queries:
~~~SQL
WITH price_ranked AS /** rank my prices to find the highest and lowest with the countries **/
(
	SELECT
		country,
		productsku,
		productprice/1000000 AS productprice,
		RANK() OVER (PARTITION BY productsku ORDER BY productprice) AS low_high,
		RANK() OVER (PARTITION BY productsku ORDER BY productprice DESC) AS high_low
	FROM
		all_sessions
	WHERE
		productprice != 0
),
min_max AS
(
	SELECT /** remove all products and duplicates that have the same price across all countries and limit it to my biggest and smallest values. **/
		DISTINCT *
	FROM
		price_ranked
	WHERE 
		NOT (low_high = high_low AND low_high = 1) AND (high_low = 1 OR low_high = 1)
)
SELECT /**give me an easy to read list of the price it is in different countries and who is the cheapest and who is most expensive **/
	p.productname,
	mm.productprice,
	CASE
		WHEN low_high = 1 THEN country
		ELSE NULL
	END AS cheapest_country,
	CASE
		WHEN high_low = 1 THEN country
		ELSE NULL
	END AS expensive_country
FROM 
	min_max mm
JOIN
	products p
ON 
	mm.productsku = p.sku
~~~
### Answer:
running through the results gives us an overwhelming trend towards United States in the cheapest location for products.


## Question 4: how many customers do we have using the website three or more times?

### SQL Queries:
~~~SQL
WITH visit_list AS
(
	SELECT DISTINCT /** gives me a list of distinct visitnumbers and there visitorid's **/
		visitnumber,
		fullvisitorid
	FROM
		analytics
	WHERE 
		visitnumber >= 3 /** change this to see how many unique visitors we have had more than 1 **/
)
SELECT /** count the number of unique visitors **/
	count(*)
FROM
	visit_list
~~~

### Answer:
at least 3 visits = 21425 times
at least 100 visits = 208 times
at least 200 visits = 63 times
at least 300 visits = 33 times

## Question 5: Lets compare the amount(value and number of) of purchases from single visit users vs return users

### SQL Queries:
~~~SQL 
SELECT DISTINCT
	fullvisitorid,
	SUM(totaltransactionrevenue) OVER ()/1000000 AS total_sold /**sum all sales **/
FROM
	all_sessions
WHERE 
	transactions = 1 AND fullvisitorid NOT IN 
	(
		SELECT DISTINCT /** Find a list of clients who have used the sight more than once **/
			fullvisitorid
		FROM 
			analytics
		WHERE 
			visitnumber > 1
	)
~~~
### Answer:
	we have 59 purchases with a value of $10,784.86 for single visit clients and only 21 with a value of $3496.45 for return clients
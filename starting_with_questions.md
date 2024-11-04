# Answer the following questions and provide the SQL queries used to find the answer.

    
## **Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


### SQL Queries:
~~~SQL
SELECT /** I ran this as two seperate queries, the first will get my highest performing city **/
	country,
	CASE /** to clean up the "not available in free data set" and "not set" options **/
		WHEN city LIKE 'not avail%' THEN country
		WHEN city LIKE '%not se%' THEN country
		ELSE city
	END AS cities,
	SUM(totaltransactionrevenue)/1000000 AS total_per_city /** divide by 1 million to account for data **/
FROM 
	all_sessions
WHERE 
	totaltransactionrevenue IS NOT NULL /** removes all no sale visits **/
GROUP BY
	country,
	city
ORDER BY
	total_per_city DESC;
	
SELECT /** this query will get my highest performing Country **/
	country,
	SUM(totaltransactionrevenue)/1000000 AS total_per_country /** divide by 1 million to account for data **/
FROM 
	all_sessions
WHERE 
	totaltransactionrevenue IS NOT NULL /** removes all no sale visits **/
GROUP BY
	country
ORDER BY
	total_per_country DESC
~~~

### Answer:
We had $6092.56 worth of sales in cities that were undefined, 
 - cities San Fransico $1564.32
 - highest country was United States with $13154.17




## **Question 2: What is the average number of products ordered from visitors in each city and country?**


### SQL Queries:
~~~SQL
WITH sold_count AS /** sumthe number of units sold linking them to unique visit id's **/
(
	SELECT 
		visitid,
		sum(units_sold) AS visit_sales
	FROM
		analytics
	WHERE
		units_sold IS NOT NULL
	GROUP BY
		visitid
),
city_list AS /** create a list of citys and countrys (remnaming the not-set and the not available ones) **/
(
	SELECT 
	DISTINCT visitid, 
	country,
	CASE /** to clean up the "not available in free data set" and "not set" options **/
		WHEN city LIKE 'not avail%' THEN country
		WHEN city LIKE '%not se%' THEN country
		ELSE city
	END AS city
	FROM all_sessions
),
avg_count AS /** create averages by city and country **/
(
	SELECT
		cl.country, 
		cl.city, 
		AVG(sc.visit_sales) OVER (PARTITION BY city)::REAL AS avg_per_city,
		AVG(sc.visit_sales) OVER (PARTITION BY country)::REAL AS avg_per_country
	FROM 
		sold_count sc
	JOIN
		city_list cl USING (visitid)
	ORDER BY
		cl.country,
		cl.city
)
SELECT /** return distinct results so I don't see the repeated information **/
	DISTINCT *
FROM 
	avg_count;
~~~

### Answer:

<details>

|	country	|	city	|	avg_per_city	|	avg_per_country	|
|	---	|	---	|	---	|	---	|
|	Australia	|	Australia	|	2	|	2	|
|	Austria	|	Austria	|	1	|	1	|
|	Belgium	|	Belgium	|	2	|	2	|
|	Bulgaria	|	Bulgaria	|	3	|	3	|
|	Canada	|	Canada	|	3	|	3.142857	|
|	Canada	|	Toronto	|	3.5	|	3.142857	|
|	Chile	|	Santiago	|	2	|	2	|
|	Colombia	|	Bogota	|	2	|	2	|
|	Denmark	|	Denmark	|	1	|	1	|
|	Egypt	|	Egypt	|	9	|	9	|
|	France	|	France	|	1	|	1	|
|	France	|	Paris	|	1	|	1	|
|	Germany	|	Berlin	|	1	|	1	|
|	Germany	|	Munich	|	1	|	1	|
|	Hong Kong	|	Hong Kong	|	1.5	|	1.5	|
|	India	|	Hyderabad	|	1.5	|	1.2	|
|	India	|	India	|	1	|	1.2	|
|	Indonesia	|	Indonesia	|	1	|	1	|
|	Ireland	|	Dublin	|	2	|	2	|
|	Israel	|	Tel Aviv-Yafo	|	3	|	3	|
|	Japan	|	Japan	|	3.5	|	3.5	|
|	Maldives	|	Maldives	|	2	|	2	|
|	Mexico	|	Mexico	|	1.5	|	1.5	|
|	Netherlands	|	Netherlands	|	2.5	|	2.5	|
|	Romania	|	Romania	|	2	|	2	|
|	Singapore	|	Singapore	|	1	|	1	|
|	South Korea	|	South Korea	|	1	|	1	|
|	Sweden	|	Sweden	|	2.5	|	2.5	|
|	Switzerland	|	Switzerland	|	1	|	2	|
|	Switzerland	|	Zurich	|	2.5	|	2	|
|	Taiwan	|	Taiwan	|	1	|	1	|
|	Thailand	|	Bangkok	|	3	|	2	|
|	Thailand	|	Thailand	|	1	|	2	|
|	United Kingdom	|	London	|	1.3333334	|	1.3333334	|
|	United States	|	Ann Arbor	|	1.5	|	7.3728814	|
|	United States	|	Atlanta	|	1	|	7.3728814	|
|	United States	|	Austin	|	1	|	7.3728814	|
|	United States	|	Chicago	|	12.142858	|	7.3728814	|
|	United States	|	Dallas	|	3	|	7.3728814	|
|	United States	|	Detroit	|	3	|	7.3728814	|
|	United States	|	Houston	|	5	|	7.3728814	|
|	United States	|	Kirkland	|	1.5	|	7.3728814	|
|	United States	|	Mountain View	|	6	|	7.3728814	|
|	United States	|	New York	|	17.272728	|	7.3728814	|
|	United States	|	Palo Alto	|	1.6	|	7.3728814	|
|	United States	|	Pittsburgh	|	12	|	7.3728814	|
|	United States	|	San Bruno	|	2	|	7.3728814	|
|	United States	|	San Francisco	|	5.625	|	7.3728814	|
|	United States	|	San Jose	|	2.5	|	7.3728814	|
|	United States	|	Seattle	|	6.5	|	7.3728814	|
|	United States	|	Sunnyvale	|	18.5	|	7.3728814	|
|	United States	|	United States	|	4.40625	|	7.3728814	|
|	United States	|	Washington	|	1	|	7.3728814	|
|	Vietnam	|	Vietnam	|	2	|	2	|

</details>




## **Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


### SQL Queries:
~~~SQL
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
		ELSE regexp_replace(v2productcategory, '/', '','gi')
	END AS clean_categories,
	count(*) AS sold_per_location
FROM
	all_sessions
WHERE
	transactions = 1
GROUP BY
	1,2,3
ORDER BY
	4 DESC;
~~~


### Answer:
We aren't noticing a huge amount of repeat purchases on any particular categories from different areas, however we can see a definite trend in the nest and apparel categories taking up a lions share of the market.




## **Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?** 

### SQL Queries:
~~~SQL
WITH sell_count AS /** this first cte counts the amount of each item sold per city **/
(
	SELECT 
		p.productname,
		als.country,
		CASE /** to clean up the "not available in free data set" and "not set" options **/
			WHEN als.city LIKE 'not avail%' THEN als.country
			WHEN als.city LIKE '%not se%' THEN als.country
			ELSE als.city
		END AS city,
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
	country, 
	city,
	productname,
	number_sold
FROM 
	ranked_count
WHERE
	sell_rank_city = 1 
ORDER BY
	country,
	city
~~~

### Answer:

<details>

|	country	|	city	|	productname	|	number_sold	|
|	---	|	---	|	---	|	---	|
|	Australia	|	Sydney	|	 Cam Indoor Security Camera - USA	|	1	|
|	Canada	|	New York	|	 Men's  Zip Hoodie	|	1	|
|	Switzerland	|	Zurich	|	 Men's 3/4 Sleeve Henley	|	1	|
|	United States	|	Atlanta	|	 Men's Short Sleeve Hero Tee Heather	|	1	|
|	United States	|	Austin	|	 Men's 100% Cotton Short Sleeve Hero Tee Navy	|	1	|
|	United States	|	Austin	|	 Men's 100% Cotton Short Sleeve Hero Tee Black	|	1	|
|	United States	|	Chicago	|	Rubber Grip Ballpoint Pen 4 Pack	|	1	|
|	United States	|	Chicago	|	 Sunglasses	|	1	|
|	United States	|	Chicago	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|
|	United States	|	Columbus	|	 Men's Short Sleeve Badge Tee Charcoal	|	1	|
|	United States	|	Houston	|	26 oz Double Wall Insulated Bottle	|	1	|
|	United States	|	Los Angeles	|	 Cam Outdoor Security Camera - USA	|	1	|
|	United States	|	Los Angeles	|	 Women's Short Sleeve Hero Tee White	|	1	|
|	United States	|	Mountain View	|	Android Women's Fleece Hoodie	|	1	|
|	United States	|	Mountain View	|	 Mobile Phone Vent Mount	|	1	|
|	United States	|	Mountain View	|	 Men's Vintage Badge Tee Sage	|	1	|
|	United States	|	Mountain View	|	Android Infant Short Sleeve Tee Pewter	|	1	|
|	United States	|	Mountain View	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|
|	United States	|	Mountain View	|	 Men's Bike Short Sleeve Tee Charcoal	|	1	|
|	United States	|	Nashville	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|
|	United States	|	New York	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|
|	United States	|	New York	|	 Cam Outdoor Security Camera - USA	|	1	|
|	United States	|	New York	|	 Men's 100% Cotton Short Sleeve Hero Tee White	|	1	|
|	United States	|	New York	|	 Onesie Red/Graphite	|	1	|
|	United States	|	New York	|	 Men's Short Sleeve Performance Badge Tee Pewter	|	1	|
|	United States	|	New York	|	 Laptop and Cell Phone Stickers	|	1	|
|	United States	|	New York	|	 Luggage Tag	|	1	|
|	United States	|	Palo Alto	|	 Cam Outdoor Security Camera - USA	|	1	|
|	United States	|	Palo Alto	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	1	|
|	United States	|	Palo Alto	|	 Learning Thermostat 3rd Gen-USA - White	|	1	|
|	United States	|	San Bruno	|	 Men's Vintage Badge Tee White	|	1	|
|	United States	|	San Francisco	|	 Protect Smoke + CO White Battery Alarm-USA	|	1	|
|	United States	|	San Francisco	|	 Tri-blend Hoodie Grey	|	1	|
|	United States	|	San Francisco	|	 Women's 3/4 Sleeve Baseball Raglan Heather/Black	|	1	|
|	United States	|	San Francisco	|	 Women's Scoop Neck Tee White	|	1	|
|	United States	|	San Francisco	|	20 oz Stainless Steel Insulated Tumbler	|	1	|
|	United States	|	San Francisco	|	Android 17oz Stainless Steel Sport Bottle	|	1	|
|	United States	|	San Francisco	|	Android Rise 14 oz Mug	|	1	|
|	United States	|	San Francisco	|	Waterproof Backpack	|	1	|
|	United States	|	San Francisco	|	 Cam Outdoor Security Camera - USA	|	1	|
|	United States	|	San Francisco	|	Windup Android	|	1	|
|	United States	|	San Jose	|	 Men's  Zip Hoodie	|	1	|
|	United States	|	San Jose	|	 Cam Outdoor Security Camera - USA	|	1	|
|	United States	|	Seattle	|	 Cam Indoor Security Camera - USA	|	1	|
|	United States	|	Sunnyvale	|	SPF-15 Slim & Slender Lip Balm	|	1	|
|	United States	|	Sunnyvale	|	Android Men's Vintage Henley	|	1	|
|	United States	|	Sunnyvale	|	 Cam Indoor Security Camera - USA	|	1	|
|	United States	|	United States	|	 Protect Smoke + CO White Wired Alarm-USA	|	2	|
|	United States	|	United States	|	 Learning Thermostat 3rd Gen-USA - Stainless Steel	|	2	|

</details>



## **Question 5: Can we summarize the impact of revenue generated from each city/country?**

### SQL Queries:
~~~SQL
SELECT /** This query will get me the total sold in every city **/
	country,
	CASE /** to clean up the "not available in free data set" and "not set" options **/
		WHEN city LIKE 'not avail%' THEN country
		WHEN city LIKE '%not se%' THEN country
		ELSE city
	END AS city,
	SUM(totaltransactionrevenue)/1000000 AS total_per_city /** divide by 1,000,000 to bring to correct values **/
FROM
	all_sessions
WHERE
	transactions = 1 /** makes sure I only include transasctions that have had an actual sale **/
GROUP BY
	country,
	city
ORDER BY
	country,
	city;
~~~
~~~SQL
SELECT /** this query will get me the total sold in every country **/
	country,
	SUM(totaltransactionrevenue)/1000000 AS total_per_country /** divide by 1,000,000 to bring to correct values **/
FROM
	all_sessions
WHERE
	transactions = 1 /** makes sure I only include transasctions that have had an actual sale **/
GROUP BY
	country
ORDER BY
	country
~~~
	
### Answer:

<details>

---Per City ---

|	country	|	city	|	total_per_city	|
|	---	|	---	|	---	|
|	Australia	|	Sydney	|	358	|
|	Canada	|	New York	|	67.99	|
|	Canada	|	Toronto	|	82.16	|
|	Israel	|	Tel Aviv-Yafo	|	602	|
|	Switzerland	|	Zurich	|	16.99	|
|	United States	|	Atlanta	|	854.44	|
|	United States	|	Austin	|	157.78	|
|	United States	|	Chicago	|	449.52	|
|	United States	|	Columbus	|	21.99	|
|	United States	|	Houston	|	38.98	|
|	United States	|	Los Angeles	|	479.48	|
|	United States	|	Mountain View	|	483.36	|
|	United States	|	Nashville	|	157	|
|	United States	|	New York	|	530.36	|
|	United States	|	Palo Alto	|	608	|
|	United States	|	San Bruno	|	103.77	|
|	United States	|	San Francisco	|	1564.32	|
|	United States	|	San Jose	|	262.38	|
|	United States	|	Seattle	|	358	|
|	United States	|	Sunnyvale	|	992.23	|
|	United States	|	United States	|	6092.56	|


--- Per Country --

|	country	|	total_per_country	|
|	---	|	---	|
|	Australia	|	358	|
|	Canada	|	150.15	|
|	Israel	|	602	|
|	Switzerland	|	16.99	|
|	United States	|	13154.17	|

</details>

As you can see the vast majority of sales are occuring inside of the United States








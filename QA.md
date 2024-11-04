### What are your risk areas? Identify and describe them.
Many, but to name a few, we are lacking in primary keys so when tieing tables together it is easy to mix and match things with the wrong information to start. 
columns are not aptly named so there is some potential to be using the wrong one at any point in time.

Does the "transactions" column act as a boolean or does it count something?

I followed up with trying to work out if the information I had uncovered was meaninfgul or if it had underlying bias's. (the vast majority of the traffic to the sight was via USA so there were definite bias towards that being the answer)


## QA Process:
Describe your QA process and include the SQL queries used to execute it.
I needed to double check that the visitid's are unique to different countries (which would make them a single session)
~~~SQL
SELECT /** checking if the productprice can have different amounts on different orders (yes it can) **/
	productsku,
	MAX(productprice),
	AVG(productprice),
	MIN(productprice)
FROM 
	cl_all_sessions
GROUP BY
	productsku
HAVING
	MAX(productprice) != MIN(productprice) -- only show me results that have a different max and minimum value
ORDER BY
	productsku	
~~~

~~~SQL	
SELECT /** see if there are any values in this column other than the 2 (there are not, hence it is acting as a boolean.) **/
	*
FROM 
	all_sessions
WHERE
	transactions IS NOT NULL AND transactions !=1
~~~
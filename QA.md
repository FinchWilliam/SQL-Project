What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.
I needed to double check that the visitid's are unique to different countries (which would make them a single session)

SELECT -- checking if the productprice can have different amounts on different orders (yes it can)
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
	
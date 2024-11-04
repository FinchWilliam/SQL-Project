Question 1: How much does the average item cost in each city/country

SQL Queries:
***SQL
WITH sell_count AS -- this first cte counts the amount of each item sold per city
(
	SELECT 
		p.productname,
		als.country,
		CASE -- to clean up the "not available in free data set" and "not set" options
			WHEN als.city LIKE 'not avail%' THEN als.country
			WHEN als.city LIKE '%not se%' THEN als.country
			ELSE als.city
		END AS city,
		AVG(als.productprice)/1000000 AS avg_price, -- gets the average price of all sold items and adjusts them
		count(*) AS number_sold
	FROM
		all_sessions als
	JOIN
		products p
	ON
		p.sku = als.productsku
	WHERE 
		als.transactions IS NOT NULL -- products that were actually purchased
	GROUP BY
		p.productname,
		als.city,
		als.country
),
ranked_count AS -- this cte will rank the items sold in each city
(
	SELECT
		*,
		DENSE_RANK() OVER (PARTITION BY city ORDER BY number_sold DESC) AS sell_rank_city
	FROM
		sell_count
)
SELECT -- finally we just select the top ranking items from each city
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
***
Answer: 
country	city	productname	number_sold	price_per_city	avg
(not set)	(not set)	 Men's Long Sleeve Raglan Ocean Blue	2	24.99	29.99
(not set)	(not set)	 Men's Long Sleeve Pullover Badge Tee Heather	2	34.99	29.99
Albania	Albania	 Women's Lightweight Microfleece Jacket	1	74.99	33.65666667
Albania	Albania	22 oz  Bottle Infuser	1	4.99	33.65666667
Albania	Albania	 Men's Vintage Tank	1	20.99	33.65666667
Algeria	Algeria	 Twill Cap	1	10.99	17.49
Algeria	Algeria	 Toddler Raglan Shirt Blue Heather/Navy	1	23.99	17.49
Argentina	Argentina	 Wool Heather Cap Heather/Black	3	24.99	27.541
Argentina	Buenos Aires	 Men's Long & Lean Tee Charcoal	1	19.99	27.541
Argentina	Buenos Aires	Android Men's  Zip Hoodie	1	55.99	27.541
Argentina	Buenos Aires	Rocket Flashlight	1	4.99	27.541
Argentina	Buenos Aires	Sport Bag	1	4.99	27.541
Argentina	Buenos Aires	 Alpine Style Backpack	1	99.99	27.541
Argentina	Buenos Aires	24 oz  Sergeant Stripe Bottle	1	7.99	27.541
Argentina	Rosario	 Men's Vintage Badge Tee Black	1	18.99	27.541
Argentina	Santa Fe	SPF-15 Slim & Slender Lip Balm	1	2.5	27.541
Argentina	Santa Fe	 Water Resistant Bluetooth Speaker	1	34.99	27.541
Armenia	Armenia	Windup Android	1	3.99	3.99
Australia	Adelaide	 Men's Watershed Full Zip Hoodie Grey	1	109.99	29.56947368
Australia	Australia	 Twill Cap	6	10.99	29.56947368
Australia	Brisbane	 Men's Vintage Henley	1	29.99	29.56947368
Australia	Brisbane	 Wool Heather Cap Heather/Black	1	24.99	29.56947368
Australia	Brisbane	 Youth Girl Hoodie	1	43.99	29.56947368
Australia	Brisbane	 Men's Microfiber 1/4 Zip Pullover Blue/Indigo	1	0	29.56947368
Australia	Brisbane	 Twill Cap	1	10.99	29.56947368
Australia	Brisbane	 Women's Short Sleeve Hero Tee Red Heather	1	18.99	29.56947368
Australia	Brisbane	 Men's Softshell Jacket Black/Grey	1	98.99	29.56947368
Australia	Brisbane	24 oz  Sergeant Stripe Bottle	1	7.99	29.56947368
Australia	Brisbane	 Leatherette Notebook Combo	1	6.99	29.56947368
Australia	Brisbane	 High Capacity 10,400mAh Charger	1	58.99	29.56947368
Australia	Melbourne	 Laptop and Cell Phone Stickers	3	1.99	29.56947368
Australia	Perth	 Leatherette Notebook Combo	1	6.99	29.56947368
Australia	Perth	 Rucksack	1	69.99	29.56947368
Australia	Perth	 Women's Short Sleeve Hero Tee Red Heather	1	18.99	29.56947368
Australia	Perth	 Custom Decals	1	1.99	29.56947368
Australia	Perth	Women's  Short Sleeve Hero Tee Black	1	16.99	29.56947368
Australia	Sydney	 Trucker Hat	5	21.99	29.56947368
Austria	Austria	 RFID Journal	2	19.99	25.115
Austria	Austria	 Men's Short Sleeve Hero Tee White	2	16.99	25.115
Austria	Austria	 Onesie Heather	2	23.99	25.115
Austria	Austria	 Wool Heather Cap Heather/Black	2	24.99	25.115
Austria	Vienna	 Men's  Zip Hoodie	1	55.99	25.115
Austria	Vienna	Android Wool Heather Cap Heather/Black	1	24.99	25.115
Austria	Vienna	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	25.115
Austria	Vienna	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	25.115
Bahamas	Bahamas	 Men's Vintage Henley	1	29.99	20.49
Bahamas	Bahamas	 Twill Cap	1	10.99	20.49
Bahrain	Bahrain	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	19.65666667
Bahrain	Bahrain	 Men's 3/4 Sleeve Henley	1	24.99	19.65666667
Bahrain	Bahrain	 Men's Short Sleeve Hero Tee Black	1	16.99	19.65666667
Bangladesh	Bangladesh	 Men's Vintage Badge Tee White	2	17.99	17.99
Barbados	Barbados	 Men's Pullover Hoodie Grey	1	51.99	51.99
Belarus	Belarus	 Men's Long Sleeve Pullover Badge Tee Heather	1	34.99	39.292
Belarus	Belarus	 Stylus Pen w/ LED Light	1	5.5	39.292
Belarus	Belarus	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	39.292
Belarus	Belarus	Android Men's Short Sleeve Hero Tee Heather	1	16.99	39.292
Belarus	Belarus	 Women's Performance Full Zip Jacket Black	1	119.99	39.292
Belgium	Antwerp	Colored Pencil Set	1	2.99	12.03809524
Belgium	Antwerp	 Insulated Stainless Steel Bottle	1	19.99	12.03809524
Belgium	Belgium	 Men's 100% Cotton Short Sleeve Hero Tee White	3	11.32666667	12.03809524
Belgium	Belgium	 Leatherette Notebook Combo	3	6.99	12.03809524
Belgium	Belgium	 Trucker Hat	3	21.99	12.03809524
Belgium	Brussels	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	12.03809524
Belgium	Ghent	Windup Android	1	3.99	12.03809524
Bolivia	Bolivia	Android Men's Short Sleeve Hero Tee Heather	1	16.99	59.99
Bolivia	Bolivia	 G Noise-reducing Bluetooth Headphones	1	145.99	59.99
Bolivia	Bolivia	 Men's Short Sleeve Hero Tee Black	1	16.99	59.99
Bosnia & Herzegovina	Bosnia & Herzegovina	24 oz  Sergeant Stripe Bottle	1	7.99	10.65666667
Bosnia & Herzegovina	Bosnia & Herzegovina	 Toddler Short Sleeve Tee Red	1	16.99	10.65666667
Bosnia & Herzegovina	Bosnia & Herzegovina	 Leatherette Notebook Combo	1	6.99	10.65666667
Botswana	Botswana	Waterproof Backpack	1	99.99	99.99
Brazil	Belo Horizonte	Collapsible Shopping Bag	1	4.99	21.5852381
Brazil	Brazil	 Trucker Hat	6	23.15666667	21.5852381
Brazil	Fortaleza	Android Luggage Tag	1	8.99	21.5852381
Brazil	Rio de Janeiro	 Blackout Cap	1	18.99	21.5852381
Brazil	Rio de Janeiro	 Men's  Zip Hoodie	1	55.99	21.5852381
Brazil	Rio de Janeiro	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	21.5852381
Brazil	Sao Paulo	Suitcase Organizer Cubes	2	21.99	21.5852381
Brunei	Brunei	 Toddler Short Sleeve Tee Red	1	16.99	23.49
Brunei	Brunei	 Men's Vintage Henley	1	29.99	23.49
Bulgaria	Bulgaria	 Toddler Short Sleeve T-shirt Royal Blue	1	16.99	11.89909091
Bulgaria	Bulgaria	 Men's Vintage Tank	1	20.99	11.89909091
Bulgaria	Bulgaria	22 oz  Bottle Infuser	1	4.99	11.89909091
Bulgaria	Bulgaria	20 oz Stainless Steel Insulated Tumbler	1	24.99	11.89909091
Bulgaria	Bulgaria	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	11.89909091
Bulgaria	Bulgaria	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	11.89909091
Bulgaria	Bulgaria	 Custom Decals	1	1.99	11.89909091
Bulgaria	Bulgaria	 Twill Cap	1	10.99	11.89909091
Bulgaria	Bulgaria	Rocket Flashlight	1	4.99	11.89909091
Bulgaria	Bulgaria	Spiral Notebook and Pen Set	1	5.99	11.89909091
Bulgaria	Bulgaria	Four Color Retractable Pen	1	2.99	11.89909091
Cambodia	Cambodia	 Twill Cap	1	10.99	20.79
Cambodia	Cambodia	Women's  Short Sleeve Hero Tee Black	1	16.99	20.79
Cambodia	Cambodia	 Lunch Bag	1	13.99	20.79
Cambodia	Cambodia	Micro Wireless Earbud	1	39.99	20.79
Cambodia	Cambodia	 Men's Short Sleeve Performance Badge Tee Navy	1	21.99	20.79
Canada	Burnaby	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	27.1355814
Canada	Burnaby	Android Men's  Zip Hoodie	1	55.99	27.1355814
Canada	Burnaby	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	27.1355814
Canada	Calgary	Android Toddler Short Sleeve T-shirt Pink	1	16.99	27.1355814
Canada	Calgary	 Luggage Tag	1	8.99	27.1355814
Canada	Calgary	Android Wool Heather Cap Heather/Black	1	24.99	27.1355814
Canada	Calgary	Recycled Mouse Pad	1	1.5	27.1355814
Canada	Calgary	 Leatherette Notebook Combo	1	6.99	27.1355814
Canada	Canada	 Twill Cap	14	10.99	27.1355814
Canada	Edmonton	 Collapsible Pet Bowl	1	4.99	27.1355814
Canada	Edmonton	 Stylus Pen w/ LED Light	1	5.5	27.1355814
Canada	Kitchener	Recycled Mouse Pad	1	1.5	27.1355814
Canada	Kitchener	Android Men's Long Sleeve Badge Crew Tee Heather	1	34.99	27.1355814
Canada	Kitchener	 Women's Lightweight Microfleece Jacket	1	74.99	27.1355814
Canada	Kitchener	 5-Panel Cap	1	15.19	27.1355814
Canada	Kitchener	Keyboard DOT Sticker	1	1.5	27.1355814
Canada	Mississauga	 Custom Decals	1	1.99	27.1355814
Canada	Mississauga	Collapsible Shopping Bag	1	4.99	27.1355814
Canada	Mississauga	 Toddler Hoodie Royal Blue	1	37.99	27.1355814
Canada	Mississauga	 Tube Power Bank	1	16.99	27.1355814
Canada	Montreal	 RFID Journal	2	19.99	27.1355814
Canada	Montreal	 Alpine Style Backpack	2	99.99	27.1355814
Canada	Montreal	 Men's 100% Cotton Short Sleeve Hero Tee Navy	2	16.99	27.1355814
Canada	Ottawa	 Men's Short Sleeve Hero Tee Heather	1	18.99	27.1355814
Canada	Ottawa	 Men's Short Sleeve Hero Tee White	1	16.99	27.1355814
Canada	Quebec City	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	27.1355814
Canada	Quebec City	 Men's Short Sleeve Hero Tee Black	1	16.99	27.1355814
Canada	Quebec City	 Men's Lightweight Microfleece Jacket Black	1	74.99	27.1355814
Canada	Quebec City	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	27.1355814
Canada	Quebec City	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	27.1355814
Canada	Quebec City	 Custom Decals	1	1.99	27.1355814
Canada	Quebec City	 Men's Short Sleeve Hero Tee Heather	1	18.99	27.1355814
Canada	Sherbrooke	 Cam Indoor Security Camera - USA	1	199	27.1355814
Canada	Sherbrooke	 Men's Long Sleeve Raglan Ocean Blue	1	0	27.1355814
Canada	Sherbrooke	 Blackout Cap	1	18.99	27.1355814
Canada	St. John's	 2200mAh Micro Charger	1	22.99	27.1355814
Canada	Toronto	 Trucker Hat	5	21.99	27.1355814
Canada	Vancouver	 Laptop Backpack	1	99.99	27.1355814
Canada	Vancouver	Micro Wireless Earbud	1	39.99	27.1355814
Canada	Vancouver	 Device Stand	1	4.99	27.1355814
Canada	Vancouver	 Men's Short Sleeve Hero Tee Black	1	16.99	27.1355814
Canada	Vancouver	 Hard Cover Journal	1	14.99	27.1355814
Canada	Waterloo	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	27.1355814
Chile	Chile	24 oz  Sergeant Stripe Bottle	2	7.99	13.65666667
Chile	Santiago	 Kick Ball	1	1.99	13.65666667
Chile	Santiago	 Men's 3/4 Sleeve Henley	1	24.99	13.65666667
Chile	Santiago	1 oz Hand Sanitizer	1	1.99	13.65666667
Chile	Santiago	Micro Wireless Earbud	1	39.99	13.65666667
Chile	Santiago	Sport Bag	1	4.99	13.65666667
China	Beijing	 Men's Airflow 1/4 Zip Pullover Lapis	1	69.99	44.49
China	China	 Men's Vintage Badge Tee White	2	18.99	44.49
Colombia	Bogota	 Hard Cover Journal	1	14.99	45.49136364
Colombia	Bogota	 Men's Airflow 1/4 Zip Pullover Black	1	69.99	45.49136364
Colombia	Bogota	 Doodle Decal	1	2.99	45.49136364
Colombia	Bogota	 Men's Pullover Hoodie Grey	1	51.99	45.49136364
Colombia	Bogota	 Toddler Short Sleeve Tee Red	1	16.99	45.49136364
Colombia	Bogota	 Men's Microfiber 1/4 Zip Pullover Blue/Indigo	1	74.99	45.49136364
Colombia	Bogota	 Lunch Bag	1	13.99	45.49136364
Colombia	Bogota	Android Men's Vintage Henley	1	29.99	45.49136364
Colombia	Bogota	 Men's 3/4 Sleeve Henley	1	24.99	45.49136364
Colombia	Bogota	Micro Wireless Earbud	1	39.99	45.49136364
Colombia	Bogota	 Snapback Hat Black	1	22.99	45.49136364
Colombia	Bogota	 Laptop Backpack	1	99.99	45.49136364
Colombia	Bogota	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	45.49136364
Colombia	Bogota	 Flashlight	1	59.99	45.49136364
Colombia	Bogota	 Men's Short Sleeve Hero Tee White	1	16.99	45.49136364
Colombia	Bogota	 Women's Fleece Hoodie	1	55.99	45.49136364
Colombia	Bogota	 Men's Short Sleeve Hero Tee Black	1	16.99	45.49136364
Colombia	Bogota	SPF-15 Slim & Slender Lip Balm	1	2.5	45.49136364
Colombia	Bogota	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	249	45.49136364
Colombia	Colombia	22 oz  Bottle Infuser	4	4.99	45.49136364
Colombia	Medellin	Bottle Opener Clip	1	3.5	45.49136364
Colombia	Medellin	 Men's Watershed Full Zip Hoodie Grey	1	109.99	45.49136364
Costa Rica	Costa Rica	Pen Pencil & Highlighter Set	1	3.99	21.79
Costa Rica	Costa Rica	 Power Bank	1	55.99	21.79
Costa Rica	Costa Rica	Plastic Sliding Flashlight	1	12.99	21.79
Costa Rica	Costa Rica	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	21.79
Costa Rica	Costa Rica	 Women's Short Sleeve Hero Tee White	1	16.99	21.79
CÃ´te dâ€™Ivoire	CÃ´te dâ€™Ivoire	 Custom Decals	1	1.99	9.823333333
CÃ´te dâ€™Ivoire	CÃ´te dâ€™Ivoire	 Men's Vintage Tank	1	20.99	9.823333333
CÃ´te dâ€™Ivoire	CÃ´te dâ€™Ivoire	 Custom Decals	1	1.99	9.823333333
CÃ´te dâ€™Ivoire	CÃ´te dâ€™Ivoire	 Men's Vintage Henley	1	29.99	9.823333333
CÃ´te dâ€™Ivoire	CÃ´te dâ€™Ivoire	 Custom Decals	1	1.99	9.823333333
CÃ´te dâ€™Ivoire	CÃ´te dâ€™Ivoire	 Custom Decals	1	1.99	9.823333333
Croatia	Croatia	24 oz  Sergeant Stripe Bottle	2	7.99	49.32333333
Croatia	Croatia	 Men's Vintage Henley	2	29.99	49.32333333
Croatia	Zagreb	 Men's Watershed Full Zip Hoodie Grey	2	109.99	49.32333333
Cyprus	Cyprus	Suitcase Organizer Cubes	1	21.99	33.99
Cyprus	Cyprus	 Wool Heather Cap Heather/Black	1	24.99	33.99
Cyprus	Cyprus	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	33.99
Cyprus	Cyprus	 Men's Airflow 1/4 Zip Pullover Lapis	1	69.99	33.99
Czechia	Brno	Leatherette Journal	1	10.99	23.74
Czechia	Brno	 Men's Pullover Hoodie Grey	1	51.99	23.74
Czechia	Czechia	 Hard Cover Journal	6	14.99	23.74
Czechia	Prague	 Men's 100% Cotton Short Sleeve Hero Tee White	2	16.99	23.74
Denmark	Copenhagen	 Trucker Hat	1	21.99	13.99
Denmark	Copenhagen	Rocket Flashlight	1	4.99	13.99
Denmark	Denmark	 Hard Cover Journal	5	14.99	13.99
Dominican Republic	Dominican Republic	 Men's Vintage Tank	1	20.99	17.03636364
Dominican Republic	Dominican Republic	 Men's Short Sleeve Hero Tee Black	1	16.99	17.03636364
Dominican Republic	Dominican Republic	 Women's Lightweight Microfleece Jacket	1	74.99	17.03636364
Dominican Republic	Dominican Republic	 Twill Cap	1	10.99	17.03636364
Dominican Republic	Dominican Republic	22 oz  Bottle Infuser	1	4.99	17.03636364
Dominican Republic	Dominican Republic	Bottle Opener Clip	1	3.5	17.03636364
Dominican Republic	Dominican Republic	Electronics Accessory Pouch	1	4.99	17.03636364
Dominican Republic	Dominican Republic	 Custom Decals	1	1.99	17.03636364
Dominican Republic	Dominican Republic	 Leatherette Notebook Combo	1	6.99	17.03636364
Dominican Republic	Dominican Republic	 Trucker Hat	1	21.99	17.03636364
Dominican Republic	Dominican Republic	 Men's Vintage Badge Tee Black	1	18.99	17.03636364
Ecuador	Ecuador	 Onesie Heather	1	23.99	35.89909091
Ecuador	Ecuador	 Tube Power Bank	1	16.99	35.89909091
Ecuador	Ecuador	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	35.89909091
Ecuador	Ecuador	 Men's Watershed Full Zip Hoodie Grey	1	109.99	35.89909091
Ecuador	Ecuador	Android Men's  Zip Hoodie	1	55.99	35.89909091
Ecuador	Ecuador	 Men's Vintage Tee	1	18.99	35.89909091
Ecuador	Ecuador	 Men's Short Sleeve Badge Tee Charcoal	1	18.99	35.89909091
Ecuador	Ecuador	 Men's Long & Lean Tee Grey	1	19.99	35.89909091
Ecuador	Ecuador	Android Men's Long Sleeve Badge Crew Tee Heather	1	34.99	35.89909091
Ecuador	Ecuador	 RFID Journal	1	19.99	35.89909091
Ecuador	Ecuador	 Men's  Zip Hoodie	1	55.99	35.89909091
Egypt	Egypt	 Leatherette Notebook Combo	1	6.99	17.80818182
Egypt	Egypt	 Men's Vintage Badge Tee Black	1	18.99	17.80818182
Egypt	Egypt	Android Men's Short Sleeve Hero Tee White	1	16.99	17.80818182
Egypt	Egypt	22 oz  Bottle Infuser	1	4.99	17.80818182
Egypt	Egypt	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	17.80818182
Egypt	Egypt	 Toddler Short Sleeve Tee Red	1	16.99	17.80818182
Egypt	Egypt	 Men's Long & Lean Tee Charcoal	1	19.99	17.80818182
Egypt	Egypt	 Men's Vintage Henley	1	29.99	17.80818182
Egypt	Egypt	Android RFID Journal	1	19.99	17.80818182
Egypt	Egypt	 Men's Vintage Badge Tee Sage	1	18.99	17.80818182
Egypt	Egypt	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	17.80818182
El Salvador	El Salvador	Android Men's  Zip Hoodie	1	55.99	30.99
El Salvador	El Salvador	 Wool Heather Cap Heather/Navy	1	24.99	30.99
El Salvador	El Salvador	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	30.99
El Salvador	El Salvador	 Men's Quilted Insulated Vest Battleship Grey	1	74.99	30.99
El Salvador	El Salvador	 Women's Vintage Hero Tee Lavender	1	16.99	30.99
El Salvador	El Salvador	 Trucker Hat	1	21.99	30.99
El Salvador	San Salvador	 Device Holder Sticky Pad	1	4.99	30.99
Estonia	Estonia	 Insulated Stainless Steel Bottle	1	19.99	20.491
Estonia	Estonia	 Mood Original Window Decal	1	1.99	20.491
Estonia	Estonia	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	0	20.491
Estonia	Estonia	 Car Clip Phone Holder	1	6.99	20.491
Estonia	Estonia	22 oz Android Bottle	1	2.99	20.491
Estonia	Estonia	 Men's Quilted Insulated Vest Black	1	74.99	20.491
Estonia	Estonia	Electronics Accessory Pouch	1	4.99	20.491
Estonia	Estonia	 Men's Short Sleeve Hero Tee Black	1	16.99	20.491
Estonia	Estonia	Android Men's  Zip Hoodie	1	55.99	20.491
Estonia	Estonia	 Zipper-front Sports Bag	1	19.99	20.491
Ethiopia	Ethiopia	 Leatherette Notebook Combo	1	6.99	10.99
Ethiopia	Ethiopia	 Hard Cover Journal	1	14.99	10.99
Finland	Finland	 Infant Zip Hood Royal Blue	1	37.99	12.99230769
Finland	Finland	Recycled Mouse Pad	1	1.5	12.99230769
Finland	Finland	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	12.99230769
Finland	Finland	Keyboard DOT Sticker	1	1.5	12.99230769
Finland	Finland	 Vintage Henley Grey/Black	1	29.99	12.99230769
Finland	Finland	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	0	12.99230769
Finland	Finland	 Twill Cap	1	10.99	12.99230769
Finland	Finland	 Laptop and Cell Phone Stickers	1	1.99	12.99230769
Finland	Finland	 Tube Power Bank	1	16.99	12.99230769
Finland	Finland	 Men's Short Sleeve Hero Tee White	1	16.99	12.99230769
Finland	Finland	Rocket Flashlight	1	4.99	12.99230769
Finland	Finland	 Device Holder Sticky Pad	1	4.99	12.99230769
Finland	Helsinki	Android Onesie Gold	1	23.99	12.99230769
France	Bordeaux	 Luggage Tag	1	14.99	16.89
France	Courbevoie	Android Wool Heather Cap Heather/Black	1	24.99	16.89
France	Courbevoie	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	16.89
France	France	 Men's 100% Cotton Short Sleeve Hero Tee White	6	16.99	16.89
France	Marseille	 Men's 3/4 Sleeve Henley	1	24.99	16.89
France	Marseille	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	16.89
France	Montreuil	Colored Pencil Set	1	2.99	16.89
France	Montreuil	Collapsible Shopping Bag	1	4.99	16.89
France	Paris	 Trucker Hat	3	21.99	16.89
France	Villeneuve-d'Ascq	 Snapback Hat Black	1	22.99	16.89
Georgia	Georgia	 Custom Decals	3	1.99	1.99
Germany	Berlin	 Wool Heather Cap Heather/Black	3	24.99	17.22926136
Germany	Frankfurt	 Bib White	1	11.99	17.22926136
Germany	Germany	 Trucker Hat	11	22.80818182	17.22926136
Germany	Hamburg	Android BTTF Moonshot Graphic Tee	1	19.99	17.22926136
Germany	Hamburg	Basecamp Explorer Powerbank Flashlight	1	22.99	17.22926136
Germany	Hamburg	Android Rise 14 oz Mug	1	12.99	17.22926136
Germany	Hamburg	 Water Resistant Bluetooth Speaker	1	34.99	17.22926136
Germany	Hamburg	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	17.22926136
Germany	Hamburg	Suitcase Organizer Cubes	1	21.99	17.22926136
Germany	Hamburg	 Blackout Cap	1	18.99	17.22926136
Germany	Hamburg	22 oz Android Bottle	1	2.99	17.22926136
Germany	Hamburg	 Women's Performance Hero Tee Gunmetal	1	0	17.22926136
Germany	Hamburg	 RFID Journal	1	19.99	17.22926136
Germany	Hamburg	22 oz  Bottle Infuser	1	4.99	17.22926136
Germany	Munich	 Twill Cap	3	10.99	17.22926136
Germany	Stuttgart	 Pocket Bluetooth Speaker	1	27.99	17.22926136
Ghana	Ghana	 Men's Vintage Tank	1	20.99	15.65666667
Ghana	Ghana	 Luggage Tag	1	8.99	15.65666667
Ghana	Ghana	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	15.65666667
Gibraltar	Gibraltar	 Men's Vintage Henley	1	29.99	29.99
Greece	Athens	Android Men's Short Sleeve Hero Tee Heather	1	16.99	11.24
Greece	Athens	 Hard Cover Journal	1	14.99	11.24
Greece	Athens	Sport Bag	1	4.99	11.24
Greece	Athens	Electronics Accessory Pouch	1	4.99	11.24
Greece	Athens	22 oz  Bottle Infuser	1	4.99	11.24
Greece	Greece	 Men's Vintage Tank	3	20.99	11.24
Greece	Thessaloniki	Red Spiral  Notebook	1	9.99	11.24
Greece	Thessaloniki	 Bib Red	1	11.99	11.24
Guatemala	Guatemala	 Twill Cap	2	10.99	10.99
Haiti	Haiti	 Men's Short Sleeve Hero Tee Heather	1	18.99	18.99
Honduras	Honduras	 Cam Outdoor Security Camera - USA	1	199	76.7425
Honduras	Honduras	 Wool Heather Cap Heather/Black	1	24.99	76.7425
Honduras	Honduras	24 oz  Sergeant Stripe Bottle	1	7.99	76.7425
Honduras	Honduras	 Women's Lightweight Microfleece Jacket	1	74.99	76.7425
Hong Kong	Hong Kong	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	10.3675
Hong Kong	Hong Kong	 Screen Cleaning Cloth	2	1.99	10.3675
Hong Kong	Hong Kong	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	10.3675
Hong Kong	Hong Kong	Recycled Mouse Pad	2	1.5	10.3675
Hungary	Budapest	Recycled Mouse Pad	1	1.5	27.17875
Hungary	Budapest	Android Men's  Zip Hoodie	1	55.99	27.17875
Hungary	Budapest	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	27.17875
Hungary	Budapest	 Men's Short Sleeve Performance Badge Tee Pewter	1	21.99	27.17875
Hungary	Budapest	 Wool Heather Cap Heather/Black	1	24.99	27.17875
Hungary	Budapest	 Men's Pullover Hoodie Grey	1	51.99	27.17875
Hungary	Hungary	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	27.17875
Hungary	Hungary	 Men's Short Sleeve Hero Tee Black	2	16.99	27.17875
Iceland	Iceland	 Insulated Stainless Steel Bottle	1	19.99	19.99
India	Ahmedabad	 Trucker Hat	2	21.99	18.72333333
India	Bengaluru	Android Men's Short Sleeve Hero Tee White	2	16.99	18.72333333
India	Bengaluru	 Leatherette Notebook Combo	2	6.99	18.72333333
India	Bengaluru	 Men's 100% Cotton Short Sleeve Hero Tee Navy	2	16.99	18.72333333
India	Bengaluru	 Men's 100% Cotton Short Sleeve Hero Tee Red	2	16.99	18.72333333
India	Bengaluru	 Men's Vintage Henley	2	29.99	18.72333333
India	Bengaluru	Women's  Short Sleeve Hero Tee Black	2	16.99	18.72333333
India	Bengaluru	 Men's Short Sleeve Hero Tee Black	2	16.99	18.72333333
India	Bengaluru	 Laptop and Cell Phone Stickers	2	1.99	18.72333333
India	Bengaluru	 Onesie Heather	2	23.99	18.72333333
India	Bhubaneswar	20 oz Stainless Steel Insulated Tumbler	1	24.99	18.72333333
India	Chandigarh	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	18.72333333
India	Chennai	 RFID Journal	5	19.99	18.72333333
India	Chennai	22 oz  Bottle Infuser	5	4.99	18.72333333
India	Gurgaon	Android Men's Vintage Tank	2	20.99	18.72333333
India	Hyderabad	 Men's Vintage Badge Tee Black	5	18.19	18.72333333
India	Hyderabad	 Men's 100% Cotton Short Sleeve Hero Tee White	5	16.99	18.72333333
India	India	 Men's Vintage Tank	9	20.99	18.72333333
India	India	 Men's 100% Cotton Short Sleeve Hero Tee Red	9	16.99	18.72333333
India	India	 Men's Vintage Tank	9	20.99	18.72333333
India	India	 Custom Decals	9	1.99	18.72333333
India	India	 Custom Decals	9	1.99	18.72333333
India	Indore	 Leatherette Notebook Combo	1	6.99	18.72333333
India	Indore	 Trucker Hat	1	21.99	18.72333333
India	Indore	 Men's Short Sleeve Hero Tee White	1	16.99	18.72333333
India	Indore	 Twill Cap	1	10.99	18.72333333
India	Indore	 Hard Cover Journal	1	14.99	18.72333333
India	Jaipur	 5-Panel Snapback Cap	1	18.99	18.72333333
India	Jaipur	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	18.72333333
India	Kharagpur	 Men's Performance Full Zip Jacket Black	1	119.99	18.72333333
India	Kharagpur	 Tri-blend Hoodie Grey	1	39.99	18.72333333
India	Kolkata	24 oz  Sergeant Stripe Bottle	2	7.99	18.72333333
India	Kolkata	 Custom Decals	2	1.99	18.72333333
India	Kolkata	 Luggage Tag	2	8.99	18.72333333
India	Lucknow	 Men's Vintage Tank	1	20.99	18.72333333
India	Lucknow	Android Men's Short Sleeve Tri-blend Hero Tee Grey	1	18.99	18.72333333
India	Mumbai	 Men's Vintage Tank	4	20.99	18.72333333
India	Nanded	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	18.72333333
India	New Delhi	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	18.72333333
India	New Delhi	 Men's Long Sleeve Raglan Ocean Blue	2	24.99	18.72333333
India	New Delhi	 Men's Vintage Tee	2	17.99	18.72333333
India	New Delhi	 Men's Short Sleeve Hero Tee Heather	2	18.99	18.72333333
India	New Delhi	 Custom Decals	2	1.99	18.72333333
India	New Delhi	 Snapback Hat Black	2	22.99	18.72333333
India	Patna	 Vintage Henley Grey/Black	1	29.99	18.72333333
India	Patna	22 oz Android Bottle	1	2.99	18.72333333
India	Pune	 Men's 100% Cotton Short Sleeve Hero Tee White	2	16.99	18.72333333
India	Pune	 Women's 3/4 Sleeve Baseball Raglan Heather/Black	2	19.99	18.72333333
Indonesia	Bandung	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	9.192
Indonesia	Indonesia	22 oz  Bottle Infuser	4	4.99	9.192
Indonesia	Jakarta	 Men's Short Sleeve Hero Tee White	2	16.99	9.192
Indonesia	Jakarta	22 oz  Bottle Infuser	2	4.99	9.192
Indonesia	Jakarta	 Women's 1/4 Zip Jacket Charcoal	2	0	9.192
Iraq	Iraq	Android 5-Panel Low Cap	1	18.99	31.32333333
Iraq	Iraq	Android Men's  Zip Hoodie	1	55.99	31.32333333
Iraq	Iraq	 Men's Vintage Badge Tee Black	1	18.99	31.32333333
Ireland	Cork	 Custom Decals	1	1.99	15.09
Ireland	Dublin	 Toddler Short Sleeve Tee Red	3	16.99	15.09
Ireland	Ireland	 Trucker Hat	2	25.49	15.09
Ireland	Ireland	 Snapback Hat Black	2	22.99	15.09
Ireland	Ireland	24 oz  Sergeant Stripe Bottle	2	7.99	15.09
Israel	Israel	 RFID Journal	1	19.99	22.79
Israel	Israel	22 oz Android Bottle	1	3.99	22.79
Israel	Israel	Android Infant Short Sleeve Tee Pewter	1	16.99	22.79
Israel	Israel	 Leatherette Notebook Combo	1	6.99	22.79
Israel	Israel	 G Noise-reducing Bluetooth Headphones	1	145.99	22.79
Israel	Israel	 Men's Vintage Tank	1	20.99	22.79
Israel	Israel	 Custom Decals	1	1.99	22.79
Israel	Israel	22 oz  Bottle Infuser	1	4.99	22.79
Israel	Israel	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	22.79
Israel	Israel	Basecamp Explorer Powerbank Flashlight	1	22.99	22.79
Israel	Israel	 Canvas Tote Natural/Navy	1	15.99	22.79
Israel	Israel	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	22.79
Israel	Tel Aviv-Yafo	 Hard Cover Journal	2	14.99	22.79
Israel	Tel Aviv-Yafo	22 oz  Bottle Infuser	2	4.99	22.79
Israel	Tel Aviv-Yafo	Android 17oz Stainless Steel Sport Bottle	2	18.99	22.79
Italy	Italy	 Men's 100% Cotton Short Sleeve Hero Tee White	5	16.99	11.63428571
Italy	Milan	22 oz  Bottle Infuser	2	4.99	11.63428571
Italy	Rome	Leatherette Journal	1	10.99	11.63428571
Italy	Rome	 RFID Journal	1	19.99	11.63428571
Italy	Rome	 Leatherette Notebook Combo	1	6.99	11.63428571
Italy	Rome	 Women's 3/4 Sleeve Baseball Raglan Heather/Black	1	19.99	11.63428571
Italy	Rome	Keyboard DOT Sticker	1	1.5	11.63428571
Jamaica	Jamaica	 Men's  Zip Hoodie	1	55.99	55.99
Japan	Chiyoda	24 oz  Sergeant Stripe Bottle	1	7.99	24.58181818
Japan	Chuo	 Women's Scoop Neck Tee Black	1	19.99	24.58181818
Japan	Japan	 Men's 100% Cotton Short Sleeve Hero Tee White	5	16.99	24.58181818
Japan	Japan	 Men's Vintage Tank	5	20.99	24.58181818
Japan	Minato	Windup Android	2	3.99	24.58181818
Japan	Minato	 Men's Vintage Tank	2	20.99	24.58181818
Japan	Minato	Women's  Short Sleeve Hero Tee Black	2	16.99	24.58181818
Japan	Minato	 Men's 100% Cotton Short Sleeve Hero Tee White	2	16.99	24.58181818
Japan	Minato	 Leatherette Notebook Combo	2	6.99	24.58181818
Japan	Nagoya	 Women's Convertible Vest-Jacket Sea Foam Green	1	98.99	24.58181818
Japan	Osaka	 Rucksack	1	69.99	24.58181818
Japan	Osaka	 Dress Socks	1	8.99	24.58181818
Japan	Osaka	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	24.58181818
Japan	Osaka	 Protect Smoke + CO White Wired Alarm-USA	1	119	24.58181818
Japan	Osaka	 Infant Short Sleeve Tee Red	1	16.99	24.58181818
Japan	Osaka	 Stylus Pen w/ LED Light	1	5.5	24.58181818
Japan	Sakai	 Leatherette Notebook Combo	1	6.99	24.58181818
Japan	Sapporo	Android Toddler Short Sleeve T-shirt Pink	1	16.99	24.58181818
Japan	Shibuya	 Men's Vintage Badge Tee Sage	1	18.99	24.58181818
Japan	Shibuya	 Men's Short Sleeve Hero Tee White	1	16.99	24.58181818
Japan	Shinjuku	Windup Android	1	3.99	24.58181818
Japan	Shinjuku	 Trucker Hat	1	28.99	24.58181818
Japan	Shinjuku	 17oz Stainless Steel Sport Bottle	1	18.99	24.58181818
Japan	Shinjuku	 Men's Vintage Tee	1	16.99	24.58181818
Japan	Shinjuku	Plastic Sliding Flashlight	1	12.99	24.58181818
Japan	Shinjuku	Colored Pencil Set	1	2.99	24.58181818
Japan	Shinjuku	 Women's Scoop Neck Tee White	1	19.99	24.58181818
Japan	Yokohama	 Slim Utility Travel Bag	1	14.99	24.58181818
Japan	Yokohama	 Men's Pullover Hoodie Grey	1	51.99	24.58181818
Japan	Yokohama	 Rucksack	1	69.99	24.58181818
Japan	Yokohama	 Men's  Zip Hoodie	1	0	24.58181818
Japan	Yokohama	Android Men's Engineer Short Sleeve Tee Charcoal	1	19.99	24.58181818
Japan	Yokohama	 Men's Short Sleeve Hero Tee Heather	1	18.99	24.58181818
Jersey	Jersey	 Tri-blend Hoodie Grey	1	39.99	39.99
Jordan	Jordan	Suitcase Organizer Cubes	1	21.99	21.99
Kazakhstan	Kazakhstan	 Men's Short Sleeve Hero Tee White	1	16.99	10.99
Kazakhstan	Kazakhstan	Collapsible Shopping Bag	1	4.99	10.99
Kenya	Kenya	 Women's Short Sleeve Hero Tee Grey	1	16.99	42.99
Kenya	Kenya	 Men's Quilted Insulated Vest Battleship Grey	1	74.99	42.99
Kenya	Nairobi	 Flashlight	1	59.99	42.99
Kenya	Nairobi	 Men's Long & Lean Tee Grey	1	19.99	42.99
Kosovo	Kosovo	 Men's Vintage Badge Tee Black	1	16.99	16.99
Kuwait	Kuwait	 Men's Vintage Badge Tee Sage	1	16.99	37.65666667
Kuwait	Kuwait	Waterproof Backpack	1	99.99	37.65666667
Kuwait	Kuwait	 Women's Fleece Hoodie	1	55.99	37.65666667
Kuwait	Kuwait	 17oz Stainless Steel Sport Bottle	1	18.99	37.65666667
Kuwait	Kuwait	Women's  Short Sleeve Hero Tee Black	1	16.99	37.65666667
Kuwait	Kuwait	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	37.65666667
Kyrgyzstan	Kyrgyzstan	Rocket Flashlight	1	4.99	4.99
Laos	Laos	 Hard Cover Journal	2	14.99	14.99
Latvia	Latvia	UpCycled Handlebar Bag	1	59.99	28.24
Latvia	Latvia	 Twill Cap	1	10.99	28.24
Latvia	Latvia	 Women's Vintage Hero Tee White	1	16.99	28.24
Latvia	Latvia	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	28.24
Latvia	Latvia	 Lunch Bag	1	13.99	28.24
Latvia	Latvia	 RFID Journal	1	19.99	28.24
Latvia	Latvia	 Men's Microfiber 1/4 Zip Pullover Blue/Indigo	1	74.99	28.24
Latvia	Latvia	Red Spiral  Notebook	1	9.99	28.24
Lebanon	Lebanon	 RFID Journal	1	19.99	19.99
Lithuania	Lithuania	 Luggage Tag	3	8.99	12.99
Lithuania	Vilnius	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	12.99
Luxembourg	Luxembourg	 Toddler Raglan Shirt Blue Heather/Navy	1	23.99	23.99
Macau	Macau	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	11.65666667
Macau	Macau	 Twill Cap	1	10.99	11.65666667
Macau	Macau	 Leatherette Notebook Combo	1	6.99	11.65666667
Macedonia (FYROM)	Macedonia (FYROM)	 Women's Performance Full Zip Jacket Black	1	119.99	47.32333333
Macedonia (FYROM)	Macedonia (FYROM)	22 oz  Bottle Infuser	1	4.99	47.32333333
Macedonia (FYROM)	Macedonia (FYROM)	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	47.32333333
Malaysia	Ipoh	Bottle Opener Clip	1	3.5	27.1426087
Malaysia	Kuala Lumpur	Android Spiral Journal with Pen	1	9.99	27.1426087
Malaysia	Kuala Lumpur	 Leatherette Notebook Combo	1	6.99	27.1426087
Malaysia	Kuala Lumpur	 Women's Short Sleeve Hero Tee Black	1	16.99	27.1426087
Malaysia	Kuala Lumpur	 Men's Long & Lean Tee Charcoal	1	20.99	27.1426087
Malaysia	Kuala Lumpur	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	27.1426087
Malaysia	Kuala Lumpur	26 oz Double Wall Insulated Bottle	1	24.99	27.1426087
Malaysia	Kuala Lumpur	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	27.1426087
Malaysia	Kuala Lumpur	Four Color Retractable Pen	1	2.99	27.1426087
Malaysia	Kuala Lumpur	 Women's Convertible Vest-Jacket Sea Foam Green	1	98.99	27.1426087
Malaysia	Kuala Lumpur	 Men's Short Sleeve Hero Tee Heather	1	18.99	27.1426087
Malaysia	Kuala Lumpur	Android Men's  Zip Hoodie	1	55.99	27.1426087
Malaysia	Kuala Lumpur	 Slim Utility Travel Bag	1	14.99	27.1426087
Malaysia	Kuala Lumpur	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	27.1426087
Malaysia	Kuala Lumpur	 Alpine Style Backpack	1	99.99	27.1426087
Malaysia	Kuala Lumpur	Electronics Accessory Pouch	1	4.99	27.1426087
Malaysia	Kuala Lumpur	 Wool Heather Cap Heather/Black	1	24.99	27.1426087
Malaysia	Malaysia	 Men's Vintage Tank	2	20.99	27.1426087
Malaysia	Malaysia	 Men's Short Sleeve Hero Tee Heather	2	18.99	27.1426087
Malaysia	Malaysia	 Men's Long Sleeve Raglan Ocean Blue	2	24.99	27.1426087
Malaysia	Malaysia	 Toddler Short Sleeve Tee Red	2	16.99	27.1426087
Malaysia	Petaling Jaya	 Women's 1/4 Zip Performance Pullover Black	1	64.99	27.1426087
Malaysia	Petaling Jaya	 Women's Short Sleeve Hero Tee Black	1	16.99	27.1426087
Maldives	Maldives	 Men's Performance Full Zip Jacket Black	1	119.99	119.99
Mali	Mali	 Custom Decals	1	1.99	1.99
Malta	Malta	 RFID Journal	1	19.99	37.99
Malta	Malta	 Women's Fleece Hoodie Black	1	55.99	37.99
Martinique	Martinique	 Water Resistant Bluetooth Speaker	1	34.99	45.49
Martinique	Martinique	 Men's Fleece Hoodie Black	1	55.99	45.49
Mauritius	Mauritius	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	37.49
Mauritius	Mauritius	 Power Bank	1	55.99	37.49
Mexico	Culiacan	20 oz Stainless Steel Insulated Tumbler	1	24.99	25.39
Mexico	Culiacan	 Men's Short Sleeve Hero Tee Black	1	16.99	25.39
Mexico	Culiacan	 Women's 1/4 Zip Performance Pullover Two-Tone Blue	1	64.99	25.39
Mexico	Mexico	 Men's Short Sleeve Hero Tee Black	4	16.99	25.39
Mexico	Mexico City	Women's  Short Sleeve Hero Tee Black	2	16.99	25.39
Mexico	Mexico City	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	25.39
Mexico	Mexico City	 RFID Journal	2	19.99	25.39
Mexico	Monterrey	 Toddler 1/4 Zip Fleece Pewter	1	37.99	25.39
Mexico	Monterrey	 Trucker Hat	1	28.99	25.39
Mexico	Monterrey	 Leatherette Notebook Combo	1	6.99	25.39
Moldova	Moldova	 Custom Decals	1	1.99	4.99
Moldova	Moldova	24 oz  Sergeant Stripe Bottle	1	7.99	4.99
Montenegro	Montenegro	 Custom Decals	1	1.99	1.99
Morocco	Morocco	 Wool Heather Cap Heather/Black	1	24.99	44.77857143
Morocco	Morocco	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	249	44.77857143
Morocco	Morocco	 Women's Short Sleeve Hero Tee Black	1	16.99	44.77857143
Morocco	Morocco	 Custom Decals	1	1.99	44.77857143
Morocco	Morocco	22 oz  Bottle Infuser	1	4.99	44.77857143
Morocco	Morocco	 Stylus Pen w/ LED Light	1	5.5	44.77857143
Morocco	Morocco	 Tote Bag	1	9.99	44.77857143
Myanmar (Burma)	Myanmar (Burma)	22 oz  Bottle Infuser	1	4.99	12.99
Myanmar (Burma)	Myanmar (Burma)	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	12.99
Myanmar (Burma)	Myanmar (Burma)	 Men's Short Sleeve Hero Tee Black	1	16.99	12.99
Nepal	Nepal	 Rucksack	1	69.99	31.32333333
Nepal	Nepal	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	31.32333333
Nepal	Nepal	22 oz  Bottle Infuser	1	4.99	31.32333333
Netherlands	Amsterdam	 Doodle Decal	1	2.99	15.32333333
Netherlands	Amsterdam	 Men's Vintage Henley	1	29.99	15.32333333
Netherlands	Amsterdam	Android Rise 14 oz Mug	1	12.99	15.32333333
Netherlands	Amsterdam	 Hard Cover Journal	1	14.99	15.32333333
Netherlands	Amsterdam	 Luggage Tag	1	14.99	15.32333333
Netherlands	Amsterdam	 Device Holder Sticky Pad	1	4.99	15.32333333
Netherlands	Amsterdam	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	15.32333333
Netherlands	Amsterdam	 Tube Power Bank	1	16.99	15.32333333
Netherlands	Amsterdam	 Canvas Tote Natural/Navy	1	15.99	15.32333333
Netherlands	Amsterdam	Aluminum Handy Emergency Flashlight	1	16.99	15.32333333
Netherlands	Netherlands	 Twill Cap	7	10.99	15.32333333
Netherlands	Rotterdam	 Wool Heather Cap Heather/Black	1	24.99	15.32333333
New Zealand	Auckland	 Women's Lightweight Microfleece Jacket	1	74.99	61.49
New Zealand	Auckland	 G Noise-reducing Bluetooth Headphones	1	145.99	61.49
New Zealand	New Zealand	 RFID Journal	3	19.99	61.49
New Zealand	New Zealand	22 oz  Bottle Infuser	3	4.99	61.49
Nicaragua	Nicaragua	Compact Selfie Stick	1	8.99	8.99
Nigeria	Nigeria	 Alpine Style Backpack	1	99.99	26.92
Nigeria	Nigeria	 Lunch Bag	1	13.99	26.92
Nigeria	Nigeria	 Men's Vintage Tank	1	20.99	26.92
Nigeria	Nigeria	 Onesie Heather	1	23.99	26.92
Nigeria	Nigeria	Keyboard DOT Sticker	1	1.5	26.92
Nigeria	Nigeria	 Twill Cap	1	10.99	26.92
Nigeria	Nigeria	 Men's Short Sleeve Hero Tee Black	1	16.99	26.92
Norway	Norway	 Trucker Hat	2	21.99	16.99
Norway	Norway	 Laptop and Cell Phone Stickers	2	1.99	16.99
Norway	Norway	 Men's 3/4 Sleeve Henley	2	24.99	16.99
Norway	Norway	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	16.99
Norway	Oslo	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	16.99
Oman	Oman	 Hard Cover Journal	1	14.99	14.99
Pakistan	Karachi	 Men's Vintage Tee	1	16.99	14.90833333
Pakistan	Karachi	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	14.90833333
Pakistan	Karachi	 RFID Journal	1	19.99	14.90833333
Pakistan	Karachi	Ballpoint Stylus Pen	1	5.5	14.90833333
Pakistan	Lahore	 Tube Power Bank	1	16.99	14.90833333
Pakistan	Pakistan	22 oz  Bottle Infuser	4	4.99	14.90833333
Palestine	Palestine	 Laptop Backpack	1	99.99	99.99
Panama	Panama	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	19.75733333
Panama	Panama	Bottle Opener Clip	1	3.5	19.75733333
Panama	Panama	22 oz  Bottle Infuser	1	4.99	19.75733333
Panama	Panama	 Men's Convertible Vest-Jacket Pewter	1	98.99	19.75733333
Panama	Panama	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	19.75733333
Panama	Panama	 Men's Short Sleeve Hero Tee Heather	1	18.99	19.75733333
Panama	Panama	Suitcase Organizer Cubes	1	21.99	19.75733333
Panama	Panama	 Custom Decals	1	1.99	19.75733333
Panama	Panama	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	19.75733333
Panama	Panama	 RFID Journal	1	19.99	19.75733333
Panama	Panama	Android Sticker Sheet Ultra Removable	1	2.99	19.75733333
Panama	Panama	Women's  Short Sleeve Hero Tee Black	1	16.99	19.75733333
Panama	Panama	Android Lunch Kit	1	17.99	19.75733333
Panama	Panama	 Luggage Tag	1	8.99	19.75733333
Panama	Panama	 Toddler Raglan Shirt Blue Heather/Navy	1	23.99	19.75733333
Papua New Guinea	Papua New Guinea	 Hard Cover Journal	1	14.99	8.49
Papua New Guinea	Papua New Guinea	 Custom Decals	1	1.99	8.49
Paraguay	Asuncion	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	16.99
Peru	La Victoria	Android Twill Cap	1	10.99	21.92
Peru	La Victoria	 Mobile Phone Vent Mount	1	0	21.92
Peru	La Victoria	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	21.92
Peru	La Victoria	22 oz  Bottle Infuser	1	4.99	21.92
Peru	La Victoria	 Men's Weatherblock Shell Jacket Black	1	92.99	21.92
Peru	La Victoria	 Twill Cap	1	10.99	21.92
Peru	La Victoria	Android Women's Short Sleeve Badge Tee Dark Heather	1	16.99	21.92
Peru	La Victoria	 Women's Vintage Hero Tee Black	1	18.99	21.92
Peru	Peru	Compact Selfie Stick	1	8.99	21.92
Peru	Peru	Compact Selfie Stick	1	8.99	21.92
Peru	Peru	 Hard Cover Journal	1	14.99	21.92
Peru	Peru	 Rucksack	1	69.99	21.92
Peru	Peru	Four Color Retractable Pen	1	2.99	21.92
Peru	Peru	 Insulated Stainless Steel Bottle	1	19.99	21.92
Peru	Peru	Yoga Mat	1	22.99	21.92
Peru	Peru	 17 oz Double Wall Stainless Steel Insulated Bottle	1	24.99	21.92
Peru	Peru	 Luggage Tag	1	8.99	21.92
Peru	Peru	Android Rise 14 oz Mug	1	12.99	21.92
Peru	Peru	Micro Wireless Earbud	1	39.99	21.92
Peru	Peru	Sport Bag	1	0	21.92
Peru	Peru	 Women's Performance Full Zip Jacket Black	1	0	21.92
Peru	Peru	 Men's Short Sleeve Badge Tee Charcoal	1	0	21.92
Peru	Peru	22 oz  Bottle Infuser	1	4.99	21.92
Peru	Peru	Waterproof Gear Bag	1	79.99	21.92
Peru	Peru	 Alpine Style Backpack	1	99.99	21.92
Peru	Peru	 Custom Decals	1	1.99	21.92
Peru	Peru	Compact Selfie Stick	1	8.99	21.92
Peru	Peru	Compact Selfie Stick	1	8.99	21.92
Philippines	Makati	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	34.41105263
Philippines	Makati	 G Noise-reducing Bluetooth Headphones	1	145.99	34.41105263
Philippines	Manila	 Men's Short Sleeve Hero Tee White	2	16.99	34.41105263
Philippines	Philippines	 Men's Short Sleeve Hero Tee Charcoal	3	18.99	34.41105263
Philippines	Philippines	 Men's 100% Cotton Short Sleeve Hero Tee Black	3	16.99	34.41105263
Philippines	Philippines	 Twill Cap	3	10.99	34.41105263
Philippines	Quezon City	Android Twill Cap	1	21.99	34.41105263
Philippines	Quezon City	 Men's Long Sleeve Pullover Badge Tee Heather	1	34.99	34.41105263
Philippines	Quezon City	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	34.41105263
Philippines	Quezon City	Seat Pack Organizer	1	11.99	34.41105263
Philippines	Quezon City	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	34.41105263
Philippines	Quezon City	 Women's Short Sleeve Hero Tee Black	1	16.99	34.41105263
Philippines	Taguig	 RFID Journal	1	19.99	34.41105263
Philippines	Taguig	 Men's Vintage Badge Tee Black	1	18.99	34.41105263
Philippines	Taguig	 Men's Convertible Vest-Jacket Pewter	1	98.99	34.41105263
Philippines	Taguig	 5-Panel Cap	1	18.99	34.41105263
Philippines	Taguig	Metal Texture Roller Pen	1	28.99	34.41105263
Philippines	Taguig	 Men's Watershed Full Zip Hoodie Grey	1	109.99	34.41105263
Philippines	Taguig	 Luggage Tag	1	8.99	34.41105263
Poland	Poland	20 oz Stainless Steel Insulated Tumbler	2	24.99	20.86625
Poland	Poland	Android Men's Engineer Short Sleeve Tee Charcoal	2	19.99	20.86625
Poland	Poland	 Men's Vintage Tank	2	20.99	20.86625
Poland	Poland	Android BTTF Moonshot Graphic Tee	2	19.99	20.86625
Poland	Poznan	 Bongo Cupholder Bluetooth Speaker	1	59.99	20.86625
Poland	Poznan	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	20.86625
Poland	Warsaw	 Laptop and Cell Phone Stickers	2	2.49	20.86625
Poland	Warsaw	Keyboard DOT Sticker	2	1.5	20.86625
Portugal	Lisbon	 Blackout Cap	1	18.99	18.65666667
Portugal	Portugal	 Men's 100% Cotton Short Sleeve Hero Tee Black	2	16.99	18.65666667
Portugal	Portugal	 RFID Journal	2	19.99	18.65666667
Puerto Rico	Puerto Rico	Android Men's Short Sleeve Tri-blend Hero Tee Grey	1	18.99	26.32333333
Puerto Rico	Puerto Rico	 Toddler Short Sleeve T-shirt Grey	1	16.99	26.32333333
Puerto Rico	Puerto Rico	Waterproof Backpack	1	99.99	26.32333333
Puerto Rico	Puerto Rico	 Custom Decals	1	1.99	26.32333333
Puerto Rico	Puerto Rico	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	26.32333333
Puerto Rico	Puerto Rico	Four Color Retractable Pen	1	2.99	26.32333333
Qatar	Doha	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	19.32333333
Qatar	Qatar	 5-Panel Cap	1	18.99	19.32333333
Qatar	Qatar	 Men's Short Sleeve Performance Badge Tee Navy	1	21.99	19.32333333
RÃ©union	RÃ©union	 Doodle Decal	1	2.99	2.49
RÃ©union	RÃ©union	 Custom Decals	1	1.99	2.49
Romania	Bucharest	 Men's 3/4 Sleeve Henley	1	24.99	27.92333333
Romania	Bucharest	 Wool Heather Cap Heather/Navy	1	24.99	27.92333333
Romania	Bucharest	 Alpine Style Backpack	1	99.99	27.92333333
Romania	Bucharest	 Twill Cap	1	10.99	27.92333333
Romania	Bucharest	 Canvas Tote Natural/Navy	1	15.99	27.92333333
Romania	Bucharest	 Custom Decals	1	1.99	27.92333333
Romania	Bucharest	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	27.92333333
Romania	Bucharest	Android Rise 14 oz Mug	1	12.99	27.92333333
Romania	Bucharest	 Men's Convertible Vest-Jacket Pewter	1	98.99	27.92333333
Romania	Cluj-Napoca	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	27.92333333
Romania	Iasi	 Men's Vintage Henley	1	29.99	27.92333333
Romania	Romania	 Custom Decals	4	1.99	27.92333333
Romania	Timisoara	 Screen Cleaning Cloth	1	1.99	27.92333333
Romania	Timisoara	Micro Wireless Earbud	1	39.99	27.92333333
Romania	Timisoara	Android BTTF Cosmos Graphic Tee	1	19.99	27.92333333
Russia	Kovrov	 Women's Fleece Hoodie	1	55.99	23.24136364
Russia	Moscow	 Men's Airflow 1/4 Zip Pullover Lapis	1	69.99	23.24136364
Russia	Moscow	 Kick Ball	1	1.99	23.24136364
Russia	Moscow	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	23.24136364
Russia	Moscow	 Youth Short Sleeve Tee Red	1	18.99	23.24136364
Russia	Moscow	 Women's 1/4 Zip Jacket Charcoal	1	0	23.24136364
Russia	Moscow	SPF-15 Slim & Slender Lip Balm	1	2.5	23.24136364
Russia	Moscow	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	23.24136364
Russia	Moscow	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	23.24136364
Russia	Moscow	 Men's Bayside Graphic Tee	1	19.99	23.24136364
Russia	Moscow	Android Men's Vintage Tank	1	20.99	23.24136364
Russia	Moscow	 Youth Short Sleeve T-shirt Green	1	18.99	23.24136364
Russia	Moscow	 Women's Vintage Hero Tee Black	1	16.99	23.24136364
Russia	Moscow	 Women's Short Sleeve Hero Tee Black	1	16.99	23.24136364
Russia	Moscow	 Women's Fleece Hoodie	1	55.99	23.24136364
Russia	Moscow	 Wool Heather Cap Heather/Black	1	24.99	23.24136364
Russia	Russia	 Twill Cap	4	10.99	23.24136364
Russia	Saint Petersburg	 Hard Cover Journal	1	14.99	23.24136364
Russia	Saint Petersburg	 Women's Lightweight Microfleece Jacket	1	0	23.24136364
Russia	Saint Petersburg	 Men's Quilted Insulated Vest Black	1	74.99	23.24136364
Russia	Saint Petersburg	Leatherette Journal	1	10.99	23.24136364
Russia	Vladivostok	 Canvas Tote Natural/Navy	1	15.99	23.24136364
Rwanda	Rwanda	 Wool Heather Cap Heather/Black	1	24.99	24.99
San Marino	San Marino	22 oz  Bottle Infuser	1	4.99	4.99
San Marino	San Marino	 Device Holder Sticky Pad	1	4.99	4.99
Saudi Arabia	Riyadh	 Youth Short Sleeve T-shirt Yellow	1	18.99	18.49083333
Saudi Arabia	Riyadh	Red Spiral  Notebook	1	9.99	18.49083333
Saudi Arabia	Riyadh	Leatherette Journal	1	10.99	18.49083333
Saudi Arabia	Saudi Arabia	 Rucksack	1	69.99	18.49083333
Saudi Arabia	Saudi Arabia	 Men's Vintage Tank	1	20.99	18.49083333
Saudi Arabia	Saudi Arabia	 Women's Vintage Hero Tee Platinum	1	18.99	18.49083333
Saudi Arabia	Saudi Arabia	 17oz Stainless Steel Sport Bottle	1	18.99	18.49083333
Saudi Arabia	Saudi Arabia	 Twill Cap	1	10.99	18.49083333
Saudi Arabia	Saudi Arabia	Women's  Short Sleeve Hero Tee Black	1	16.99	18.49083333
Saudi Arabia	Saudi Arabia	Android Men's Short Sleeve Hero Tee White	1	16.99	18.49083333
Saudi Arabia	Saudi Arabia	24 oz  Sergeant Stripe Bottle	1	7.99	18.49083333
Saudi Arabia	Saudi Arabia	 Women's Lightweight Microfleece Jacket	1	0	18.49083333
Serbia	Serbia	 Twill Cap	2	10.99	10.99
Singapore	Singapore	 Men's 100% Cotton Short Sleeve Hero Tee Black	3	16.99	16.99
Singapore	Singapore	 Men's 100% Cotton Short Sleeve Hero Tee Black	3	16.99	16.99
Sint Maarten	Sint Maarten	 Stylus Pen w/ LED Light	1	5.5	5.5
Slovakia	Bratislava	 Learning Thermostat 3rd Gen-USA - Copper	1	0	13.61625
Slovakia	Slovakia	 Men's Vintage Henley	2	29.99	13.61625
Slovakia	Slovakia	 Luggage Tag	2	8.99	13.61625
Slovakia	Slovakia	 Twill Cap	2	10.99	13.61625
Slovakia	Slovakia	 RFID Journal	2	19.99	13.61625
Slovakia	Slovakia	 Custom Decals	2	1.99	13.61625
Slovakia	Slovakia	 Hard Cover Journal	2	14.99	13.61625
Slovakia	Slovakia	 Trucker Hat	2	21.99	13.61625
Slovenia	Slovenia	 Women's Short Sleeve Hero Tee Charcoal	1	18.99	29.49
Slovenia	Slovenia	 Toddler Short Sleeve Tee Red	1	16.99	29.49
Slovenia	Slovenia	Electronics Accessory Pouch	1	4.99	29.49
Slovenia	Slovenia	 G Noise-reducing Bluetooth Headphones	1	145.99	29.49
Slovenia	Slovenia	 Hard Cover Journal	1	14.99	29.49
Slovenia	Slovenia	22 oz  Bottle Infuser	1	4.99	29.49
Slovenia	Slovenia	 Trucker Hat	1	21.99	29.49
Slovenia	Slovenia	 Leatherette Notebook Combo	1	6.99	29.49
Somalia	Somalia	 Men's Short Sleeve Hero Tee White	1	16.99	16.99
South Africa	Cape Town	 Men's Pullover Hoodie Grey	1	51.99	27.79
South Africa	Sandton	 Water Resistant Bluetooth Speaker	1	34.99	27.79
South Africa	South Africa	 Men's 100% Cotton Short Sleeve Hero Tee Black	2	16.99	27.79
South Africa	South Africa	 Men's Vintage Henley	2	29.99	27.79
South Africa	Westville	Sport Bag	1	4.99	27.79
South Korea	Seoul	Android Men's Vintage Henley	1	29.99	20.62488889
South Korea	Seoul	 Men's Short Sleeve Performance Badge Tee Pewter	1	21.99	20.62488889
South Korea	Seoul	 Vintage Henley Grey/Black	1	29.99	20.62488889
South Korea	Seoul	Windup Android	1	3.99	20.62488889
South Korea	Seoul	 17oz Stainless Steel Sport Bottle	1	18.99	20.62488889
South Korea	Seoul	Ballpoint Stylus Pen	1	5.5	20.62488889
South Korea	Seoul	Android Men's  Zip Hoodie	1	55.99	20.62488889
South Korea	Seoul	 Leatherette Notebook Combo	1	6.99	20.62488889
South Korea	Seoul	 Men's Weatherblock Shell Jacket Black	1	92.99	20.62488889
South Korea	Seoul	Ballpoint LED Light Pen	1	2.5	20.62488889
South Korea	Seoul	22 oz  Bottle Infuser	1	4.99	20.62488889
South Korea	Seoul	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	20.62488889
South Korea	Seoul	 Flashlight	1	59.99	20.62488889
South Korea	Seoul	 Custom Decals	1	1.99	20.62488889
South Korea	Seoul	24 oz  Sergeant Stripe Bottle	1	7.99	20.62488889
South Korea	Seoul	 Dress Socks	1	8.99	20.62488889
South Korea	Seoul	Red Shine 15 oz Mug	1	12.99	20.62488889
South Korea	Seoul	Compact Selfie Stick	1	8.99	20.62488889
South Korea	Seoul	Android Men's Long Sleeve Badge Crew Tee Heather	1	34.99	20.62488889
South Korea	Seoul	 Baby on Board Window Decal	1	2.99	20.62488889
South Korea	Seoul	 Onesie Green	1	23.99	20.62488889
South Korea	Seoul	Sport Bag	1	4.99	20.62488889
South Korea	South Korea	 Men's Long Sleeve Pullover Badge Tee Heather	1	34.99	20.62488889
South Korea	South Korea	Keyboard DOT Sticker	1	1.5	20.62488889
South Korea	South Korea	 2200mAh Micro Charger	1	22.99	20.62488889
South Korea	South Korea	1 oz Hand Sanitizer	1	1.99	20.62488889
South Korea	South Korea	Seat Pack Organizer	1	11.99	20.62488889
South Korea	South Korea	 Men's Vintage Badge Tee White	1	16.99	20.62488889
South Korea	South Korea	 Women's Short Sleeve Hero Tee White	1	16.99	20.62488889
South Korea	South Korea	 4400mAh Power Bank	1	37.99	20.62488889
South Korea	South Korea	22 oz  Bottle Infuser	1	4.99	20.62488889
South Korea	South Korea	 Stylus Pen w/ LED Light	1	5.5	20.62488889
South Korea	South Korea	Android Toddler Short Sleeve T-shirt Aqua	1	16.99	20.62488889
South Korea	South Korea	 Device Holder Sticky Pad	1	4.99	20.62488889
South Korea	South Korea	 Men's  Zip Hoodie	1	0	20.62488889
South Korea	South Korea	 Blackout Cap	1	18.99	20.62488889
South Korea	South Korea	Rubber Grip Ballpoint Pen 4 Pack	1	0	20.62488889
South Korea	South Korea	 High Capacity 10,400mAh Charger	1	58.99	20.62488889
South Korea	South Korea	 Custom Decals	1	1.99	20.62488889
South Korea	South Korea	Ballpoint Stylus Pen	1	5.5	20.62488889
South Korea	South Korea	 Screen Cleaning Cloth	1	1.99	20.62488889
South Korea	South Korea	 G Noise-reducing Bluetooth Headphones	1	145.99	20.62488889
South Korea	South Korea	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	20.62488889
South Korea	South Korea	Android BTTF Cosmos Graphic Tee	1	19.99	20.62488889
South Korea	South Korea	 Twill Cap	1	10.99	20.62488889
Spain	Barcelona	 Men's Short Sleeve Hero Tee White	1	16.99	18.29392857
Spain	Barcelona	26 oz Double Wall Insulated Bottle	1	24.99	18.29392857
Spain	Barcelona	 Luggage Tag	1	8.99	18.29392857
Spain	Barcelona	 Mobile Phone Vent Mount	1	6.99	18.29392857
Spain	Barcelona	 Snapback Hat Black	1	22.99	18.29392857
Spain	Barcelona	24 oz  Sergeant Stripe Bottle	1	7.99	18.29392857
Spain	Barcelona	 Men's Vintage Henley	1	29.99	18.29392857
Spain	Barcelona	Android Rise 14 oz Mug	1	12.99	18.29392857
Spain	Barcelona	Android Hard Cover Journal	1	14.99	18.29392857
Spain	Barcelona	Badge Holder	1	1.99	18.29392857
Spain	Barcelona	 Men's Pullover Hoodie Grey	1	51.99	18.29392857
Spain	Barcelona	 Men's Short Sleeve Hero Tee Black	1	16.99	18.29392857
Spain	Barcelona	 Laptop and Cell Phone Stickers	1	1.99	18.29392857
Spain	Barcelona	 Men's Long & Lean Tee Charcoal	1	19.99	18.29392857
Spain	Barcelona	 Rucksack	1	69.99	18.29392857
Spain	Barcelona	 Tri-blend Hoodie Grey	1	39.99	18.29392857
Spain	Barcelona	Android Onesie Gold	1	23.99	18.29392857
Spain	Bilbao	 Trucker Hat	1	21.99	18.29392857
Spain	Bilbao	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	18.29392857
Spain	Madrid	SPF-15 Slim & Slender Lip Balm	2	2.5	18.29392857
Spain	Madrid	 Dress Socks	2	8.99	18.29392857
Spain	Pozuelo de Alarcon	 Car Clip Phone Holder	1	6.99	18.29392857
Spain	Pozuelo de Alarcon	Rocket Flashlight	1	4.99	18.29392857
Spain	Pozuelo de Alarcon	 Hard Cover Journal	1	14.99	18.29392857
Spain	Spain	22 oz  Bottle Infuser	3	4.99	18.29392857
Spain	Spain	 Men's 100% Cotton Short Sleeve Hero Tee Navy	3	16.99	18.29392857
Spain	Spain	 Men's Short Sleeve Hero Tee White	3	16.99	18.29392857
Spain	Spain	 RFID Journal	3	19.99	18.29392857
Sri Lanka	Colombo	Android Men's Vintage Henley	2	29.99	23.52923077
Sri Lanka	Sri Lanka	 Men's Long & Lean Tee Grey	1	19.99	23.52923077
Sri Lanka	Sri Lanka	 Wool Heather Cap Heather/Black	1	24.99	23.52923077
Sri Lanka	Sri Lanka	 Cam Outdoor Security Camera - USA	1	0	23.52923077
Sri Lanka	Sri Lanka	Android Men's Vintage Tank	1	20.99	23.52923077
Sri Lanka	Sri Lanka	 Men's 3/4 Sleeve Henley	1	24.99	23.52923077
Sri Lanka	Sri Lanka	 Men's Long & Lean Tee Charcoal	1	19.99	23.52923077
Sri Lanka	Sri Lanka	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	23.52923077
Sri Lanka	Sri Lanka	 Men's Vintage Badge Tee Sage	1	18.99	23.52923077
Sri Lanka	Sri Lanka	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	23.52923077
Sri Lanka	Sri Lanka	 Men's Short Sleeve Hero Tee White	1	16.99	23.52923077
Sri Lanka	Sri Lanka	 Custom Decals	1	1.99	23.52923077
Sri Lanka	Sri Lanka	 Men's Weatherblock Shell Jacket Black	1	92.99	23.52923077
Sudan	Sudan	 RFID Journal	1	19.99	19.99
Sweden	Stockholm	 Men's Pullover Hoodie Grey	1	51.99	23.02
Sweden	Stockholm	 Men's Short Sleeve Hero Tee Black	1	16.99	23.02
Sweden	Stockholm	Ballpoint Stylus Pen	1	5.5	23.02
Sweden	Stockholm	 Men's Short Sleeve Hero Tee White	1	16.99	23.02
Sweden	Stockholm	24 oz  Sergeant Stripe Bottle	1	7.99	23.02
Sweden	Stockholm	 Water Resistant Bluetooth Speaker	1	34.99	23.02
Sweden	Stockholm	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	23.02
Sweden	Stockholm	Crunch Noise Dog Toy	1	4.99	23.02
Sweden	Stockholm	Waterproof Backpack	1	79.99	23.02
Sweden	Stockholm	 Men's Short Sleeve Hero Tee Heather	1	18.99	23.02
Sweden	Stockholm	 Women's Short Sleeve Hero Tee Red Heather	1	18.99	23.02
Sweden	Stockholm	Metal Texture Roller Pen	1	28.99	23.02
Sweden	Stockholm	 French Terry Cap	1	18.99	23.02
Sweden	Stockholm	Android Rise 14 oz Mug	1	12.99	23.02
Sweden	Stockholm	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	23.02
Sweden	Sweden	 Leatherette Notebook Combo	3	6.99	23.02
Sweden	Sweden	 Men's Vintage Henley	3	29.99	23.02
Switzerland	Switzerland	 Trucker Hat	3	21.99	15.32333333
Switzerland	Zurich	 Men's Vintage Badge Tee Black	2	18.99	15.32333333
Switzerland	Zurich	Sport Bag	2	4.99	15.32333333
Taiwan	Longtan District	26 oz Double Wall Insulated Bottle	1	24.99	21.59
Taiwan	Neipu Township	 Women's Fleece Hoodie	1	55.99	21.59
Taiwan	Taiwan	Sport Bag	4	4.99	21.59
Taiwan	Taiwan	Sport Bag	4	4.99	21.59
Taiwan	Zhongli District	 Men's 100% Cotton Short Sleeve Hero Tee White	2	16.99	21.59
Tanzania	Tanzania	 Twill Cap	1	10.99	10.99
Thailand	Bangkok	 Twill Cap	2	10.99	14.77785714
Thailand	Bangkok	Keyboard DOT Sticker	2	1.5	14.77785714
Thailand	Bangkok	 Men's Vintage Henley	2	14.995	14.77785714
Thailand	Thailand	 Men's Vintage Henley	2	29.99	14.77785714
Thailand	Thailand	 Trucker Hat	2	21.99	14.77785714
Thailand	Thailand	 Men's Vintage Badge Tee Black	2	18.99	14.77785714
Thailand	Thailand	22 oz  Bottle Infuser	2	4.99	14.77785714
Trinidad & Tobago	Trinidad & Tobago	 Twill Cap	1	10.99	12.99
Trinidad & Tobago	Trinidad & Tobago	 Hard Cover Journal	1	14.99	12.99
Tunisia	Tunisia	 Device Holder Sticky Pad	2	4.99	4.99
Turkey	Antalya	 Tube Power Bank	1	0	36.15833333
Turkey	Istanbul	 Trucker Hat	2	21.99	36.15833333
Turkey	Istanbul	 G Noise-reducing Bluetooth Headphones	2	145.99	36.15833333
Turkey	Izmir	 Women's Short Sleeve Hero Tee White	1	16.99	36.15833333
Turkey	Izmir	 Women's Short Sleeve Hero Tee Black	1	16.99	36.15833333
Turkey	Turkey	 Hard Cover Journal	3	14.99	36.15833333
Uganda	Uganda	 Laptop and Cell Phone Stickers	1	2.99	2.99
Ukraine	Kharkiv	 Women's V-Neck Tee Charcoal	1	18.99	24.88
Ukraine	Kiev	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	24.88
Ukraine	Kiev	Keyboard DOT Sticker	1	1.5	24.88
Ukraine	Kiev	22 oz Android Bottle	1	2.99	24.88
Ukraine	Kiev	Metal Texture Roller Pen	1	28.99	24.88
Ukraine	Kiev	 Women's Vintage Hero Tee White	1	18.99	24.88
Ukraine	Kiev	Android Rise 14 oz Mug	1	12.99	24.88
Ukraine	Kiev	 Tote Bag	1	9.99	24.88
Ukraine	Kiev	 Alpine Style Backpack	1	99.99	24.88
Ukraine	Kiev	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	24.88
Ukraine	Kiev	 Toddler Short Sleeve Tee White	1	16.99	24.88
Ukraine	Kiev	 Women's Fleece Hoodie	1	55.99	24.88
Ukraine	Kiev	Micro Wireless Earbud	1	39.99	24.88
Ukraine	Kiev	Waterproof Gear Bag	1	79.99	24.88
Ukraine	Kiev	 Leatherette Notebook Combo	1	6.99	24.88
Ukraine	Kiev	Maze Pen	1	0.99	24.88
Ukraine	Kiev	Bottle Opener Clip	1	3.5	24.88
Ukraine	Ukraine	 Hard Cover Journal	3	14.99	24.88
United Arab Emirates	Dubai	Recycled Mouse Pad	1	1.5	26.37294118
United Arab Emirates	Dubai	 Men's Short Sleeve Performance Badge Tee Pewter	1	21.99	26.37294118
United Arab Emirates	Dubai	Android Youth Short Sleeve T-shirt Aqua	1	18.99	26.37294118
United Arab Emirates	Dubai	 Women's Convertible Vest-Jacket Sea Foam Green	1	98.99	26.37294118
United Arab Emirates	Dubai	Leatherette Journal	1	10.99	26.37294118
United Arab Emirates	Dubai	 Women's Long Sleeve Tee Lavender	1	23.99	26.37294118
United Arab Emirates	Dubai	 Hard Cover Journal	1	14.99	26.37294118
United Arab Emirates	United Arab Emirates	 Water Resistant Bluetooth Speaker	1	34.99	26.37294118
United Arab Emirates	United Arab Emirates	22 oz  Bottle Infuser	1	4.99	26.37294118
United Arab Emirates	United Arab Emirates	 Men's Long & Lean Tee Charcoal	1	19.99	26.37294118
United Arab Emirates	United Arab Emirates	Pen Pencil & Highlighter Set	1	3.99	26.37294118
United Arab Emirates	United Arab Emirates	 Men's Short Sleeve Performance Badge Tee Navy	1	21.99	26.37294118
United Arab Emirates	United Arab Emirates	Android Rise 14 oz Mug	1	12.99	26.37294118
United Arab Emirates	United Arab Emirates	 Women's Fleece Hoodie	1	55.99	26.37294118
United Arab Emirates	United Arab Emirates	 Men's Vintage Tank	1	20.99	26.37294118
United Arab Emirates	United Arab Emirates	Android Men's  Zip Hoodie	1	55.99	26.37294118
United Arab Emirates	United Arab Emirates	 Wool Heather Cap Heather/Black	1	24.99	26.37294118
United Kingdom	Coventry	 Youth Short Sleeve Tee Red	1	18.99	27.80818182
United Kingdom	Egham	 Men's Short Sleeve Hero Tee White	1	16.99	27.80818182
United Kingdom	Glasgow	 Water Resistant Bluetooth Speaker	1	34.99	27.80818182
United Kingdom	London	 Twill Cap	10	10.99	27.80818182
United Kingdom	Manchester	 G Noise-reducing Bluetooth Headphones	1	145.99	27.80818182
United Kingdom	Manchester	 Luggage Tag	1	8.99	27.80818182
United Kingdom	Oxford	 Water Resistant Bluetooth Speaker	1	34.99	27.80818182
United Kingdom	Salford	 Car Clip Phone Holder	1	6.99	27.80818182
United Kingdom	United Kingdom	 Hard Cover Journal	21	14.99	27.80818182
United Kingdom	Watford	 Luggage Tag	1	8.99	27.80818182
United Kingdom	Wrexham	 Laptop and Cell Phone Stickers	1	2.99	27.80818182
United States	Akron	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	33.99093467
United States	Amsterdam	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	33.99093467
United States	Ann Arbor	 Men's 100% Cotton Short Sleeve Hero Tee Navy	2	15.29	33.99093467
United States	Ann Arbor	 Men's Convertible Vest-Jacket Pewter	2	98.99	33.99093467
United States	Ann Arbor	 Men's Vintage Badge Tee Black	2	17.09	33.99093467
United States	Appleton	Aluminum Handy Emergency Flashlight	1	16.99	33.99093467
United States	Ashburn	Compact Selfie Stick	1	8.99	33.99093467
United States	Ashburn	Android Lunch Kit	1	17.99	33.99093467
United States	Atlanta	 Men's 100% Cotton Short Sleeve Hero Tee Navy	4	12.7425	33.99093467
United States	Atlanta	 Twill Cap	4	10.99	33.99093467
United States	Austin	 Men's Vintage Badge Tee Black	3	17.65666667	33.99093467
United States	Austin	 Women's Short Sleeve Hero Tee Sky Blue	3	18.99	33.99093467
United States	Austin	22 oz Android Bottle	3	3.123333333	33.99093467
United States	Avon	 Blackout Cap	1	18.99	33.99093467
United States	Avon	Bottle Opener Clip	1	3.5	33.99093467
United States	Bellflower	 Custom Decals	2	1.99	33.99093467
United States	Bellingham	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	249	33.99093467
United States	Bellingham	 Custom Decals	1	1.99	33.99093467
United States	Berkeley	 Onesie Heather	1	23.99	33.99093467
United States	Boston	 Laptop Backpack	2	99.99	33.99093467
United States	Boston	Collapsible Shopping Bag	2	4.99	33.99093467
United States	Boulder	 Men's Performance Full Zip Jacket Black	1	119.99	33.99093467
United States	Boulder	Bottle Opener Clip	1	3.5	33.99093467
United States	Boulder	Compact Selfie Stick	1	8.99	33.99093467
United States	Boulder	 Toddler Short Sleeve Tee Red	1	16.99	33.99093467
United States	Boulder	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Boulder	 Twill Cap	1	10.99	33.99093467
United States	Cambridge	 Screen Cleaning Cloth	3	1.856666667	33.99093467
United States	Charlotte	7 Dog Frisbee	2	1.5	33.99093467
United States	Charlotte	Android Men's  Zip Hoodie	2	27.995	33.99093467
United States	Chicago	 Men's 100% Cotton Short Sleeve Hero Tee White	7	16.99	33.99093467
United States	Chico	 Men's 3/4 Sleeve Henley	1	24.99	33.99093467
United States	Chico	Android Men's Long Sleeve Badge Crew Tee Heather	1	34.99	33.99093467
United States	Columbia	 Device Stand	1	4.99	33.99093467
United States	Columbus	Android Men's  Zip Hoodie	1	55.99	33.99093467
United States	Columbus	 Men's Short Sleeve Badge Tee Charcoal	1	18.99	33.99093467
United States	Council Bluffs	 Men's Long Sleeve Raglan Ocean Blue	1	24.99	33.99093467
United States	Council Bluffs	 Kick Ball	1	1.59	33.99093467
United States	Cupertino	 Men's Vintage Tank	2	18.89	33.99093467
United States	Dallas	 Leatherette Notebook Combo	2	6.99	33.99093467
United States	Dallas	 Women's Short Sleeve Hero Tee Black	2	16.99	33.99093467
United States	Dallas	Micro Wireless Earbud	2	39.99	33.99093467
United States	Denver	 4400mAh Power Bank	1	37.99	33.99093467
United States	Denver	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Denver	Waterproof Backpack	1	99.99	33.99093467
United States	Denver	 Twill Cap	1	10.99	33.99093467
United States	Denver	 Luggage Tag	1	8.99	33.99093467
United States	Denver	Crunch Noise Dog Toy	1	4.99	33.99093467
United States	Detroit	 Screen Cleaning Cloth	1	1.99	33.99093467
United States	Detroit	 Wool Heather Cap Heather/Navy	1	24.99	33.99093467
United States	Detroit	 High Capacity 10,400mAh Charger	1	58.99	33.99093467
United States	Detroit	 22 oz Water Bottle	1	2.99	33.99093467
United States	Druid Hills	Colored Pencil Set	1	2.99	33.99093467
United States	East Lansing	25L Classic Rucksack	1	99.99	33.99093467
United States	Eau Claire	 Men's 100% Cotton Short Sleeve Hero Tee Black	2	16.99	33.99093467
United States	Eau Claire	 Men's Short Sleeve Hero Tee Charcoal	2	18.99	33.99093467
United States	El Paso	 Men's Short Sleeve Hero Tee Light Blue	1	18.99	33.99093467
United States	Forest Park	 Men's Microfiber 1/4 Zip Pullover Blue/Indigo	1	59.99	33.99093467
United States	Fort Worth	 Trucker Hat	1	21.99	33.99093467
United States	Fremont	Android Rise 14 oz Mug	1	12.99	33.99093467
United States	Fremont	Colored Pencil Set	1	2.99	33.99093467
United States	Fremont	 Men's Convertible Vest-Jacket Pewter	1	98.99	33.99093467
United States	Fremont	 Laptop Backpack	1	99.99	33.99093467
United States	Fremont	 Men's Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Fremont	 Women's Vintage Hero Tee Black	1	18.99	33.99093467
United States	Fremont	 Tri-blend Hoodie Grey	1	39.99	33.99093467
United States	Fremont	 Protect Smoke + CO White Battery Alarm-USA	1	119	33.99093467
United States	Fremont	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Fremont	Android Men's  Zip Hoodie	1	55.99	33.99093467
United States	Fremont	Windup Android	1	3.99	33.99093467
United States	Fremont	 RFID Journal	1	19.99	33.99093467
United States	Fremont	 Stylus Pen w/ LED Light	1	5.5	33.99093467
United States	Fremont	 2200mAh Micro Charger	1	18.39	33.99093467
United States	Goose Creek	Android Wool Heather Cap Heather/Black	1	24.99	33.99093467
United States	Greer	 Men's Quilted Insulated Vest Black	1	74.99	33.99093467
United States	Hayward	 Men's Vintage Badge Tee White	1	18.99	33.99093467
United States	Houston	 Men's Short Sleeve Hero Tee Black	3	16.99	33.99093467
United States	Houston	 Men's Long Sleeve Raglan Ocean Blue	3	24.99	33.99093467
United States	Indianapolis	Ballpoint Stylus Pen	1	5.5	33.99093467
United States	Irvine	 Laptop and Cell Phone Stickers	1	1.59	33.99093467
United States	Irvine	 Women's Convertible Vest-Jacket Sea Foam Green	1	0	33.99093467
United States	Irvine	Switch Tone Color Crayon Pen	1	1.99	33.99093467
United States	Irvine	Android Rise 14 oz Mug	1	12.99	33.99093467
United States	Irvine	 High Capacity 10,400mAh Charger	1	58.99	33.99093467
United States	Irvine	 Trucker Hat	1	21.99	33.99093467
United States	Irvine	 5-Panel Cap	1	18.99	33.99093467
United States	Irvine	 Wool Heather Cap Heather/Navy	1	24.99	33.99093467
United States	Irvine	 Tote Bag	1	9.99	33.99093467
United States	Irvine	Sport Bag	1	0	33.99093467
United States	Irvine	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	249	33.99093467
United States	Irvine	 Women's Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Irvine	Recycled Mouse Pad	1	1.5	33.99093467
United States	Irvine	Android 17oz Stainless Steel Sport Bottle	1	18.99	33.99093467
United States	Irvine	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	33.99093467
United States	Irvine	 Men's Vintage Badge Tee Sage	1	18.99	33.99093467
United States	Irvine	 Men's 100% Cotton Short Sleeve Hero Tee White	1	13.59	33.99093467
United States	Irvine	 Women's Scoop Neck Tee Black	1	19.99	33.99093467
United States	Jacksonville	20 oz Stainless Steel Insulated Tumbler	1	24.99	33.99093467
United States	Jacksonville	 Device Stand	1	4.99	33.99093467
United States	Jersey City	 Youth Short Sleeve Tee White	1	18.99	33.99093467
United States	Jersey City	 Women's Short Sleeve Hero Dark Grey	1	18.99	33.99093467
United States	Jersey City	 Women's 3/4 Sleeve Baseball Raglan Heather/Black	1	0	33.99093467
United States	Kalamazoo	Satin Black Ballpoint Pen	1	39.99	33.99093467
United States	Kansas City	 Insulated Stainless Steel Bottle	1	19.99	33.99093467
United States	Kansas City	 Women's Short Sleeve Hero Tee Heather	1	18.99	33.99093467
United States	Kansas City	 Women's Short Sleeve Hero Tee Red Heather	1	18.99	33.99093467
United States	Kirkland	Waterproof Backpack	2	99.99	33.99093467
United States	Kirkland	 Women's Lightweight Microfleece Jacket	2	37.495	33.99093467
United States	LaFayette	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	33.99093467
United States	Lake Oswego	Recycled Mouse Pad	1	1.5	33.99093467
United States	Lake Oswego	 Men's Short Sleeve Hero Tee Heather	1	18.99	33.99093467
United States	Lake Oswego	 Women's Short Sleeve Performance Tee Pewter	1	21.99	33.99093467
United States	Lake Oswego	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	33.99093467
United States	Lake Oswego	 Men's Performance Full Zip Jacket Black	1	0	33.99093467
United States	Las Vegas	 Men's Airflow 1/4 Zip Pullover Lapis	1	69.99	33.99093467
United States	Las Vegas	Android Youth Short Sleeve T-shirt Pewter	1	18.99	33.99093467
United States	Las Vegas	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Los Angeles	 Men's 100% Cotton Short Sleeve Hero Tee White	7	16.99	33.99093467
United States	Madison	 Tote Bag	1	9.99	33.99093467
United States	Marlboro	 Men's Microfiber 1/4 Zip Pullover Blue/Indigo	1	0	33.99093467
United States	Menlo Park	 Men's Weatherblock Shell Jacket Black	1	92.99	33.99093467
United States	Menlo Park	 Snapback Hat Black	1	22.99	33.99093467
United States	Menlo Park	 Cam Indoor Security Camera - USA	1	199	33.99093467
United States	Milpitas	 Men's Microfiber 1/4 Zip Pullover Blue/Indigo	1	74.99	33.99093467
United States	Milpitas	Crunch Noise Dog Toy	1	4.99	33.99093467
United States	Milpitas	 Men's  Zip Hoodie	1	55.99	33.99093467
United States	Milpitas	 Women's Quilted Insulated Vest Black	1	74.99	33.99093467
United States	Milpitas	 Women's Lightweight Microfleece Jacket	1	74.99	33.99093467
United States	Milpitas	 Women's Short Sleeve Hero Dark Grey	1	18.99	33.99093467
United States	Milpitas	 Women's Short Sleeve Hero Tee Sky Blue	1	18.99	33.99093467
United States	Milpitas	Android Stretch Fit Hat	1	17.59	33.99093467
United States	Minneapolis	Android Men's Short Sleeve Tri-blend Hero Tee Grey	1	18.99	33.99093467
United States	Minneapolis	 Men's Vintage Badge Tee White	1	18.99	33.99093467
United States	Mountain View	 Cam Indoor Security Camera - USA	24	172.3333333	33.99093467
United States	Nashville	 Learning Thermostat 3rd Gen-USA - Stainless Steel	1	149	33.99093467
United States	New York	 Twill Cap	15	10.84333333	33.99093467
United States	Norfolk	Suitcase Organizer Cubes	1	21.99	33.99093467
United States	Norfolk	 Trucker Hat	1	21.99	33.99093467
United States	Norfolk	 Women's Short Sleeve Hero Tee Red Heather	1	18.99	33.99093467
United States	Oakland	 Women's Convertible Vest-Jacket Sea Foam Green	1	98.99	33.99093467
United States	Oakland	Yoga Block	1	12.99	33.99093467
United States	Oakland	 Doodle Decal	1	2.99	33.99093467
United States	Oakland	Keyboard DOT Sticker	1	1.5	33.99093467
United States	Oakland	Android Men's  Zip Hoodie	1	55.99	33.99093467
United States	Oakland	 Laptop and Cell Phone Stickers	1	1.99	33.99093467
United States	Oakland	 Rucksack	1	69.99	33.99093467
United States	Orlando	Suitcase Organizer Cubes	1	21.99	33.99093467
United States	Orlando	 Men's Watershed Full Zip Hoodie Grey	1	109.99	33.99093467
United States	Orlando	Women's  Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Orlando	 Wool Heather Cap Heather/Black	1	24.99	33.99093467
United States	Orlando	 Men's Vintage Henley	1	29.99	33.99093467
United States	Orlando	 Men's Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	Oviedo	 Luggage Tag	1	8.99	33.99093467
United States	Oviedo	 Wool Heather Cap Heather/Black	1	24.99	33.99093467
United States	Oviedo	 5-Panel Snapback Cap	1	18.99	33.99093467
United States	Palo Alto	 Cam Outdoor Security Camera - USA	7	176.1428571	33.99093467
United States	Panama City	 Men's Convertible Vest-Jacket Pewter	1	98.99	33.99093467
United States	Parsippany-Troy Hills	 Men's Lightweight Microfleece Jacket Black	1	74.99	33.99093467
United States	Philadelphia	 Men's Vintage Tank	2	20.99	33.99093467
United States	Phoenix	Switch Tone Color Crayon Pen	1	1.99	33.99093467
United States	Phoenix	 Cam Indoor Security Camera - USA	1	199	33.99093467
United States	Phoenix	 Men's Short Sleeve Hero Tee Heather	1	18.99	33.99093467
United States	Phoenix	Android 5-Panel Low Cap	1	18.99	33.99093467
United States	Phoenix	 Screen Cleaning Cloth	1	1.99	33.99093467
United States	Phoenix	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	33.99093467
United States	Piscataway Township	 Men's 100% Cotton Short Sleeve Hero Tee Red	1	16.99	33.99093467
United States	Pittsburgh	Collapsible Shopping Bag	2	4.99	33.99093467
United States	Pleasanton	Electronics Accessory Pouch	1	4.99	33.99093467
United States	Portland	 Women's 1/4 Zip Jacket Charcoal	1	0	33.99093467
United States	Portland	 Tri-blend Hoodie Grey	1	0	33.99093467
United States	Portland	 Trucker Hat	1	21.99	33.99093467
United States	Portland	 Men's 100% Cotton Short Sleeve Hero Tee Navy	1	16.99	33.99093467
United States	Portland	 Men's Short Sleeve Hero Tee Charcoal	1	18.99	33.99093467
United States	Portland	 Women's Convertible Vest-Jacket Sea Foam Green	1	98.99	33.99093467
United States	Redmond	 RFID Journal	2	19.99	33.99093467
United States	Redwood City	 Men's Vintage Badge Tee Sage	1	18.99	33.99093467
United States	Redwood City	 Cam Indoor Security Camera - USA	1	199	33.99093467
United States	Redwood City	 Women's Convertible Vest-Jacket Black	1	98.99	33.99093467
United States	Redwood City	 Women's Short Sleeve Hero Tee White	1	16.99	33.99093467
United States	Redwood City	 Men's Convertible Vest-Jacket Pewter	1	98.99	33.99093467
United States	Redwood City	 Lunch Bag	1	13.99	33.99093467
United States	Rexburg	24 oz  Sergeant Stripe Bottle	1	7.99	33.99093467
United States	Rexburg	Android 17oz Stainless Steel Sport Bottle	1	18.99	33.99093467
United States	Rexburg	 17oz Stainless Steel Sport Bottle	1	18.99	33.99093467
United States	Richardson	Micro Wireless Earbud	1	39.99	33.99093467
United States	Sacramento	 Blackout Cap	1	18.99	33.99093467
United States	Salem	 Bib White	2	11.99	33.99093467
United States	Salem	 17oz Stainless Steel Sport Bottle	2	18.99	33.99093467
United States	Salem	 Men's Long Sleeve Raglan Ocean Blue	2	22.99	33.99093467
United States	San Antonio	Android 17oz Stainless Steel Sport Bottle	2	18.99	33.99093467
United States	San Bruno	Foam Can and Bottle Cooler	2	0.795	33.99093467
United States	San Bruno	 RFID Journal	2	19.99	33.99093467
United States	San Bruno	 Trucker Hat	2	21.99	33.99093467
United States	San Bruno	 Water Resistant Bluetooth Speaker	2	31.49	33.99093467
United States	San Bruno	 Twill Cap	2	10.99	33.99093467
United States	San Bruno	22 oz  Bottle Infuser	2	4.49	33.99093467
United States	San Diego	 Men's 100% Cotton Short Sleeve Hero Tee Black	2	8.495	33.99093467
United States	San Diego	 Zipper-front Sports Bag	2	19.99	33.99093467
United States	San Francisco	 Men's 100% Cotton Short Sleeve Hero Tee White	12	15.0075	33.99093467
United States	San Jose	 Men's 100% Cotton Short Sleeve Hero Tee White	8	16.99	33.99093467
United States	San Mateo	 Trucker Hat	1	21.99	33.99093467
United States	San Mateo	 Tri-blend Hoodie Grey	1	39.99	33.99093467
United States	San Mateo	 Device Holder Sticky Pad	1	4.99	33.99093467
United States	Santa Clara	 Trucker Hat	2	21.99	33.99093467
United States	Santa Clara	 Men's Quilted Insulated Vest Black	2	74.99	33.99093467
United States	Santa Clara	 Women's Vintage Hero Tee Black	2	17.99	33.99093467
United States	Santa Clara	 Cam Indoor Security Camera - USA	2	199	33.99093467
United States	Santa Clara	 Women's Short Sleeve Hero Tee White	2	15.29	33.99093467
United States	Santa Monica	 Slim Utility Travel Bag	1	14.99	33.99093467
United States	Seattle	 Twill Cap	4	10.99	33.99093467
United States	Seattle	Android Men's  Zip Hoodie	4	53.19	33.99093467
United States	Seattle	 Laptop and Cell Phone Stickers	4	2.24	33.99093467
United States	South El Monte	 Men's Vintage Badge Tee White	1	16.99	33.99093467
United States	South San Francisco	 Rucksack	1	69.99	33.99093467
United States	South San Francisco	Android Sticker Sheet Ultra Removable	1	2.99	33.99093467
United States	South San Francisco	 Men's 100% Cotton Short Sleeve Hero Tee Black	1	16.99	33.99093467
United States	South San Francisco	 Men's Short Sleeve Hero Tee Heather	1	18.99	33.99093467
United States	St. Louis	 Bib Red	1	11.99	33.99093467
United States	St. Louis	 Wool Heather Cap Heather/Navy	1	24.99	33.99093467
United States	Stanford	 Men's  Zip Hoodie	1	55.99	33.99093467
United States	Sunnyvale	 Cam Indoor Security Camera - USA	6	139.1666667	33.99093467
United States	Sunnyvale	 Twill Cap	6	10.62333333	33.99093467
United States	Sunnyvale	 Cam Outdoor Security Camera - USA	6	172.3333333	33.99093467
United States	Sunnyvale	 Men's Short Sleeve Hero Tee Charcoal	6	18.35666667	33.99093467
United States	Tampa	Android Wool Heather Cap Heather/Black	1	24.99	33.99093467
United States	Tempe	 Snapback Hat Black	1	22.99	33.99093467
United States	Tempe	Windup Android	1	3.99	33.99093467
United States	Tempe	Android Men's Engineer Short Sleeve Tee Charcoal	1	19.99	33.99093467
United States	Tempe	 Car Clip Phone Holder	1	6.99	33.99093467
United States	The Dalles	 Men's Long Sleeve Pullover Badge Tee Heather	1	36.99	33.99093467
United States	The Dalles	 Laptop and Cell Phone Stickers	1	1.99	33.99093467
United States	United States	 Twill Cap	86	10.99	33.99093467
United States	University Park	Waterproof Backpack	1	99.99	33.99093467
United States	Vancouver	 Women's Fleece Hoodie	1	0	33.99093467
United States	Washington	 Men's 100% Cotton Short Sleeve Hero Tee Navy	2	16.99	33.99093467
United States	Washington	 Men's 100% Cotton Short Sleeve Hero Tee White	2	16.99	33.99093467
United States	Washington	 Insulated Stainless Steel Bottle	2	19.99	33.99093467
United States	Washington	 G Noise-reducing Bluetooth Headphones	2	145.99	33.99093467
United States	Washington	 Laptop Backpack	2	99.99	33.99093467
United States	Wellesley	Colored Pencil Set	1	2.99	33.99093467
United States	Westlake Village	 Men's Long Sleeve Raglan Ocean Blue	1	0	33.99093467
United States	Yokohama	 Men's Pullover Hoodie Grey	1	51.99	33.99093467
Uruguay	Montevideo	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	22.65666667
Uruguay	Montevideo	 Vintage Henley Grey/Black	1	29.99	22.65666667
Uruguay	Uruguay	 Men's Vintage Tank	2	20.99	22.65666667
Venezuela	Maracaibo	Android Men's Vintage Henley	1	29.99	18.24
Venezuela	Maracaibo	 Tube Power Bank	1	16.99	18.24
Venezuela	Venezuela	 Hard Cover Journal	2	14.99	18.24
Venezuela	Venezuela	 Twill Cap	2	10.99	18.24
Vietnam	Hanoi	 Luggage Tag	1	8.99	25.20571429
Vietnam	Hanoi	26 oz Double Wall Insulated Bottle	1	24.99	25.20571429
Vietnam	Hanoi	 Dress Socks	1	8.99	25.20571429
Vietnam	Hanoi	 Men's 100% Cotton Short Sleeve Hero Tee White	1	16.99	25.20571429
Vietnam	Ho Chi Minh City	 Men's Short Sleeve Badge Tee Charcoal	1	18.99	25.20571429
Vietnam	Ho Chi Minh City	Recycled Mouse Pad	1	1.5	25.20571429
Vietnam	Ho Chi Minh City	Android Men's Long Sleeve Badge Crew Tee Heather	1	34.99	25.20571429
Vietnam	Ho Chi Minh City	 Laptop Backpack	1	99.99	25.20571429
Vietnam	Ho Chi Minh City	 Toddler Short Sleeve Tee Red	1	16.99	25.20571429
Vietnam	Ho Chi Minh City	Basecamp Explorer Powerbank Flashlight	1	22.99	25.20571429
Vietnam	Ho Chi Minh City	Keyboard DOT Sticker	1	1.5	25.20571429
Vietnam	Ho Chi Minh City	22 oz  Bottle Infuser	1	4.99	25.20571429
Vietnam	Ho Chi Minh City	 Men's Airflow 1/4 Zip Pullover Black	1	69.99	25.20571429
Vietnam	Vietnam	Android Men's Vintage Tank	2	20.99	25.20571429
Zimbabwe	Zimbabwe	 Screen Cleaning Cloth	1	1.99	1.99



Question 2: What is the max/min and average cost of each item, broken down by country and city

SQL Queries:
WITH cleaned AS
(
	SELECT 
		country,
		CASE -- to clean up the "not available in free data set" and "not set" options
			WHEN city LIKE 'not avail%' THEN country
			WHEN city LIKE '%not se%' THEN country
			ELSE city
		END AS city,
		CASE -- Clean up the product category section
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
avg_calculations AS
( -- to calculate my max and minimums and averages 
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
SELECT DISTINCT
	*
FROM
	avg_calculations
ORDER BY
	categories

Answer:
We can see a big variation on the price of the products ordered in each category depending on what the country we're looking at is.


Question 3: what does the spread of item cost look like according to different countries.

SQL Queries:
WITH price_ranked AS -- rank my prices to find the highest and lowest with the countries
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
	SELECT -- remove all products and duplicates that have the same price across all countries and limit it to my biggest and smallest values.
		DISTINCT *
	FROM
		price_ranked
	WHERE 
		NOT (low_high = high_low AND low_high = 1) AND (high_low = 1 OR low_high = 1)
)
SELECT --give me an easy to read list of the price it is in different countries and who is the cheapest and who is most expensive
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
	
Answer:
running through the results gives us an overwhelming trend towards United States in the cheapest location for products.


Question 4: how many customers do we have using the website three or more times?

SQL Queries:
WITH visit_list AS
(
	SELECT DISTINCT -- gives me a list of distinct visitnumbers and there visitorid's
		visitnumber,
		fullvisitorid
	FROM
		analytics
	WHERE 
		visitnumber >= 3 -- change this to see how many unique visitors we have had more than 1
)
SELECT -- count the number of unique visitors
	count(*)
FROM
	visit_list

Answer:
at least 3 visits = 21425 times
at least 100 visits = 208 times
at least 200 visits = 63 times
at least 300 visits = 33 times

Question 5: Lets compare the amount(value and number of) of purchases from single visit users vs return users

SQL Queries: 
SELECT DISTINCT
	fullvisitorid,
	SUM(totaltransactionrevenue) OVER ()/1000000
FROM
	all_sessions
WHERE 
	transactions = 1 AND fullvisitorid NOT IN 
	(
		SELECT DISTINCT
			fullvisitorid
		FROM 
			analytics
		WHERE 
			visitnumber > 1
	)

Answer:
	we have 59 purchases with a value of $10,784.86 for single visit clients and only 21 with a value of $3496.45 for return clients
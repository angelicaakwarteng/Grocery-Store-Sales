# Grocery-Store-Sales
This is a SQL project which focuses on helping FoodYum Grocery store to keep stocking products in all categories that cover a range of prices to ensure they have stock for a broad range of customers.


## Data

The data is available in the table `products`.

The dataset contains records of customers for their last full year of the loyalty program.

| Column Name | Criteria                                                |
|-------------|---------------------------------------------------------|
|product_id | Nominal. The unique identifier of the product. </br>Missing values are not possible due to the database structure.|
| product_type | Nominal. The product category type of the product, one of 5 values (Produce, Meat, Dairy, Bakery, Snacks). </br>Missing values should be replaced with “Unknown”. |
| brand | Nominal. The brand of the product. One of 7 possible values. </br>Missing values should be replaced with “Unknown”. |
| weight | Continuous. The weight of the product in grams. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median weight. |
| price | Continuous. The price the product is sold at, in US dollars. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median price. |
| average_units_sold | Discrete. The average number of units sold each month. This can be any positive integer value. </br>Missing values should be replaced with 0. |
| year_added | Nominal. The year the product was first added to FoodYum stock.</br>Missing values should be replaced with 2022. |
| stock_location | Nominal. The location that stock originates. This can be one of four warehouse locations, A, B, C or D </br>Missing values should be replaced with “Unknown”. |

# Task 1
Last year (2022) there was a bug in the product system. For some products that were added in that year, the `year_added` value was not set in the data. As the year the product was added may have an impact on the price of the product, this is important information to have. 

Determine how many products have the `year_added` value missing. Your output should be a single column, `missing_year`, with a single row giving the number of missing values to help FoodYum's manager understand clearly.

# Solution
SELECT COUNT(*) AS missing_year FROM products
WHERE year_added IS NULL;


# Task 2
Given what you know about the year added data, FoodYum wants you to make sure all of the data is clean before you start your analysis. The table below shows what the data should look like. 

Your second task is to ensure the product data matches the description provided. They do not want you to update the original table.  

| Column Name | Criteria                                                |
|-------------|---------------------------------------------------------|
|product_id | Nominal. The unique identifier of the product. </br>Missing values are not possible due to the database structure.|
| product_type | Nominal. The product category type of the product, one of 5 values (Produce, Meat, Dairy, Bakery, Snacks). </br>Missing values should be replaced with “Unknown”. |
| brand | Nominal. The brand of the product. One of 7 possible values. </br>Missing values should be replaced with “Unknown”. |
| weight | Continuous. The weight of the product in grams. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median weight. |
| price | Continuous. The price the product is sold at, in US dollars. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median price. |
| average_units_sold | Discrete. The average number of units sold each month. This can be any positive integer value. </br>Missing values should be replaced with 0. |
| year_added | Nominal. The year the product was first added to FoodYum stock.</br>Missing values should be replaced with last year (2022). |
| stock_location | Nominal. The location that stock originates. This can be one of four warehouse locations, A, B, C or D </br>Missing values should be replaced with “Unknown”. |

# Solution
SELECT * FROM
(WITH fill_missing AS(
	SELECT pt.product_id,
			COALESCE(pt.product_type, 'Unknown') AS product_type,
			COALESCE(pt.average_units_sold, '0') AS average_units_sold,
			COALESCE(pt.year_added, '2022') AS year_added,
			COALESCE(pt.stock_location, 'Unknown') AS stock_location,
			REPLACE(pt.brand, '-', 'Unknown') AS brand,
			CAST(TRIM(weight, 'grams') AS DECIMAL) AS weight,
			ROUND(CAST(price AS DECIMAL), 2) AS price,
 			UPPER(pt.stock_location) AS final_stock_location
	FROM products pt
	GROUP BY pt.product_id, 
	pt.product_type, 
	pt.average_units_sold, pt.year_added, pt.stock_location, pt.brand, pt.weight, pt.price 
),
median_values AS(
	SELECT product_id,
		PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY weight) AS median_weight,
		PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY price) AS median_price
	FROM fill_missing
	GROUP BY product_id
)
SELECT fm.product_id, fm.product_type, fm.brand,
			COALESCE(fm.weight, median_weight) AS weight,
			COALESCE(fm.price, median_price) AS price,
 			fm.average_units_sold,
 			fm.year_added,
 		    fm.final_stock_location AS stock_location
	FROM fill_missing fm
	LEFT JOIN median_values mv ON fm.product_id = mv.product_id) AS clean_data;


 # Task 3
To find out how the range varies for each product type, the manager asked you to help them determine the minimum and maximum values for each product type.   

 # Solution
 SELECT * FROM (SELECT product_type, 
		min(price) AS min_price,
		max(price) AS max_price
		FROM products
		GROUP BY product_type) AS min_max_product;


# Task 4
The team wants to look in more detail at meat and dairy products where the average units sold was greater than ten. 
They specifically requested to view the `product_id`, `price` and `average_units_sold` of the rows of interest to them.

# Solution
SELECT product_id, 
		price,
		average_units_sold
FROM (
	SELECT *
	FROM products
	WHERE product_type IN ('Meat','Dairy') AND average_units_sold > 10)
AS average_price_product;

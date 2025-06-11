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

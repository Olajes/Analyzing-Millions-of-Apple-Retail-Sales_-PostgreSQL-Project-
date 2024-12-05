# Analyzing Millions of Apple Retail Sales: PostgreSQL Project  

## Project Overview  
This project showcases advanced SQL querying techniques through the analysis of over 1 million rows of Apple retail sales data. The dataset includes information about products, stores, sales transactions, and warranty claims across various Apple retail locations globally.  
By tackling a variety of questions, from basic to complex, this project demonstrates how to extract valuable insights from large datasets using sophisticated SQL queries.

### Key Highlights:
- **Dataset**: Over 1 million rows of sales data.
- **Period Covered**: Multi-year data allowing for long-term trend analysis.
- **Geographical Coverage**: Sales data from Apple stores across various countries.
- **Database**: PostgreSQL

---
## Entity Relationship Diagram (ERD)
![image alt](https://github.com/Olajes/Analyzing-Millions-of-Apple-Retail-Sales_-PostgreSQL-Project-/blob/df1a9101d76d452f501ddff3a0e57d220c81c69d/ERD%20DIAGRAM%20FOR%20APPLE_SALES_PROJECT.JPG)


---
## Dataset Schema  

### 1. **Stores**  
Contains information about Apple retail stores.

| Column Name  | Data Type             | Description                           |
|--------------|-----------------------|---------------------------------------|
| `store_id`   | CHARACTER VARYING (5)  | Unique identifier for each store      |
| `store_name` | CHARACTER VARYING (30) | Name of the store                     |
| `city`       | CHARACTER VARYING (25) | City where the store is located       |
| `country`    | CHARACTER VARYING (25) | Country of the store                  |

---

### 2. **Category**  
Holds product category information.

| Column Name  | Data Type             | Description                           |
|--------------|-----------------------|---------------------------------------|
| `category_id`| CHARACTER VARYING (10) | Unique identifier for each category   |
| `category_name`| CHARACTER VARYING (10)| Name of the category                  |

---

### 3. **Products**  
Details about Apple products.

| Column Name  | Data Type             | Description                           |
|--------------|-----------------------|---------------------------------------|
| `product_id` | CHARACTER VARYING (10) | Unique identifier for each product    |
| `product_name`| CHARACTER VARYING (35)| Name of the product                   |
| `category_id`| CHARACTER VARYING (10) | References the `category` table       |
| `launch_date`| DATE                   | Date when the product was launched    |
| `price`      | DOUBLE PRECISION       | Price of the product                  |

---

### 4. **Sales**  
Stores sales transactions.

| Column Name  | Data Type             | Description                           |
|--------------|-----------------------|---------------------------------------|
| `sale_id`    | CHARACTER VARYING (15) | Unique identifier for each sale       |
| `sale_date`  | DATE                   | Date of the sale                      |
| `store_id`   | CHARACTER VARYING (10) | References the `store` table          |
| `product_id` | CHARACTER VARYING (10) | References the `product` table        |
| `quantity`   | INT                    | Number of units sold                  |

---

### 5. **Warranty**  
Contains information about warranty claims.

| Column Name  | Data Type             | Description                           |
|--------------|-----------------------|---------------------------------------|
| `claim_id`   | CHARACTER VARYING (10) | Unique identifier for each warranty claim |
| `claim_date` | DATE                   | Date the claim was made               |
| `sale_id`    | CHARACTER VARYING (15) | References the `sales` table          |
| `repair_status`| CHARACTER VARYING (15)| Status of the warranty claim (e.g., Paid Repaired, Warranty Void) |

---

## Objectives  

### Easy to Medium (10 Questions)  
1. Find the number of stores in each country.  
```sql
  SELECT country,count(store_id) AS total_stores
  FROM stores
  GROUP BY country
  ORDER BY total_stores DESC;
```

2. Calculate the total number of units sold by each store.  
```sql
  SELECT * FROM stores;
  SELECT * FROM sales;
  
  
  SELECT str.store_id AS store,
  		str.store_name,
  		SUM(sal.quantity) AS total_sales
  FROM sales sal
  JOIN stores str
  ON sal.store_id = str.store_id
  GROUP BY store
  ORDER BY store,total_sales DESC;

```
3. Identify how many sales occurred in December 2023.  
```sql
  SELECT COUNT(sale_id)
  FROM sales
  WHERE TO_CHAR(sale_date,'MM-YYYY') = '12-2023';
```
4. Determine how many stores have never had a warranty claim filed.  
```sql
  SELECT COUNT(*) 
  FROM stores
  WHERE store_id NOT IN (
  						SELECT 
  							distinct(store_id) 
  						FROM sales sl
  						RIGHT JOIN warranty wt
  						ON sl.sale_id = wt.sale_id);
```
5. Calculate the percentage of warranty claims marked as "Warranty Void".  
```sql
  SELECT 
  	ROUND(COUNT(claim_id)/ (SELECT COUNT(*) FROM warranty)::numeric * 100,2) AS warranty_vold_pct
  FROM warranty
  WHERE repair_status = 'Warranty Void';
```
6. Identify which store had the highest total units sold in the last year.  
```sql
  SELECT 
  	store_id, SUM(quantity) AS total_unit_sold
  FROM sales
  WHERE sale_date >= (CURRENT_DATE - INTERVAL '1 year')
  GROUP BY store_id
  ORDER BY total_unit_sold DESC
  -- LIMIT 1;

```
7. Count the number of unique products sold in the last year.  
```sql
  SELECT 
  	COUNT(DISTINCT(product_id))
  FROM sales
  WHERE sale_date >= (CURRENT_DATE - INTERVAL '1 year');
```
8. Find the average price of products in each category.  
```sql
  SELECT * FROM category;
  SELECT 
  	p.category_id,
  	c.category_name,
  	AVG(price) AS avg_price
  FROM products p
  JOIN category c
  ON p.category_id = c.category_id
  GROUP BY p.category_id,c.category_name
  ORDER BY avg_price DESC;
```
9. How many warranty claims were filed in 2020?  
```sql
  SELECT 
  	COUNT(*) 
  FROM warranty
  WHERE EXTRACT(YEAR FROM claim_date) = 2020;
```
10. For each store, identify the best-selling day based on the highest quantity sold.  
```sql
  SELECT *
  FROM
  	(SELECT 
  		sl.store_id,
  		TO_CHAR(sl.sale_date,'Day') as day_name,
  		SUM(sl.quantity),
  		RANK() OVER(PARTITION BY sl.store_id ORDER BY SUM(sl.quantity) DESC) AS rank_st
  	FROM sales sl
  	JOIN stores st
  	ON sl.store_id = st.store_id
  	GROUP BY 1,2
  	ORDER by 1,3 DESC) AS rank_
  WHERE rank_st = 1
```
---

### Medium to Hard (5 Questions)  
1. Identify the least selling product in each country for each year based on total units sold.  
```sql
  SELECT * FROM products;
  SELECT * FROM stores;
  
  
  
  WITH product_rank as
  (SELECT 
  	st.country,
  	p.product_name,
  	SUM(s.quantity) total_qty_sold,
  	RANK() OVER (PARTITION BY st.country ORDER BY SUM(s.quantity)) as rank
  FROM sales s
  JOIN stores st
  	ON s.store_id = st.store_id
  JOIN products p
  	ON s.product_id = p.product_id
  GROUP BY 1, 2)
  
  SELECT *
  FROM product_rank
  WHERE rank = 1;
```
2. Calculate how many warranty claims were filed within 180 days of a product sale.  
```sql
  SELECT 
  	COUNT(*)
  	/*w.*,
  	s.sale_date,
  	(w.claim_date - sale_date) as date_diff */
  FROM warranty w
  LEFT JOIN sales s
  	ON s.sale_id = w.sale_id
  WHERE w.claim_date - sale_date <= 180
```
3. Determine how many warranty claims were filed for products launched in the last two years.  
```sql
  SELECT 
  	p.product_name,
  	COUNT(w.claim_id),
  	COUNT(s.sale_id)
  FROM warranty as w
  RIGHT JOIN sales s
  	ON s.sale_id = w.sale_id
  JOIN products p
  	ON p.product_id = s.product_id
  WHERE p.launch_date >= CURRENT_DATE - INTERVAL '2 years'
  GROUP BY p.product_name
  HAVING COUNT(w.claim_id) > 0
```
4. List the months in the last three years where sales exceeded 5,000 units in the USA.  
```sql
  SELECT 
  	TO_CHAR(s.sale_date,'MM-YYYY'),
  	SUM(s.quantity) as total_unit_sold
  FROM sales s
  JOIN stores st
  ON s.store_id = st.store_id
  WHERE st.country = 'USA' AND
  		s.sale_date >= CURRENT_DATE - INTERVAL '3 year'
  GROUP BY 1
  HAVING SUM(s.quantity) > 5000;
```
5. Identify the product category with the most warranty claims filed in the last two years.  
```sql
  SELECT 
  	c.category_name,
  	COUNT(w.claim_id)
  FROM warranty w
  LEFT JOIN sales s
  	ON w.sale_id = s.sale_id
  JOIN products p
  	ON p.product_id = s.product_id
  JOIN category c
  	ON c.category_id = p.category_id
  WHERE w.claim_date >= CURRENT_DATE - INTERVAL '2 year'
  GROUP BY 1;
```
---

### Complex (5 Questions)  
1. Determine the percentage chance of receiving warranty claims after each purchase for each country.  
```sql
  SELECT * FROM category;
  SELECT * FROM products;
  SELECT * FROM stores;
  
  SELECT 
  	country,
  	total_unit_sold,
  	total_claim,
  	ROUND(COALESCE(((total_claim::numeric/total_unit_sold::numeric) * 100), 0),3) AS risk
  FROM
  		(SELECT 
  			st.country,
  			SUM(s.quantity) as total_unit_sold,
  			COUNT(w.claim_id) as total_claim
  		FROM sales s 
  		JOIN stores st
  			ON s.store_id = st.store_id
  		LEFT JOIN warranty w
  			ON w.sale_id = s.sale_id
  		GROUP BY 1) t1
  ORDER BY 3 DESC;
  -- LIMIT 10;
```
2. Analyze the year-by-year growth ratio for each store.  
```sql
  WITH yearly_sale_cte as
  	(SELECT 
  		s.store_id,
  		st.store_name,
  		EXTRACT(YEAR FROM sale_date) as year,
  		SUM(s.quantity * p.price) as total_sale
  	FROM sales s
  	JOIN products p
  		ON s.product_id = p.product_id
  	JOIN stores st
  		ON st.store_id = s.store_id
  	GROUP BY 1,2,3
  	ORDER BY 2,3),
  
  growth_ratio as
  	(SELECT 
  		store_name,
  		year,
  		LAG(total_sale) OVER (PARTITION BY store_name ORDER BY year) as last_year_sale,
  		total_sale as current_year_sale
  	FROM yearly_sale_cte)
  
  SELECT 
  	store_name,
  	year,
  	last_year_sale,
  	current_year_sale,
  	ROUND( ((current_year_sale - last_year_sale):: numeric/ (last_year_sale :: numeric) * 100 ), 3) AS year_by_year_growth_ratio
  FROM growth_ratio
  WHERE last_year_sale IS NOT NULL
  		AND YEAR <> EXTRACT(YEAR FROM CURRENT_DATE);
```
3. Calculate the correlation between product price and warranty claims for products sold in the last five years, segmented by price range.  
```sql
  SELECT * FROM stores;
  SELECT * FROM sales;
  
  SELECT 
  	-- p.price,
  	CASE
  		WHEN p.price < 500 THEN 'Less Expenses Products'
  		WHEN p.price BETWEEN 500 AND 1000 THEN 'Mid Range Product'
  		ELSE 'Expensive Product'
  	END as price_segment,
  	COUNT(w.claim_id) AS total_claim
  FROM warranty w
  LEFT JOIN sales s
  	ON	w.sale_id = s.sale_id
  JOIN products p
  	ON p.product_id = s.product_id
  WHERE claim_date >= CURRENT_DATE - INTERVAL '5 year'
  GROUP BY 1;
```
4. Identify the store with the highest percentage of "Paid Repaired" claims relative to total claims filed.  
```sql
  SELECT * FROM category;
  SELECT * FROM products;
  SELECT * FROM stores;
  
  WITH paid_repair_cte as 
  (SELECT 
  	store_id,
  	COUNT(w.claim_id) as paid_repaired
  FROM sales s
  RIGHT JOIN warranty w
  	ON w.sale_id = s.sale_id
  WHERE w.repair_status = 'Paid Repaired'
  GROUP BY 1),
  
  total_repaired_cte as
  (SELECT 
  	store_id,
  	COUNT(w.claim_id) as total_repaired
  FROM sales s
  Right JOIN warranty w
  	ON w.sale_id = s.sale_id
  GROUP BY 1)
  
  SELECT
  	tr.store_id,
  	st.store_name,
  	pr.paid_repaired,
  	tr.total_repaired,
  	ROUND( pr.paid_repaired::numeric/tr.total_repaired::numeric * 100, 2) as pct_paid_repaired
  FROM paid_repair_cte AS pr
  JOIN total_repaired_cte AS tr
  	ON pr.store_id = tr.store_id
  JOIN stores AS st
  	ON tr.store_id = st.store_id
```
5. Write a query to calculate the monthly running total of sales for each store over the past four years and compare trends during this period.  
```sql
  SELECT * FROM category;
  SELECT * FROM products;
  SELECT * FROM stores;
  
  WITH monthly_Sales as 
  (SELECT 
  	store_id,
  	EXTRACT(YEAR FROM sale_date) as year,
  	EXTRACT(MONTH FROM sale_date) AS month,
  	SUM(p.price * s.quantity) as total_revenue
  FROM sales s
  JOIN products p
  	ON s.product_id = p.product_id
  GROUP BY 1,2,3
  ORDER BY 1,2,3)
  
  SELECT
  	store_id,
  	month,
  	year,
  	total_revenue,
  	SUM(total_revenue) OVER (PARTITION BY store_id ORDER BY year,month) AS running_total
  FROM monthly_Sales
```
---

### Bonus Question  
Analyze product sales trends over time, segmented into key periods:From launch to 6 months, 6-12 months, 12-18 months and Beyond 18 months  
```sql
  SELECT * FROM category;
  SELECT * FROM products;
  SELECT * FROM stores;
  
  SELECT 
  	p.product_name,
  	CASE
  	WHEN s.sale_date BETWEEN p.launch_date  AND p.launch_date + INTERVAL '6 month' THEN '0-6 month'
  	WHEN s.sale_date BETWEEN p.launch_date + INTERVAL '6 month'  AND p.launch_date + INTERVAL '12 month' - INTERVAL '1 day' THEN '6-12 month'
  	WHEN s.sale_date BETWEEN p.launch_date + INTERVAL '12 month'  AND p.launch_date + INTERVAL '18 month' - INTERVAL '1 day' THEN '12-18 month'
  	ELSE '18+ month'
  	END p_l_c,
  	SUM(s.quantity) total_qty_sale
  FROM sales s
  JOIN products p
  	ON s.product_id = p.product_id
  GROUP BY 1, 2
  ORDER BY 1,3 DESC;
```
---

## Project Focus  
This project primarily focuses on:  
- **Complex Joins and Aggregations**: Performing complex joins and aggregations to extract meaningful insights.  
- **Window Functions**: Using advanced window functions for running totals, growth analysis, and time-based queries.  
- **Data Segmentation**: Analyzing data across different time frames.  
- **Correlation Analysis**: Determining relationships between variables like product price and warranty claims.  
- **Real-World Problem Solving**: Solving business-related questions faced by data analysts.

---

## Conclusion  
By completing this project, you will:  
- Develop advanced SQL querying skills.  
- Improve your ability to handle large datasets.  
- Gain practical experience in solving complex data analysis problems.  

This project is an excellent addition to your portfolio and will demonstrate your expertise in SQL to potential employers.

---



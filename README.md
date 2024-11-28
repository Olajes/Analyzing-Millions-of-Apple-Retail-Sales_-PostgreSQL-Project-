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

![ERD Diagram](C:\Users\user\Desktop\STEVE HTML\PROJECT CATALOG/ERD DIAGRAM FOR APPLE_SALES_PROJECT.JPG)

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
2. Calculate the total number of units sold by each store.  
3. Identify how many sales occurred in December 2023.  
4. Determine how many stores have never had a warranty claim filed.  
5. Calculate the percentage of warranty claims marked as "Warranty Void".  
6. Identify which store had the highest total units sold in the last year.  
7. Count the number of unique products sold in the last year.  
8. Find the average price of products in each category.  
9. How many warranty claims were filed in 2020?  
10. For each store, identify the best-selling day based on the highest quantity sold.  

---

### Medium to Hard (5 Questions)  
1. Identify the least selling product in each country for each year based on total units sold.  
2. Calculate how many warranty claims were filed within 180 days of a product sale.  
3. Determine how many warranty claims were filed for products launched in the last two years.  
4. List the months in the last three years where sales exceeded 5,000 units in the USA.  
5. Identify the product category with the most warranty claims filed in the last two years.  

---

### Complex (5 Questions)  
1. Determine the percentage chance of receiving warranty claims after each purchase for each country.  
2. Analyze the year-by-year growth ratio for each store.  
3. Calculate the correlation between product price and warranty claims for products sold in the last five years, segmented by price range.  
4. Identify the store with the highest percentage of "Paid Repaired" claims relative to total claims filed.  
5. Write a query to calculate the monthly running total of sales for each store over the past four years and compare trends during this period.  

---

### Bonus Question  
Analyze product sales trends over time, segmented into key periods:From launch to 6 months, 6-12 months, 12-18 months and Beyond 18 months  

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

## How to Run the Project  
1. Clone the repository:  
   ```bash
   git clone https://github.com/yourusername/apple-retail-sales-analysis.git

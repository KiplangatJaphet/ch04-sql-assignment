## 🗄️ Complete SQL Assignment: From Basic Queries to Advanced Database Design

A comprehensive guide covering core SQL concepts, advanced techniques, query optimization, and database modeling with practical examples.

---

## Database Schema Overview
Our assignment uses a simple e-commerce database with three main tables:

- **customer_info:** Customer details (id, name, location)
- **products:** Product catalog (id, name, price, customer_id)
- **sales:** Sales transactions (id, total_sales, product_id, customer_id)

---

## Section 1: Core SQL Concepts</u>

## Q1: Write a SQL query to list all customers located in Nairobi. Show only full_name and location.

**Query:**

```
sql
SELECT customer_id, full_name, location
FROM customer_info
WHERE location = 'Nairobi';
```
**Purpose:** Filtering with WHERE clause to find customers in a specific location.

 | full\_name       | location |
  ------------------| -------- |
| Jane Robinson     | Nairobi  |
| Michael Rodriguez | Nairobi  |
| Michael Harris    | Nairobi  |
| Robert Brown      | Nairobi  |
| Olivia Robinson   | Nairobi  |
| Michael Thompson  | Nairobi  |
| James Thompson    | Nairobi  |



---

## Q2: Write a SQL query to display each customer along with the products they purchased. Include full_name, product_name, and price.
**Query:**

```
sql
SELECT c.customer_id, c.full_name, p.product_name, p.price
FROM customer_info c
JOIN products p ON c.customer_id = p.customer_id;
```
**Purpose:** Join to connect customers with their products.

| customer\_id | full\_name      | product\_name | price   |
| ------------ | --------------- | ------------- | ------- |
| 18           | Michael Harris  | Desk Chair    | 1548.44 |
| 17           | Joseph Martinez | Smartphone    | 27.36   |
| 40           | Thomas Thomas   | Headphones    | 1984.17 |
| 58           | John Garcia     | Monitor       | 1342.11 |
| 77           | Isabella White  | Mouse         | 204.04  |
| 81           | Robert Anderson | Microphone    | 219.27  |
| 70           | Joseph Doe      | Hard Drive    | 24.48   |

---


## Q3: Write a SQL query to find the total sales amount for each customer. Display full_name and the total amount spent, sorted in descending order.
**Query:**

```
sql
select ci.customer_id, ci.full_name, SUM(s.total_sales) as total_amount  
from customer_info ci 
join sales s on ci.customer_id = s.customer_id 
group by ci.customer_id, ci.full_name
order by total_amount desc; 
```
**Purpose:** Uses SUM() with GROUP BY to calculate total amount spent per customer.

| customer\_id | full\_name      | total\_amount |
| ------------ | --------------- | ------------- |
| 40           | Thomas Thomas   | 58,669.20     |
| 3            | Jane Robinson   | 53,281.58     |
| 78           | Michael Brown   | 49,229.30     |
| 85           | Amelia White    | 47,714.29     |
| 66           | James Lewis     | 37,832.48     |
| 56           | Mia Lee         | 36,837.70     |
| 17           | Joseph Martinez | 33,605.50     |
 



---


## Q4: Write a SQL query to find all customers who have purchased products priced above 10,000.
**Query:**

```
sql
SELECT DISTINCT c.customer_id, c.full_name, p.product_name, p.price
FROM customer_info c
JOIN products p ON c.customer_id = p.customer_id
WHERE p.price > 10000;
```
**Purpose:** Using DISTINCT to avoid duplicates, filtering with JOIN conditions and WHERE condition to filter products above 10,000.

| customer\_id | full\_name   | product\_name | price   |
| ------------ | ------------ | ------------- | ------- |
| ...              | ...           | ...          | ...


---


## Q5: Write a SQL query to find the top 3 customers with the highest total sales.
**Query:**

```
sql
select ci.customer_id, ci.full_name, sum(total_sales) as total_amount 
from sales s 
join customer_info ci on s.customer_id = ci.customer_id 
group by ci.customer_id, ci.full_name 
order by total_amount desc 
limit 3;
 
**Purpose:** Using ORDER BY and LIMIT to get top 3 results after sorting.

| customer\_id | full\_name    | total\_amount |
| ------------ | ------------- | ------------- |
| 40           | Thomas Thomas | 58,669.20     |
| 3            | Jane Robinson | 53,281.58     |
| 78           | Michael Brown | 49,229.30     |



---


## Section 2: Advanced SQL Techniques</u>

## Q6: Write a CTE that calculates the average sales per customer and then returns customers whose total sales are above that average.

**Query:**

```
sql
WITH customer_totals AS (
    SELECT 
        c.customer_id,
        c.full_name,
        SUM(s.total_sales) AS total_sales
    FROM customer_info c
    JOIN sales s ON c.customer_id = s.customer_id
    GROUP BY c.customer_id, c.full_name
),
average_sales AS (
    SELECT AVG(total_sales) AS avg_sales
    FROM customer_totals
)
SELECT 
    ct.customer_id,
    ct.full_name,
    ct.total_sales
FROM customer_totals ct
CROSS JOIN average_sales a
WHERE ct.total_sales > a.avg_sales
ORDER BY ct.total_sales DESC
Limit 3;
```
**Purpose:** A CTE calculates each customer’s total and compares it against the average.

| customer\_id | full\_name    | total\_sales |
| ------------ | ------------- | ------------ |
| 40           | Thomas Thomas | 58,669.20    |
| 3            | Jane Robinson | 53,281.58    |
| 78           | Michael Brown | 49,229.30    |
| 85           | Amelia White  | 47,714.29    |
| 66           | James Lewis   | 37,832.48    |




---


## Q7: Write a Window Function query that ranks products by their total sales in descending order. Display product_name, total_sales, and rank.

**Query:**

```
select p.product_name, 
sum(s.total_sales) as total_sales,
rank ()over (order by sum(s.total_sales) desc) as sales_rank
from products p 
join sales s on p.product_id = s.product_id
group by p.product_name;
```
**Purpose:** Ranks products by total sales using a window function RANK().

| product\_name | total\_sales | sales\_rank |
| ------------- | ------------ | ----------- |
| Microphone    | 110,515.77   | 1           |
| Desk Chair    | 102,157.46   | 2           |
| Monitor       | 101,817.33   | 3           |
| Camera        | 98,305.35    | 4           |
| Drone         | 97,830.93    | 5           |
| Hard Drive    | 97,668.82    | 6           |
| Tablet        | 79,636.73    | 7           |





---


## Q8: Create a View called high_value_customers that lists all customers with total sales greater than 15,000.
**Query:**

```
**sql

create view high_value_customers as 
select ci.full_name, ci.customer_id, sum(s.total_sales) as total_sales 
from customer_info ci 
join sales s on ci.customer_id = s.customer_id 
group by ci.customer_id, ci.full_name 
having sum(s.total_sales) > 15000;

select * from high_value_customers;
```
**Purpose:** A VIEW for reusability, listing customers above 15,000 in total sales.

| full\_name      | customer\_id | total\_sales |
| --------------- | ------------ | ------------ |
| Mia Lee         | 56           | 36,837.70    |
| Amelia Martinez | 91           | 28,350.90    |
| John Garcia     | 58           | 32,605.14    |
| Sophia Thomas   | 8            | 22,945.90    |
| Amelia White    | 87           | 24,664.40    |
| Olivia Doe      | 54           | 23,555.90    |
| John Rodriguez  | 29           | 19,249.67    |



---


## Q9: Create a Stored Procedure that accepts a location as input and returns all customers and their total spending from that location.
**Query:**

```
**sql
CREATE PROCEDURE GetCustomersByLocation(IN p_location VARCHAR(100))
BEGIN
    SELECT 
        c.customer_id,
        c.full_name,
        c.location,
        SUM(s.total_sales) AS total_spending
    FROM customer_info c
    JOIN sales s ON s.customer_id = c.customer_id
    WHERE c.location = p_location
    GROUP BY c.customer_id, c.full_name, c.location
    ORDER BY total_spending DESC;
END;


CALL GetCustomersByLocation('Nyeri');

```
**Purpose:** Stored procedures for reusable business logic with parameters.

| customer\_id | full\_name     | location | total\_spending   |
| ------------ | -------------- | -------- | ----------------- |
| 40           | Thomas Thomas  | Nyeri    | 58,669.1997375488 |
| 56           | Mia Lee        | Nyeri    | 36,837.7011108398 |
| 42           | James Thomas   | Nyeri    | 27,602.1904296875 |
| 70           | Joseph Doe     | Nyeri    | 25,582.1798439026 |
| 21           | Robert White   | Nyeri    | 25,344.8100585938 |
| 54           | Olivia Doe     | Nyeri    | 23,555.8999023438 |
| 34           | Amelia Jackson | Nyeri    | 22,290.75         |
| 29           | John Rodriguez | Nyeri    | 19,249.669921875  |
| 45           | Sophia Lee     | Nyeri    | 16,602.1291566016 |

*** NOTE: You have to use MYSQL to run the procedure.

---


## Q10: Write a recursive query to display all sales transactions in order by sales_id, along with a running total of sales.

**Query:**

```
sql
WITH RECURSIVE Sales_CTE AS (
    -- Base case: first sales record
    SELECT 
        s.sales_id,
        s.total_sales,
        s.total_sales AS running_total
    FROM sales s
    WHERE s.sales_id = (SELECT MIN(sales_id) FROM sales)

    UNION ALL

    -- Recursive case: add next sales record
    SELECT 
        s.sales_id,
        s.total_sales,
        c.running_total + s.total_sales AS running_total
    FROM sales s
    JOIN Sales_CTE c ON s.sales_id = c.sales_id + 1
)
SELECT * FROM Sales_CTE;
```
**Purpose:** Recursive CTEs for hierarchical data and cumulative calculations.

| sales\_id | total\_sales | running\_total |
| --------- | ------------ | -------------- |
| 1         | 5,249.91     | 5,249.91       |
| 2         | 5,368.44     | 10,618.35      |
| 3         | 1,413.70     | 12,032.05      |
| 4         | 5,696.52     | 17,728.57      |
| 5         | 5,695.56     | 23,424.13      |
| 6         | 7,736.48     | 31,160.61      |
| 7         | 2,542.72     | 33,703.33      |



---


## Section 3: Query Optimization & Performance</u>

## Q11: The following query is running slowly:
SELECT * FROM sales WHERE total_sales > 5000;
Explain two changes you would make to improve its performance and then write the optimized SQL query.

**Problem:** This query runs slowly:
How to improve performance: 
1. ** Create an Index**
Add an index on total_sales so filtering is faster.

~~sql running slow
SELECT * FROM sales WHERE total_sales > 5000;
```
**Solutions:**

create index idx_sales_total on sales (total_sales);

 ```


2. **Select only needed columns:**
 
sql
select sales_id, product_id, customer_id, total_sales 
from sales 
where total_sales > 5000; 
```
**Why it works:**

- Index allows fast lookups instead of full table scans by avoiding SELECT *
- Selecting fewer columns reduces I/O and memory usage

---


## Q12: Create an index on a column that would improve queries filtering by customer location, then write a query to test the improvement.
**Create Index:**

```
sql
CREATE INDEX idx_customer_location ON customer_info(location);
```
**Test the query improvement:

```
sql
explain select customer_id, full_name, location 
from customer_info 
where location = 'Nairobi';

**Purpose:** Index on location column to speed up filtering by city and EXPLAIN shows if the query uses the index efficiently.

---


## Section 4: Database Design & Modeling

## Q13: Redesign the given schema into 3rd Normal Form (3NF) and provide the new CREATE TABLE statements.
~~ Redesigning schema
--customer_info 
  -customer_id (pk) 
  - full_name 
  - location 
 
-- products 
  - product_id(pk) 
  - product_name 
  
-- sales 
  - sales_id(pk) 
  - total_sales
  - customer_id (fk) 
  -product_id (fk)
  
-- CREATE TABLE STATEMENTS
**sql~
create table customer_info(
    customer_id serial primary key,
    full_name varchar(120),
    location varchar(90)
);

**sql~
create table products(
    product_id serial primary key,
    product_name varchar(120),
    price float
);
 **sql~
create table sales(
    sales_id serial primary key,
    customer_id int references customer_info(customer_id),
    product_id INT references products(product_id),
    total_sales float
);
```
---


## Q14: Create a Star Schema design for analyzing sales by product and customer location. Include the fact table and dimension tables with their fields.
**Fact Table:**

```
sql~~
create table fact_sales (
    sales_id serial primary key,
    customer_id int references customer_info(customer_id),
    location_id int references location_id(location_id)
    product_id int references product_id(product_id),
    total_sales float
);
```
**Dimension Tables:**

```
Dimension Tables: dim_customer, dim_product, dim_location and fact_sales
~~sql
create table dim_customer (
    customer_id serial primary key,
    full_name varchar(120)
);

**sql
create table dim_location (
    location_id primary key,
    location varchar(90)
);

**sql
create table dim_product (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(120),
    price FLOAT
); 

**sql
create table fact_sales (
    sales_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES dim_customer(customer_id),
    location_id INT REFERENCES dim_location(location_id),
    product_id INT REFERENCES dim_product(product_id),
    total_sales FLOAT
);```
**Star Schema Benefits:**

- Optimized for analytical queries
- Fast aggregations
---


## Q15: Explain a scenario where denormalization would improve performance for reporting queries, and demonstrate the SQL table creation for that denormalized structure.
Scenario
-1. Read-Heavy Applications: When you read data much more than you write it
-2. Performance Critical: When query speed is more important than storage space
-3. Reporting Systems: When complex analytics require fast data access
-4. Data Warehousing: For analytical workloads

**Denormalized Table:**

-- Example 
-- SQL table creation dernomalized by combining customer_info, product and sales into one reporting table. 

~ sql
create table denorm_sales_report (
    sales_id SERIAL PRIMARY KEY,
    full_name VARCHAR(120),
    location VARCHAR(90),
    product_name VARCHAR(120),
    price FLOAT,
    total_sales FLOAT
);
```


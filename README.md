# Sunrise Supermarket — SQL/PLSQL Assignment One

## Student Information

| Field               | Details                          |
| ------------------- | -------------------------------- |
| **Student Name**    | BENIMANA XAVIER                  |
| **Student ID**      | 20251SEN320                      |
| **Assignment**      | PLSQL Assignment One             |
| **Business**        | Sunrise Supermarket              |
| **DBMS Used**       | Oracle Database                  |
| **Repository Name** | `assignment_1_BENIMANA_Xavier-20251SEN320` |

---

# 1. Project Overview

This project is an SQL/PLSQL database assignment based on a fictional business called **Sunrise Supermarket**.

Sunrise Supermarket sells different products to customers. Customers place orders, and each order can contain one or more products. The management of Sunrise Supermarket needs useful information about customers, products, orders, spending, and sales trends.

The purpose of this project is to create and populate the database and then use SQL queries to analyze the business data.

The assignment demonstrates the use of:

* INNER JOIN
* LEFT JOIN
* Common Table Expressions (CTEs)
* Aggregate functions
* Window functions
* `RANK()`
* `ROW_NUMBER()`
* `LAG()`
* Running totals
* Date calculations
* Subqueries
* `GROUP BY`
* `SUM()`
* `AVG()`
* Foreign keys
* Primary keys

---

# 2. Business Scenario

Sunrise Supermarket has customers from different cities who purchase different products.

The database stores four main types of information:

1. **Customers** — information about supermarket customers.
2. **Products** — products sold by the supermarket.
3. **Orders** — orders placed by customers.
4. **Order Items** — individual products contained in each order.

Management wants to answer questions such as:

* Which customer placed each order?
* What products were purchased?
* Which customers have not placed any orders?
* Which customers spend more than the average customer?
* Who are the highest-spending customers?
* What is the order sequence for each customer?
* How much revenue has accumulated over time?
* How many days pass between a customer's orders?

SQL JOINs, CTEs, and window functions are used to answer these questions.

---

# 3. Database Structure

The database contains four tables:

```text
CUSTOMERS
    |
    | customer_id
    |
    v
ORDERS
    |
    | order_id
    |
    v
ORDER_ITEMS
    |
    | product_id
    |
    v
PRODUCTS
```

### Tables

### Customers

Stores information about customers.

| Column          | Description                |
| --------------- | -------------------------- |
| `customer_id`   | Unique customer identifier |
| `customer_name` | Customer's name            |
| `email`         | Customer email             |
| `city`          | Customer's city            |

### Products

Stores information about products.

| Column         | Description               |
| -------------- | ------------------------- |
| `product_id`   | Unique product identifier |
| `product_name` | Name of product           |
| `category`     | Product category          |
| `price`        | Product selling price     |

### Orders

Stores information about customer orders.

| Column        | Description                   |
| ------------- | ----------------------------- |
| `order_id`    | Unique order identifier       |
| `customer_id` | Customer who placed the order |
| `order_date`  | Date the order was placed     |

### Order Items

Stores products contained in each order.

| Column          | Description                  |
| --------------- | ---------------------------- |
| `order_item_id` | Unique order item identifier |
| `order_id`      | Related order                |
| `product_id`    | Related product              |
| `quantity`      | Quantity purchased           |

---

# 4. Database Creation

The following SQL statements create the four tables.

```sql
CREATE TABLE customers (
    customer_id NUMBER PRIMARY KEY,
    customer_name VARCHAR2(100),
    email VARCHAR2(100),
    city VARCHAR2(50)
);

CREATE TABLE products (
    product_id NUMBER PRIMARY KEY,
    product_name VARCHAR2(100),
    category VARCHAR2(50),
    price NUMBER(10,2)
);

CREATE TABLE orders (
    order_id NUMBER PRIMARY KEY,
    customer_id NUMBER REFERENCES customers(customer_id),
    order_date DATE
);

CREATE TABLE order_items (
    order_item_id NUMBER PRIMARY KEY,
    order_id NUMBER REFERENCES orders(order_id),
    product_id NUMBER REFERENCES products(product_id),
    quantity NUMBER
);
```

---

# 5. Sample Data

The assignment requires at least:

* 5 customers
* 8 products
* 3 or more categories
* 15 orders
* 25 order items
* Multiple order dates

The following data satisfies those requirements.

---

## 5.1 Insert Customers

```sql
INSERT INTO customers VALUES
(1, 'Jean Claude', 'jean.claude@email.com', 'Kigali');

INSERT INTO customers VALUES
(2, 'Alice Uwase', 'alice.uwase@email.com', 'Huye');

INSERT INTO customers VALUES
(3, 'Eric Ndayisenga', 'eric.ndayisenga@email.com', 'Musanze');

INSERT INTO customers VALUES
(4, 'Diane Mukamana', 'diane.mukamana@email.com', 'Rubavu');

INSERT INTO customers VALUES
(5, 'Patrick Habimana', 'patrick.habimana@email.com', 'Kigali');

INSERT INTO customers VALUES
(6, 'Grace Ingabire', 'grace.ingabire@email.com', 'Kigali');
```

Customer 6 is intentionally included so that the LEFT JOIN can demonstrate a customer with no orders.

---

# 5.2 Insert Products

The products belong to more than three categories.

```sql
INSERT INTO products VALUES
(1, 'Rice 5kg', 'Food', 6500);

INSERT INTO products VALUES
(2, 'Sugar 2kg', 'Food', 3000);

INSERT INTO products VALUES
(3, 'Cooking Oil 1L', 'Food', 4500);

INSERT INTO products VALUES
(4, 'Milk 1L', 'Beverages', 1500);

INSERT INTO products VALUES
(5, 'Bread', 'Bakery', 1200);

INSERT INTO products VALUES
(6, 'Soap', 'Personal Care', 1000);

INSERT INTO products VALUES
(7, 'Toothpaste', 'Personal Care', 2500);

INSERT INTO products VALUES
(8, 'Juice 1L', 'Beverages', 2500);

COMMIT;
```

---

# 5.3 Insert Orders

There are 15 orders distributed across multiple dates.

```sql
INSERT INTO orders VALUES
(1, 1, DATE '2026-09-01');

INSERT INTO orders VALUES
(2, 2, DATE '2026-09-02');

INSERT INTO orders VALUES
(3, 3, DATE '2026-09-03');

INSERT INTO orders VALUES
(4, 1, DATE '2026-09-05');

INSERT INTO orders VALUES
(5, 4, DATE '2026-09-06');

INSERT INTO orders VALUES
(6, 5, DATE '2026-09-07');

INSERT INTO orders VALUES
(7, 2, DATE '2026-09-09');

INSERT INTO orders VALUES
(8, 3, DATE '2026-09-10');

INSERT INTO orders VALUES
(9, 1, DATE '2026-09-12');

INSERT INTO orders VALUES
(10, 4, DATE '2026-09-13');

INSERT INTO orders VALUES
(11, 5, DATE '2026-09-14');

INSERT INTO orders VALUES
(12, 2, DATE '2026-09-16');

INSERT INTO orders VALUES
(13, 3, DATE '2026-09-18');

INSERT INTO orders VALUES
(14, 5, DATE '2026-09-19');

INSERT INTO orders VALUES
(15, 1, DATE '2026-09-20');

COMMIT;
```

---

# 5.4 Insert Order Items

There are more than 25 order items.

```sql
INSERT INTO order_items VALUES (1, 1, 1, 2);
INSERT INTO order_items VALUES (2, 1, 4, 3);

INSERT INTO order_items VALUES (3, 2, 2, 2);
INSERT INTO order_items VALUES (4, 2, 5, 3);

INSERT INTO order_items VALUES (5, 3, 3, 2);
INSERT INTO order_items VALUES (6, 3, 6, 4);

INSERT INTO order_items VALUES (7, 4, 1, 1);
INSERT INTO order_items VALUES (8, 4, 7, 2);

INSERT INTO order_items VALUES (9, 5, 8, 2);
INSERT INTO order_items VALUES (10, 5, 5, 4);

INSERT INTO order_items VALUES (11, 6, 3, 3);
INSERT INTO order_items VALUES (12, 6, 6, 2);

INSERT INTO order_items VALUES (13, 7, 1, 2);
INSERT INTO order_items VALUES (14, 7, 2, 3);

INSERT INTO order_items VALUES (15, 8, 4, 5);
INSERT INTO order_items VALUES (16, 8, 8, 2);

INSERT INTO order_items VALUES (17, 9, 3, 2);
INSERT INTO order_items VALUES (18, 9, 7, 3);

INSERT INTO order_items VALUES (19, 10, 1, 1);
INSERT INTO order_items VALUES (20, 10, 5, 5);

INSERT INTO order_items VALUES (21, 11, 2, 4);
INSERT INTO order_items VALUES (22, 11, 6, 3);

INSERT INTO order_items VALUES (23, 12, 1, 3);
INSERT INTO order_items VALUES (24, 12, 4, 4);

INSERT INTO order_items VALUES (25, 13, 3, 2);
INSERT INTO order_items VALUES (26, 13, 8, 3);

INSERT INTO order_items VALUES (27, 14, 7, 2);
INSERT INTO order_items VALUES (28, 14, 5, 4);

INSERT INTO order_items VALUES (29, 15, 1, 2);
INSERT INTO order_items VALUES (30, 15, 3, 2);

COMMIT;
```

---

# 6. Verification of Inserted Data

The following queries can be used to confirm that the required amount of data has been inserted.

## Count Customers

```sql
SELECT COUNT(*) AS total_customers
FROM customers;
```

Expected result:

```text
TOTAL_CUSTOMERS
---------------
6
```

## Count Products

```sql
SELECT COUNT(*) AS total_products
FROM products;
```

Expected result:

```text
TOTAL_PRODUCTS
--------------
8
```

## Count Orders

```sql
SELECT COUNT(*) AS total_orders
FROM orders;
```

Expected result:

```text
TOTAL_ORDERS
------------
15
```

## Count Order Items

```sql
SELECT COUNT(*) AS total_order_items
FROM order_items;
```

Expected result:

```text
TOTAL_ORDER_ITEMS
-----------------
30
```

---

# 7. JOIN Queries

JOINs are used to combine information stored in different tables.

---

## JOIN Query 1 — Orders and Customers

### Requirement

List every order with:

* Customer name
* Customer city
* Order date

This uses an **INNER JOIN** between `orders` and `customers`.

### SQL

```sql
SELECT
    o.order_id,
    c.customer_name,
    c.city,
    o.order_date
FROM orders o
INNER JOIN customers c
    ON o.customer_id = c.customer_id
ORDER BY o.order_date;
```

### Explanation

The `orders` table contains the `customer_id`, but it does not contain the customer's name or city.

The `INNER JOIN` connects the two tables using:

```sql
o.customer_id = c.customer_id
```

Only orders that have a matching customer are returned.

### Expected Result

| Order ID | Customer        | City    | Order Date  |
| -------: | --------------- | ------- | ----------- |
|        1 | Jean Claude     | Kigali  | 01-SEP-2026 |
|        2 | Alice Uwase     | Huye    | 02-SEP-2026 |
|        3 | Eric Ndayisenga | Musanze | 03-SEP-2026 |
|        4 | Jean Claude     | Kigali  | 05-SEP-2026 |
|      ... | ...             | ...     | ...         |
|       15 | Jean Claude     | Kigali  | 20-SEP-2026 |

### Business Interpretation

This query allows management to see which customers are placing orders and where those customers are located.

This information can help the supermarket understand its customer distribution across different cities.

### Screenshot

Save the SQL Developer result screenshot in:

```text
screenshots/
└── joins/
    └── join_1_orders_customers.png
```

---

# 8. JOIN Query 2 — Order Items and Products

### Requirement

List every order item with:

* Product name
* Category
* Price
* Quantity

### SQL

```sql
SELECT
    oi.order_item_id,
    oi.order_id,
    p.product_name,
    p.category,
    p.price,
    oi.quantity
FROM order_items oi
INNER JOIN products p
    ON oi.product_id = p.product_id
ORDER BY oi.order_id;
```

### Explanation

The `order_items` table contains the product ID and quantity, while the `products` table contains the product name, category, and price.

The JOIN connects both tables using:

```sql
oi.product_id = p.product_id
```

### Business Interpretation

This query helps management understand exactly which products were included in customer orders.

It can be useful for:

* Inventory management
* Product sales analysis
* Category analysis
* Understanding customer purchases

### Screenshot

Save the result as:

```text
screenshots/
└── joins/
    └── join_2_order_items_products.png
```

---

# 9. JOIN Query 3 — Customers and Orders Using LEFT JOIN

### Requirement

List all customers and their orders where they exist, including customers who have no orders.

### SQL

```sql
SELECT
    c.customer_id,
    c.customer_name,
    c.city,
    o.order_id,
    o.order_date
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
ORDER BY c.customer_id, o.order_date;
```

### Explanation

A `LEFT JOIN` returns every customer from the `customers` table.

If a customer has an order, the order information is displayed.

If a customer does not have an order, the order columns contain `NULL`.

Customer **Grace Ingabire** has no order, so this query demonstrates why a LEFT JOIN is useful.

### Business Interpretation

This query helps management identify:

* Customers who have purchased products
* Customers who have never placed an order

Customers without orders could potentially be contacted through appropriate customer engagement activities.

### Screenshot

Save the result as:

```text
screenshots/
└── joins/
    └── join_3_customers_orders_left_join.png
```

---

# 10. CTE Query — Customers Above Average Spending

## Requirement

Calculate each customer's total spending and return customers whose spending is above the average customer spending.

A **Common Table Expression (CTE)** is used to calculate customer totals first.

### SQL

```sql
WITH customer_totals AS (
    SELECT
        c.customer_id,
        c.customer_name,
        SUM(oi.quantity * p.price) AS total_spend
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
    JOIN order_items oi
        ON o.order_id = oi.order_id
    JOIN products p
        ON oi.product_id = p.product_id
    GROUP BY
        c.customer_id,
        c.customer_name
)
SELECT
    customer_id,
    customer_name,
    total_spend
FROM customer_totals
WHERE total_spend > (
    SELECT AVG(total_spend)
    FROM customer_totals
)
ORDER BY total_spend DESC;
```

## Explanation

The CTE is named:

```sql
customer_totals
```

It calculates the total amount spent by each customer.

The calculation is:

```sql
SUM(oi.quantity * p.price)
```

The outer query then calculates the average customer spending:

```sql
SELECT AVG(total_spend)
FROM customer_totals
```

Finally, only customers whose spending is greater than the average are displayed.

## Why a CTE is useful

A CTE makes a complicated query easier to understand by dividing the problem into logical steps.

Instead of calculating customer totals repeatedly, the totals are calculated once in:

```sql
WITH customer_totals AS (...)
```

and then reused by the main query.

## Business Interpretation

Management can use this query to identify customers who generate above-average revenue.

This can help with:

* Customer segmentation
* Customer relationship management
* Sales analysis
* Understanding high-value customers

### Screenshot

Save the result as:



---

# 11. Window Function Query 1 — Rank Customers by Spending

## Requirement

Rank customers according to their total spending, with the highest spender receiving the highest priority rank.

### SQL

```sql
WITH customer_totals AS (
    SELECT
        c.customer_id,
        c.customer_name,
        SUM(oi.quantity * p.price) AS total_spend
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
    JOIN order_items oi
        ON o.order_id = oi.order_id
    JOIN products p
        ON oi.product_id = p.product_id
    GROUP BY
        c.customer_id,
        c.customer_name
)
SELECT
    customer_id,
    customer_name,
    total_spend,
    RANK() OVER (
        ORDER BY total_spend DESC
    ) AS spending_rank
FROM customer_totals
ORDER BY spending_rank;
```

## Explanation

The query first calculates total spending for each customer.

The window function:

```sql
RANK() OVER (
    ORDER BY total_spend DESC
)
```

assigns a rank based on spending.

Because `DESC` is used, customers with larger spending amounts receive smaller rank numbers.

For example:

```text
Rank 1
Rank 2
Rank 3
```

If two customers have the same spending amount, `RANK()` gives them the same rank.

## Business Interpretation

Management can use this information to understand which customers contribute the most revenue.

The ranking can support customer analysis and sales reporting.

### Screenshot

```text
screenshots/
└── window_functions/
    └── customer_spending_rank.png
```

---

# 12. Window Function Query 2 — Number Each Customer's Orders

## Requirement

Number each customer's orders according to the order date.

### SQL

```sql
SELECT
    customer_id,
    order_id,
    order_date,
    ROW_NUMBER() OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS customer_order_number
FROM orders
ORDER BY customer_id, order_date;
```

## Explanation

The window function is:

```sql
ROW_NUMBER()
```

The query uses:

```sql
PARTITION BY customer_id
```

This means that numbering starts again for every customer.

The orders are then arranged by:

```sql
ORDER BY order_date
```

For example, if customer 1 placed three orders, they could receive:

```text
Order 1 → customer_order_number = 1
Order 4 → customer_order_number = 2
Order 9 → customer_order_number = 3
```

## Business Interpretation

This allows management to understand the sequence of customer purchases.

It can be useful for:

* Customer purchase history
* Repeat-purchase analysis
* Customer activity monitoring



---

# 13. Window Function Query 3 — Running Revenue Total

## Requirement

Show a running total of revenue over time, ordered by order date.

### SQL

```sql
WITH order_revenue AS (
    SELECT
        o.order_id,
        o.order_date,
        SUM(oi.quantity * p.price) AS order_total
    FROM orders o
    JOIN order_items oi
        ON o.order_id = oi.order_id
    JOIN products p
        ON oi.product_id = p.product_id
    GROUP BY
        o.order_id,
        o.order_date
)
SELECT
    order_id,
    order_date,
    order_total,
    SUM(order_total) OVER (
        ORDER BY order_date, order_id
    ) AS running_revenue
FROM order_revenue
ORDER BY order_date, order_id;
```

## Explanation

First, the CTE calculates the revenue generated by each order.

For example:

```sql
SUM(oi.quantity * p.price)
```

calculates the total value of an order.

The window function:

```sql
SUM(order_total) OVER (
    ORDER BY order_date, order_id
)
```

then continuously adds each order's revenue to the previous revenue.

This creates a cumulative or running revenue total.

## Example Concept

If the order revenues were:

```text
Order 1 = 20,000
Order 2 = 15,000
Order 3 = 10,000
```

The running revenue would be:

```text
Order 1 → 20,000
Order 2 → 35,000
Order 3 → 45,000
```

## Business Interpretation

A running revenue total allows management to monitor how sales accumulate over time.

It can help management understand:

* Revenue growth
* Sales trends
* Daily sales progression
* Overall business performance

### Screenshot

```text
screenshots/
└── window_functions/
    └── running_revenue.png
```

---

# 14. Window Function Query 4 — Days Between Customer Orders

## Requirement

For each customer with more than one order, show the number of days between the current order and the previous order.

### SQL

```sql
WITH customer_orders AS (
    SELECT
        customer_id,
        order_id,
        order_date,
        LAG(order_date) OVER (
            PARTITION BY customer_id
            ORDER BY order_date
        ) AS previous_order_date,
        COUNT(*) OVER (
            PARTITION BY customer_id
        ) AS order_count
    FROM orders
)
SELECT
    customer_id,
    order_id,
    order_date,
    previous_order_date,
    order_date - previous_order_date AS days_between_orders
FROM customer_orders
WHERE order_count > 1
ORDER BY customer_id, order_date;
```

## Explanation

The `LAG()` function retrieves the previous order date for each customer.

```sql
LAG(order_date) OVER (
    PARTITION BY customer_id
    ORDER BY order_date
)
```

`PARTITION BY customer_id` means that each customer's orders are analyzed separately.

The expression:

```sql
order_date - previous_order_date
```

calculates the number of days between the two dates in Oracle.

The query also uses:

```sql
COUNT(*) OVER (
    PARTITION BY customer_id
)
```

to determine how many orders each customer has.

The condition:

```sql
WHERE order_count > 1
```

ensures that only customers with more than one order are included.

## Business Interpretation

This query helps management understand customer purchasing frequency.

For example, if a customer regularly purchases every few days, the supermarket can understand that customer's buying pattern.

The information can support:

* Customer behavior analysis
* Purchase frequency analysis
* Sales planning
* Customer retention activities



---

# 15. SQL Concepts Demonstrated

This assignment demonstrates several important SQL concepts.

| SQL Concept      | Used For                                             |
| ---------------- | ---------------------------------------------------- |
| `INNER JOIN`     | Combining matching records                           |
| `LEFT JOIN`      | Showing all customers including those without orders |
| `WITH` / CTE     | Creating temporary query results                     |
| `SUM()`          | Calculating sales and spending                       |
| `AVG()`          | Calculating average spending                         |
| `GROUP BY`       | Grouping sales by customer/order                     |
| `RANK()`         | Ranking customers by spending                        |
| `ROW_NUMBER()`   | Numbering orders                                     |
| `LAG()`          | Accessing previous orders                            |
| Window `SUM()`   | Calculating running revenue                          |
| `COUNT() OVER()` | Counting orders per customer                         |
| Date subtraction | Calculating days between orders                      |
| Primary Key      | Uniquely identifying records                         |
| Foreign Key      | Connecting related tables                            |

---

# 16. Business Interpretation

The database analysis provides useful information for Sunrise Supermarket management.

## Customer Analysis

The customer queries show:

* Who placed orders
* Where customers are located
* Which customers have no orders
* Which customers spend more than the average

This gives management a better understanding of the customer base.

## Product Analysis

The order-item query connects products with purchases.

Management can use this information to understand:

* Products being purchased
* Product categories
* Quantity purchased
* Product prices

## Spending Analysis

The CTE calculates the total spending of each customer.

The ranking query then orders customers according to their total spending.

This provides a clear view of customer contribution to supermarket revenue.

## Sales Trend Analysis

The running revenue query shows how total revenue accumulates over time.

This can help management monitor sales progression during the period covered by the data.

## Customer Purchase Frequency

The `LAG()` query shows the number of days between purchases.

This provides information about how frequently customers return to the supermarket.

---

# 17. Challenges Encountered

## Challenge 1 — Connecting Multiple Tables

Some queries required information from four tables:

```text
customers
orders
order_items
products
```

Understanding the relationships between these tables was necessary before writing the queries.

### Resolution

The foreign keys were used to identify the correct relationships:

```text
customers.customer_id
        ↓
orders.customer_id

orders.order_id
        ↓
order_items.order_id

products.product_id
        ↓
order_items.product_id
```

---

## Challenge 2 — Calculating Customer Spending

Customer spending is not stored directly in a table.

It must be calculated using:

```sql
quantity * price
```

### Resolution

The calculation was performed using:

```sql
SUM(oi.quantity * p.price)
```

and grouped by customer.

---

## Challenge 3 — Finding Customers Above Average

The average must be calculated after obtaining each customer's total spending.

### Resolution

A CTE was used:

```sql
WITH customer_totals AS (...)
```

The CTE first calculated each customer's spending, after which the average was calculated from those totals.

---

## Challenge 4 — Working With Previous Orders

Finding the previous order for each customer is difficult using normal aggregation.

### Resolution

The `LAG()` window function was used:

```sql
LAG(order_date) OVER (
    PARTITION BY customer_id
    ORDER BY order_date
)
```

This allows the previous order date to be accessed without manually joining the orders table to itself.

---

## Challenge 5 — Calculating Running Revenue

A normal `SUM()` gives the total but does not automatically provide a cumulative value for every order.

### Resolution

A window-function `SUM()` was used:

```sql
SUM(order_total) OVER (
    ORDER BY order_date, order_id
)
```

This creates a running revenue total.

---

---

# 19. How to Run the Project

## Step 1 — Install Oracle Database

Install Oracle Database or use an Oracle Database environment such as Oracle SQL Developer connected to an Oracle database.

## Step 2 — Open SQL Developer

Open Oracle SQL Developer and connect to your database.

## Step 3 — Create the Tables

Run:

```sql
CREATE TABLE customers (
    customer_id NUMBER PRIMARY KEY,
    customer_name VARCHAR2(100),
    email VARCHAR2(100),
    city VARCHAR2(50)
);

CREATE TABLE products (
    product_id NUMBER PRIMARY KEY,
    product_name VARCHAR2(100),
    category VARCHAR2(50),
    price NUMBER(10,2)
);

CREATE TABLE orders (
    order_id NUMBER PRIMARY KEY,
    customer_id NUMBER REFERENCES customers(customer_id),
    order_date DATE
);

CREATE TABLE order_items (
    order_item_id NUMBER PRIMARY KEY,
    order_id NUMBER REFERENCES orders(order_id),
    product_id NUMBER REFERENCES products(product_id),
    quantity NUMBER
);
```

## Step 4 — Insert the Data

Run the customer, product, order, and order-item INSERT statements.

Then execute:

```sql
COMMIT;
```

## Step 5 — Verify the Data

Run:

```sql
SELECT COUNT(*) FROM customers;

SELECT COUNT(*) FROM products;

SELECT COUNT(*) FROM orders;

SELECT COUNT(*) FROM order_items;
```

The expected minimum results are:

```text
Customers    = 6
Products     = 8
Orders       = 15
Order Items  = 30
```

---

# 23. Conclusion

This assignment demonstrates how SQL can be used to transform raw supermarket transaction data into useful business information.

The JOIN queries combine information from related tables, the CTE simplifies complex customer-spending analysis, and window functions provide advanced analysis such as customer ranking, order sequencing, running revenue, and purchase intervals.

The resulting database provides Sunrise Supermarket management with a foundation for understanding customers, products, purchasing behavior, and revenue trends.


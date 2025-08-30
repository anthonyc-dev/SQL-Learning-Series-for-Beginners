# 📘 SQL Learning Series for Beginners (1 - 18)

---

## 1. Introduction to SQL

* SQL = **Structured Query Language**.
* Purpose: manage and query **relational databases** (data stored in **tables** with relationships).

### Example Table: `customers`

| customer\_id | name    | age | city     |
| ------------ | ------- | --- | -------- |
| 1            | Alice   | 25  | New York |
| 2            | Bob     | 30  | Chicago  |
| 3            | Charlie | 28  | Dallas   |

---

## 2. SELECT (Getting Data)

The most common query.

```sql
-- Select all columns
SELECT * FROM customers;

-- Select specific columns
SELECT name, city FROM customers;
```

---

## 3. Filtering Data with WHERE

```sql
SELECT * FROM customers WHERE age > 25;
SELECT * FROM customers WHERE city = 'Chicago';
```

Operators you’ll use often:

* `=`, `!=`
* `<`, `<=`, `>`, `>=`
* `BETWEEN 20 AND 30`
* `IN ('New York', 'Dallas')`
* `LIKE 'A%'` (starts with A)

---

## 4. Sorting Results

```sql
-- Ascending (default)
SELECT * FROM customers ORDER BY age ASC;

-- Descending
SELECT * FROM customers ORDER BY age DESC;
```

---

## 5. Limiting Data

```sql
SELECT * FROM customers LIMIT 3;  -- only 3 rows
```

---

## 6. Aggregate Functions

Aggregate = summary values.

```sql
SELECT COUNT(*) FROM customers;              -- count rows
SELECT AVG(age) FROM customers;              -- average
SELECT SUM(amount) FROM orders;              -- total sales
SELECT MIN(age), MAX(age) FROM customers;    -- youngest & oldest
```

---

## 7. GROUP BY and HAVING

Grouping rows together.

```sql
-- Count how many customers in each city
SELECT city, COUNT(*) AS total
FROM customers
GROUP BY city;

-- Only show cities with more than 1 customer
SELECT city, COUNT(*) AS total
FROM customers
GROUP BY city
HAVING COUNT(*) > 1;
```

---

## 8. Relational Tables & Joins

Tables connect via **keys**.

Example:
`customers (customer_id)` ↔ `orders (customer_id)`

### INNER JOIN

Only matches:

```sql
SELECT c.name, o.product
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id;
```

### LEFT JOIN

All from left + matches:

```sql
SELECT c.name, o.product
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

### RIGHT JOIN

All from right + matches:

```sql
SELECT c.name, o.product
FROM customers c
RIGHT JOIN orders o ON c.customer_id = o.customer_id;
```

### FULL OUTER JOIN

Everything, match or not:

```sql
SELECT c.name, o.product
FROM customers c
FULL OUTER JOIN orders o ON c.customer_id = o.customer_id;
```

---

## 9. Subqueries

A query inside another query.

```sql
-- Customers who placed orders
SELECT name
FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders);
```

---

## 10. Modifying Data

### Insert

```sql
INSERT INTO customers (name, age, city)
VALUES ('David', 22, 'Boston');
```

### Update

```sql
UPDATE customers
SET city = 'Los Angeles'
WHERE name = 'Alice';
```

### Delete

```sql
DELETE FROM customers WHERE name = 'Charlie';
```

⚠️ Always use `WHERE`, otherwise all rows are affected.

---

## 11. Indexes and Keys

* **Primary Key** = uniquely identifies a row.
* **Foreign Key** = enforces relationship between tables.
* **Index** = makes queries faster.

```sql
CREATE TABLE customers (
  customer_id INT PRIMARY KEY,
  name VARCHAR(100),
  age INT
);

CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  customer_id INT,
  product VARCHAR(100),
  FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

---

## 12. Best Practices

✅ Always select **specific columns** instead of `*`.
✅ Use **clear table & column names** (`customer_id` > `id`).
✅ **Backup** before running `DELETE` or `UPDATE`.
✅ Add **indexes** to columns you filter/join often.
✅ **Normalize** data (avoid storing duplicate info).
✅ Use **transactions** when doing multiple changes.
✅ Apply **constraints** (NOT NULL, UNIQUE, CHECK).
✅ Keep queries **readable** → use aliases (`c`, `o`).
✅ Test queries on a **subset of data** before production.

---

## 13. Data Types

Different databases have slightly different types, but common ones are:

* **INT** → whole numbers
* **DECIMAL/NUMERIC** → precise decimals (money)
* **FLOAT/REAL** → approximate decimals (scientific)
* **VARCHAR(n)** → variable-length string
* **TEXT** → large string
* **DATE / TIME / TIMESTAMP** → temporal data
* **BOOLEAN** → true/false

Example:

```sql
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  name VARCHAR(100),
  price DECIMAL(10,2),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 14. Constraints

Constraints keep data valid.

* **NOT NULL** → column must have value
* **UNIQUE** → no duplicates
* **CHECK** → must satisfy condition
* **DEFAULT** → default value if none given

```sql
CREATE TABLE employees (
  employee_id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL,
  age INT CHECK (age >= 18),
  role VARCHAR(50) DEFAULT 'staff'
);
```

---

## 15. Transactions

A **transaction** = group of SQL statements that succeed or fail together.
Useful in banking, booking, or inventory.

```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;  -- saves changes
-- or
ROLLBACK; -- undo if something failed
```

---

## 16. Views

A **view** = saved query you can treat like a table.

```sql
CREATE VIEW customer_orders AS
SELECT c.name, o.product
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id;

-- Use the view
SELECT * FROM customer_orders;
```

---

## 17. Stored Procedures

A **stored procedure** = a saved block of SQL code that you can run again.

```sql
CREATE PROCEDURE GetCustomerOrders(IN cust_id INT)
BEGIN
  SELECT * FROM orders WHERE customer_id = cust_id;
END;
```

---

## 18. Functions

Databases also have built-in and user-defined functions.

Examples:

```sql
SELECT UPPER(name) FROM customers;   -- convert to uppercase
SELECT NOW();                        -- current date/time
SELECT LENGTH(name) FROM customers;  -- string length
```

---

👉 That’s already a **solid 18-section series**.
Next sections could cover **advanced JOIN tricks, window functions, CTEs, performance tuning, indexing strategies, query optimization, security (roles & privileges), and backup/restore**.

---

Do you want me to **continue building this series (19 → 30+)** into intermediate/advanced topics so your GitHub doc looks like a **complete SQL handbook**?

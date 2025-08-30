# 📘 SQL Learning Series (19 - 39)

---

## 19. Common Table Expressions (CTEs)

CTEs make queries **cleaner and reusable**. Think of them as **temporary named result sets**.

```sql
WITH CustomerOrderCount AS (
  SELECT customer_id, COUNT(*) AS total_orders
  FROM orders
  GROUP BY customer_id
)
SELECT c.name, co.total_orders
FROM customers c
JOIN CustomerOrderCount co ON c.customer_id = co.customer_id;
```

✅ Easier to read than nesting subqueries.

---

## 20. Window Functions

Window functions let you calculate **aggregates without grouping the whole result**.

```sql
-- Rank customers by age
SELECT name, age,
       RANK() OVER (ORDER BY age DESC) AS rank_by_age
FROM customers;

-- Running total of orders
SELECT order_id, customer_id,
       SUM(amount) OVER (ORDER BY order_id) AS running_total
FROM orders;
```

💡 Useful for analytics (ranking, rolling totals, moving averages).

---

## 21. Advanced Joins

Sometimes joins aren’t simple `=`.

```sql
-- Join with range
SELECT e.name, d.discount
FROM employees e
JOIN discounts d ON e.age BETWEEN d.min_age AND d.max_age;

-- Self-join for hierarchy (employees & managers)
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;
```

---

## 22. UNION vs UNION ALL

Combine results from multiple queries.

```sql
SELECT name FROM customers
UNION
SELECT name FROM employees;  -- removes duplicates

SELECT name FROM customers
UNION ALL
SELECT name FROM employees;  -- keeps duplicates
```

---

## 23. Set Operators (INTERSECT, EXCEPT)

Not all databases support these, but when available:

```sql
-- Common names in both tables
SELECT name FROM customers
INTERSECT
SELECT name FROM employees;

-- Names in customers but not in employees
SELECT name FROM customers
EXCEPT
SELECT name FROM employees;
```

---

## 24. CASE Expressions

SQL’s way of handling **if-else logic**.

```sql
SELECT name,
       CASE
         WHEN age < 18 THEN 'Minor'
         WHEN age BETWEEN 18 AND 65 THEN 'Adult'
         ELSE 'Senior'
       END AS age_group
FROM customers;
```

---

## 25. Performance Tuning Basics

### Tips:

* Avoid `SELECT *` → select only needed columns.
* Use **indexes** wisely (too many = slow inserts).
* Check **execution plan** (`EXPLAIN` in MySQL/Postgres).
* Rewrite **nested subqueries** as **joins** or **CTEs**.
* Add **composite indexes** for multiple columns.

```sql
EXPLAIN SELECT * FROM customers WHERE age > 30;
```

---

## 26. Indexing Strategies

Indexes speed up queries but have trade-offs.

* **Single-column index** → fast filter on one column.
* **Composite index** `(col1, col2)` → efficient when filtering on both.
* **Covering index** → includes all needed columns, avoids table lookup.

```sql
CREATE INDEX idx_customers_city_age
ON customers (city, age);
```

---

## 27. Security & User Management

Databases support **users, roles, privileges**.

```sql
-- Create user
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'strongpassword';

-- Give permissions
GRANT SELECT, INSERT, UPDATE ON shop_db.* TO 'app_user'@'localhost';

-- Revoke
REVOKE DELETE ON shop_db.* FROM 'app_user'@'localhost';
```

✅ Principle of least privilege = never give more access than needed.

---

## 28. Backup & Restore

### MySQL

```bash
mysqldump -u root -p shop_db > backup.sql
mysql -u root -p shop_db < backup.sql
```

### PostgreSQL

```bash
pg_dump shop_db > backup.sql
psql shop_db < backup.sql
```

---

## 29. Triggers

Triggers run automatically when something happens (INSERT, UPDATE, DELETE).

```sql
CREATE TRIGGER before_order_insert
BEFORE INSERT ON orders
FOR EACH ROW
BEGIN
  IF NEW.amount <= 0 THEN
    SIGNAL SQLSTATE '45000'
      SET MESSAGE_TEXT = 'Amount must be positive';
  END IF;
END;
```

✅ Use carefully — they can complicate debugging.

---

## 30. Stored Functions

Unlike stored procedures, **functions return a value**.

```sql
CREATE FUNCTION GetTotalOrders(cust_id INT)
RETURNS INT
BEGIN
  DECLARE total INT;
  SELECT COUNT(*) INTO total FROM orders WHERE customer_id = cust_id;
  RETURN total;
END;
```

---

## 31. Advanced Data Integrity

* **Foreign Keys with CASCADE**

```sql
CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  customer_id INT,
  FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
  ON DELETE CASCADE
);
```

💡 If a customer is deleted → their orders auto-delete.

* **CHECK constraints** for business rules.

---

## 32. Normalization vs Denormalization

* **Normalization** → reduce redundancy, better consistency.

  * Example: customer info in one table, orders in another.
* **Denormalization** → duplicate some data for speed (useful in analytics).

👉 Use normalization for OLTP (apps), denormalization for OLAP (reporting).

---

## 33. Transactions with Isolation Levels

Prevent issues when multiple users change data at once.

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

BEGIN;
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;
```

Isolation levels:

* READ UNCOMMITTED
* READ COMMITTED
* REPEATABLE READ
* SERIALIZABLE (safest, slowest)

---

## 34. JSON in SQL

Modern databases support JSON.

```sql
-- Store JSON
INSERT INTO products (details)
VALUES ('{"color":"red","size":"M"}');

-- Query JSON (Postgres/MySQL)
SELECT details->>'color' FROM products;
```

---

## 35. Temporary Tables

Useful for staging results.

```sql
CREATE TEMP TABLE temp_orders AS
SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '7 days';
```

---

## 36. Materialized Views

Like views, but results are stored physically (faster for big data).

```sql
CREATE MATERIALIZED VIEW recent_sales AS
SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '30 days';

-- Refresh when needed
REFRESH MATERIALIZED VIEW recent_sales;
```

---

## 37. Partitioning

Split large tables for performance.

```sql
CREATE TABLE orders_2024 PARTITION OF orders
FOR VALUES FROM ('2024-01-01') TO ('2024-12-31');
```

---

## 38. SQL for Analytics

Combine window functions + aggregates:

```sql
-- Top 3 products by revenue
SELECT product,
       SUM(amount) AS total_sales,
       RANK() OVER (ORDER BY SUM(amount) DESC) AS rank
FROM orders
GROUP BY product
ORDER BY total_sales DESC
LIMIT 3;
```

---

## 39. ETL with SQL

Extract, Transform, Load.

```sql
-- Extract
SELECT * FROM raw_orders;

-- Transform (clean up)
SELECT TRIM(customer_name), UPPER(city) FROM raw_orders;

-- Load (insert into cleaned table)
INSERT INTO clean_orders (customer, city)
SELECT TRIM(customer_name), UPPER(city) FROM raw_orders;
```

---

## 40. Final Best Practices (Advanced)

✅ Always document your schema.
✅ Use **naming conventions** (snake\_case).
✅ Archive old data instead of deleting.
✅ Monitor slow queries with logs.
✅ Automate backups & schema migrations.
✅ Secure with roles & encryption.
✅ Think about **scalability** early (partitioning, sharding).

---


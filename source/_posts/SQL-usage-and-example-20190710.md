---
title: SQL usage and example 
date: 2019-07-26 19:14:02
tags: [SQL]
categories: [Web后端]
---




本文是在学习https://www.youtube.com/watch?v=7S_tz1z_5bA 课程时的一些记录，使用的数据可以去网上下载[here](https://github.com/stan1318/SQL-Course-Materials)

本文同时参考了[MySQL cheatsheet](https://devhints.io/mysql)，这个网站整理了很多资料，不错

### Create / Delete Database
```sql
CREATE DATABASE dbNameYouWant
CREATE DATABASE dbNameYouWant CHARACTER SET utf8
DROP DATABASE dbNameYouWant
ALTER DATABASE dbNameYouWant CHARACTER SET utf8
```

### Backup Database to SQL File
```sql
mysqldump -u Username -p dbNameYouWant > databasename_backup.sql
```

### Restore from backup SQL File
```sql
mysql - u Username -p dbNameYouWant < databasename_backup.sql
```


### Repair Tables After Unclean Shutdown
```sql
mysqlcheck --all-databases
mysqlcheck --all-databases --fast
```

### Browsing
```sql
SHOW DATABASES
SHOW TABLES
SHOW FIELDS FROM table / DESCRIBE table
SHOW CREATE TABLE table
SHOW PROCESSLIST
KILL process_number
```


### Basics

```sql
USE sql_store;

SELECT *
FROM customers
WHERE state = 'CA'
ORDER BY first_name
LIMIT 3;
```

1. SQL不区分大小写；
2. 每个语句以分号结束；

### 注释

```sql
-- This is a comment and it won't get executed.
```

SQL注释以`--`为标志

###  SELECT
```sql
SELECT * FROM table
SELECT * FROM table1, table2, ...
SELECT field1, field2, ... FROM table1, table2, ...
SELECT ... FROM ... WHERE condition
SELECT ... FROM ... WHERE condition GROUPBY field
SELECT ... FROM ... WHERE condition GROUPBY field HAVING condition2
SELECT ... FROM ... WHERE condition ORDER BY field1, field2
SELECT ... FROM ... WHERE condition ORDER BY field1, field2 DESC
SELECT ... FROM ... WHERE condition LIMIT 10
SELECT DISTINCT field1 FROM ...
SELECT DISTINCT field1, field2 FROM ...
```

#### 定义新column
```sql
SELECT (points * 10 + 20) AS discount_factor
FROM customers
```

#### 去除重复项
```sql
SELECT DISTINCT state
FROM customers
```

### WHERE

#### 比较运算符
>: 大于
>=: 大于或等于
><: 小于
><=: 小于或等于
>=: 等于
><>: 不等于
>!=: 不等于

#### 逻辑运算符
##### AND
```sql
SELECT *
FROM customers
WHERE birthdate > '1990-01-01' AND points > 1000
```

##### OR
```sql
SELECT *
FROM customers
WHERE birthdate > '1990-01-01' OR points > 1000
```
##### NOT
```sql
SELECT *
FROM customers
WHERE NOT(birthdate > '1990-01-01')
```

#### IN 操作符
```sql
-- Returns customers in any of these states: VA, NY, CA
SELECT *
FROM customers 
WHERE state IN ('VA', 'NY', 'CA')
```

#### BETWEEN 操作符
```sql
SELECT *
FROM customers
WHERE points BETWEEN 100 AND 200
```
#### LIKE 操作符

+ %: any number of characters
+ _: exactly one characters
```sql
SELECT *
FROM customers
WHERE first_name LIKE 'b%'

-- Returns customers whose first name starts with b
```

#### REGEXP 操作符
+ ^：标识一个字符串的开头
+ $：标识一个字符串的结尾
+ |：逻辑OR
+ [abc]：匹配任意一个字符
+ [a-d]：匹配a-d之间任意一个字符

```sql
-- Returns customers whose first name starts with a
SELECT *
FROM customers
WHERE first_name REGEXP '^a'
```

```sql
-- Returns customers whose first name ends with EY or ON 
WHERE first_name REGEXP 'ey$|on$'

-- Returns customers whose first name starts with MY or contains SE
WHERE first_name REGEXP '^my|se'

-- Returns customers whose first name contains B followed by  R or U
WHERE first_name REGEXP 'b[ru]'
```

#### IS NULL 操作符
```sql
-- Returns customers who don’t have a phone number 
SELECT *
FROM customers 
WHERE phone IS NULL

-- IS NOT NULL
```

### ORDER BY

```sql
-- Sort customers by state (in ascending order), and then 
-- by their first name (in descending order) 
SELECT *
FROM customers 
ORDER BY state, first_name DESC
```

### LIMIT
```sql
-- return only 4 customers
SELECT *
FROM customers
LIMIT 3
```

```sql
—- Skip 6 customers and return 3
SELECT *
FROM customers 
LIMIT 6, 3
```

### Select - Join
```sql
SELECT ... FROM t1 JOIN t2 ON t1.id1 = t2.id2 WHERE condition
SELECT ... FROM t1 LEFT JOIN t2 ON t1.id1 = t2.id2 WHERE condition
SELECT ... FROM t1 JOIN (t2 JOIN t3 ON ...) ON ...
```


### Inner Joins

#### 不同的两个表join
```sql
SELECT order_id, orders.customer_id, first_name, last_name
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id
```
可以简写为：
```sql
SELECT order_id, o.customer_id, first_name, last_name
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
```

#### 同一个表join
```sql
USE sql_hr;

SELECT
    e.employee_id,
    e.first_name,
    m.first_name AS manager
FROM employees e
JOIN employees m
    ON e.reports_to = m.employee_id
```

#### 多个（3个以上）表join
```sql
USE sql_store;

SELECT 
    o.order_id,
    o.order_date,
    c.first_name,
    c.last_name,
    os.name AS status
FROM orders o
JOIN customers c 
    ON o.customer_id = c.customer_id
JOIN order_statuses os
    ON o.status = os.order_status_id
```

#### Compound Join Condition
When we have multiple conditions to join these tables.

```sql
use sql_store;

SELECT *
FROM order_items oi
JOIN order_item_notes oin
    ON oi.order_id = oin.order_id
    AND oi.product_id = oin.product_id
```

###  Outer Join
Left Join

Right Join

```sql
use sql_store;

SELECT 
    p.product_id,
    p.name,
    oi.quantity
FROM products p
LEFT JOIN order_items oi
    ON p.product_id = oi.product_id
ORDER BY p.product_id
```






### Using 语句
当两个表有相同的列名时，可以使用Using来简写Join On
```sql
use sql_invoicing;

SELECT
    p.date,
    c.name AS client,
    p.amount,
    pm.name
FROM clients c
JOIN payments p 
    ON c.client_id = p.client_id
JOIN payment_methods pm
    ON p.payment_method = pm.payment_method_id
```
可写为：
```sql
use sql_invoicing;

SELECT
    p.date,
    c.name AS client,
    p.amount,
    pm.name
FROM clients c
JOIN payments p 
    USING (client_id)
JOIN payment_methods pm
    ON p.payment_method = pm.payment_method_id    
```

### Natural Joins 自然连接
自然连接（NATURAL JOIN）是一种特殊的等值连接，将表中具有相同名称的列自动进行匹配。

```sql
use sql_store;

SELECT
    o.order_id,
    c.first_name
FROM orders o
NATURAL JOIN customers c
```

### Cross Joins

```sql
use sql_store;

SELECT
    sh.name AS shipper,
    p.name
FROM products p
CROSS JOIN shippers sh
ORDER BY sh.name
```
另一种写法：
```sql
use sql_store;

SELECT
    sh.name AS shipper,
    p.name
FROM products p, shippers sh
ORDER BY sh.name
```

### Inner、Outer、Full、Cross 区别
现有两张表，Table A 是左边的表。Table B 是右边的表。其各有四条记录，其中有两条记录name是相同的：

1. `INNER JOIN` 产生的结果是AB的交集
```sql
SELECT * 
FROM TableA 
INNER JOIN TableB 
    ON TableA.name = TableB.name
```
2. `LEFT [OUTER] JOIN` 产生表A的完全集，而B表中匹配的则有值，没有匹配的则以null值取代。
```sql
SELECT * 
FROM TableA 
LEFT OUTER JOIN TableB
    ON TableA.name = TableB.name
```
3. `RIGHT [OUTER] JOIN` 产生表B的完全集，而A表中匹配的则有值，没有匹配的则以null值取代。
```sql
SELECT * 
FROM TableA
RIGHT OUTER JOIN TableB 
    ON TableA.name = TableB.name
```
4. `FULL [OUTER] JOIN` 产生A和B的并集。对于没有匹配的记录，则会以null做为值。
```sql
SELECT *
FROM TableA
FULL OUTER JOIN TableB
    ON TableA.name = TableB.name
```
你可以通过`is NULL`将没有匹配的值找出来：
```sql
SELECT *
FROM TableA
FULL OUTER JOIN TableB
    ON TableA.name = TableB.name
WHERE TableA.id IS null OR TableB.id IS null 
```
5. `CROSS JOIN` 把表A和表B的数据进行一个N*M的组合，即笛卡尔积。如本例会产生4*4=16条记录，在开发过程中我们肯定是要过滤数据，所以这种很少用。
```sql
SELECT *
FROM TableA
CROSS JOIN TableB
```

### Union
请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。

```sql
use sql_store;

SELECT 
    customer_id, 
    first_name, 
    points,
    "Bronze" AS type
FROM customers
WHERE points < 2000
UNION
SELECT 
    customer_id, 
    first_name, 
    points,
    "Silver" AS type
FROM customers
WHERE points >= 2000 AND points <3000
UNION
SELECT 
    customer_id, 
    first_name, 
    points,
    "Gold" AS type
FROM customers
WHERE points >= 3000
ORDER BY first_name
```

### Complete SELECT query
```sql
SELECT DISTINCT column, AGG_FUNC(column_or_expression), …
FROM mytable
    JOIN another_table
      ON mytable.column = another_table.column
    WHERE constraint_expression
    GROUP BY column
    HAVING constraint_expression
    ORDER BY column ASC/DESC
    LIMIT count OFFSET COUNT;
```

### Insert
```sql
INSERT INTO table1 (field1, field2, ...) VALUES (value1, value2, ...)
```

例如：
```sql
use sql_store;
INSERT INTO customers
VALUES (
    DEFAULT,
    'John',
    'Smith',
    '1990-01-01',
    NULL,
    'address',
    'city',
    'CA',
    DEFAULT)
```
对于有默认值的，可以不输入
```sql
use sql_store;
INSERT INTO customers
    (first_name,
    last_name,
    birth_date,
    address,
    city,
    state)
VALUES (
    'John',
    'Smith',
    '1990-01-01',
    'address',
    'city',
    'CA')
```
插入多行：
```sql
use sql_store;
INSERT INTO shippers (name)
VALUES ('Shipper1'),
	   ('Shipper2'),
       ('Shipper3')
```


### 一个复杂的例子
从数据库sql_invoicing的invoices表中复制结构至新表invoices_archived，并将client_id列名改为client_name(需要查询client表)，然后从invoices中复制payment_date不为空的数据到invoices_archived中。

```sql
use sql_invoicing;

CREATE TABLE invoices_archived AS
SELECT 
	invoice_id,
    number,
    "client_name",
    invoice_total,
    payment_total,
    invoice_date,
    due_date,
    payment_date
FROM invoices;

TRUNCATE invoices_archived;

INSERT INTO invoices_archived
SELECT 
    i.invoice_id,
    i.number,
    c.name AS client_name,
    i.invoice_total,
    i.payment_total,
    i.invoice_date,
    i.due_date,
    i.payment_date
FROM invoices i
JOIN clients c
    USING(client_id)
WHERE i.payment_date IS NOT NULL    
```

事实上，不需要复制表，然后删除，可以如下：
```sql
use sql_invoicing;

CREATE TABLE invoices_archived AS
SELECT 
    i.invoice_id,
    i.number,
    c.name AS client_name,
    i.invoice_total,
    i.payment_total,
    i.invoice_date,
    i.due_date,
    i.payment_date
FROM invoices i
JOIN clients c
    USING(client_id)
WHERE i.payment_date IS NOT NULL    
```

### Insert
```sql
INSERT INTO table1 (field1, field2, ...) VALUES (value1, value2, ...)
```

### Delete
```sql
DELETE FROM table1 / TRUNCATE table1
DELETE FROM table1 WHERE condition
```

### Update
```sql
UPDATE table1 SET field1=new_value1 WHERE condition

UPDATE table1, table2 SET field1=new_value1, field2=new_value2, ... WHERE  table1.id1 = table2.id2 AND condition
```

### Create / Delete / Modify Table
```sql
CREATE TABLE table (field1 type1, field2 type2, ...)
CREATE TABLE table (field1 type1, field2 type2, ..., INDEX (field))
CREATE TABLE table (field1 type1, field2 type2, ..., PRIMARY KEY (field1))
CREATE TABLE table (field1 type1, field2 type2, ..., PRIMARY KEY (field1, field2))

CREATE TABLE table1 (fk_field1 type1, field2 type2, ...,
  FOREIGN KEY (fk_field1) REFERENCES table2 (t2_fieldA))
    [ON UPDATE|ON DELETE] [CASCADE|SET NULL]
CREATE TABLE table1 (fk_field1 type1, fk_field2 type2, ...,
 FOREIGN KEY (fk_field1, fk_field2) REFERENCES table2 (t2_fieldA, t2_fieldB))
 
CREATE TABLE table IF NOT EXISTS (...)
CREATE TEMPORARY TABLE table (...)
```

### DROP
```sql
DROP TABLE table
DROP TABLE IF EXISTS table
DROP TABLE table1, table2, ...
```

### Users and Privileges
```sql
GRANT ALL PRIVILEGES ON base.* TO 'user'@'localhost' IDENTIFIED BY 'password';
GRANT SELECT, INSERT, DELETE ON base.* TO 'user'@'localhost' IDENTIFIED BY 'password';
REVOKE ALL PRIVILEGES ON base.* FROM 'user'@'host'; -- one permission only
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'user'@'host'; -- all permissions
```









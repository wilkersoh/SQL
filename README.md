# SQL tutorial

Tags: Mysql

- Bookmark

### Introduction

- Client connect to DBMS (Database Management System) and give an instruction for querying or modify data. The DBMS will execute the instruction to datbase and send back the data.

> 潛規則： SELECT這些sql language都寫`大寫`，`小寫` 是那些自己的data 。雖然全部小寫一樣能work。

Database Name - `sql_store`

```sql
USE sql_store;
-- 進入databases裡的 sql_store database;
-- 大多數都會用這個database在以下的tutorial
```

```sql
SELECT * 
FROM customers
WHERE customer_id > 2
ORDER BY first_name
LIMIT 8
-- SQL statement 的command位子
-- SELECT first_name 表示你要show first_name column罷了，* wildcard代表全部
-- FROM 表示從哪個Table
-- LIMIT 一定是最後
```

```sql
SELECT *
FROM orders o
JOIN customers c 
	ON o.customer_id = c.customer_id
ORDER BY c.customer_id
	
SELECT *
FROM orders o, customers c
WHERE o.customer_id = c.customer_id
ORDER BY c.customer_id
-- 上面兩個例子都是一樣的
-- 推薦第一個，因為第二個只要忘記寫WHERE data就會cross joins
-- 而第一個，我們必須跟著JOIN ON的syntax，如果沒跟著會出現systax error提醒
-- 上面例子是 找出customer和它的order customer和order裡的table
-- 只要他們互相都有 就把兩個table的row放在一起
-- 如果 有一個customer沒有order 就不會出現在我的result
```

### SELECT Clause

```sql
SELECT last_name, first_name, points, points + 10
FROM customers

---*****--
SELECT last_name, first_name, points, points + 10 AS point_more_10
FROM customers
-- AS 的功能是 rename table column name
```

1. SELECT 會return出他select的column，如上面 points + 10， 它會把points裡的 value + 10。如下圖Table顯示

[😃](https://www.notion.so/e157b7a55c63487d9b82c001fd952b29)

```sql
SELECT 
	last_name, 
	first_name, 
	points, 
	(points + 10) * 100 AS 'discount_factor'
FROM customers
```

[🥺](https://www.notion.so/b02e9f5a4b1848feb399856fad3581ef)

```sql
-- 不會出現 相同state的value
SELECT DISTINCT state
FROM customers

-- 把state全部print出來
SELECT state
FROM customers
```

### WHERE Clause

```sql
SELECT *
FROM Customers
-- WHERE points > 3000
-- WHERE state = 'VA'
WHERE state = 'va'

--:  >, >=, <, <=, =, !=, <>
-- <> 和 != 是相同的。如：!= 'va' 就是 customers裡 filter出沒有va的state
```

### AND, OR and NOT Operators

```sql
SELECT *
FROM customers
WHERE birth_date > '1990-01-01' AND points > 1000

SELECT *
FROM customers
WHERE birth_date > '1990-01-01' OR points > 1000 
-- 只要大過the year or point就true

SELECT *
FROM customers
WHERE birth_date > '1990-01-01' OR points > 1000 AND state = 'VA'

SELECT *
FROM customers
WHERE birth_date > '1990-01-01' OR 
			(points > 1000 AND state = 'VA')
```

Note: `AND` 和 `OR` 一起用，sql會去run `AND` 先過後才去run `OR`。可以用() 讓更好讀

```sql
SELECT *
FROM customers
WHERE NOT (birth_date > '1990-01-01' OR points > 1000)
-- 找出customer 小過1990 和 少過1000 points

SELECT *
FROM customers
WHERE birth_date <= '1990-01-01' AND points <= 1000
```

Note: 遇到 `NOT` 全部`變成倒反`的了 > 變成 <， OR 變成 AND 

```sql
-- From order_items table, get the items
-- for order #6
-- where the total price is greater than 30

SELECT *
FROM order_items
WHERE order_id = 6 AND unit_price * quantity > 30
```

### IN Operator

```sql
SELECT *
FROM Customers
WHERE state = 'VA' OR state = 'GA' OR state = 'FL'

-- Better way
SELECT *
FROM Customers
WHERE state IN ('VA', 'GA', 'FL') 

-- NOT IN
SELECT *
FROM Customers
WHERE state NOT IN ('VA', 'GA', 'FL') 
```

### BETWEEN Operator

```sql
SELECT *
FROM Customers
WHERE points >= 1000 AND points <= 3000

-- Better way
SELECT *
FROM Customers
WHERE points BETWEEN 1000 AND 3000
```

### LIKE Operator ( `%` , `_` )

```sql
SELECT *
FROM Customers
WHERE last_name LIKE 'b%'
-- b%是說 找出第一個開頭字母b 在last_name裡 不管 大小寫

SELECT *
FROM Customers
WHERE last_name LIKE '%b%'
-- %b% 找出string中 before or after 是b 的 last name， 就是說 哪裡有b 就true

SELECT *
FROM Customers
WHERE last_name LIKE '%y'
-- %y 找出最後的字母是y的

SELECT *
FROM Customers
WHERE last_name LIKE 'b____y' 
-- b+4個底線+y，找出第一個字母是b and 第六個字母是y的last name

-- 結論：
-- % 代表著無限號碼的character
-- _ 代表著single號碼character
```

### REGEXP Operator

```sql
SELECT *
FROM Customers
-- WHERE last_name LIKE '%field%' 
WHERE last_name REGEXP 'field' -- 能取代上面的 %field%

SELECT *
FROM Customers
-- WHERE last_name REGEXP '^field' -- 開頭要是field
-- WHERE last_name REGEXP 'field$' -- 最後要是field
-- WHERE last_name REGEXP 'field|mac' -- 找出last name誰有field or max
WHERE last_name REGEXP '^field|mac' -- 找出field開頭或 mac在任何位子的last name 
WHERE last_name REGEXP '[gim]e' -- 找出ge or ie or me
WHERE last_name REGEXP 'e[a-h]' -- 找出ea, eb, ec..., eh

-- 結論:
-- ^ 開頭
-- $ 結尾
-- |  或
-- [abcd]g 找出 ag, bg等等
-- [a-d]
```

### IS NULL Operator

```sql
SELECT *
FROM Customers
WHERE phone IS NULL

SELECT *
FROM Customers
WHERE phone IS NOT NULL
-- Filter null in the phone
```

### ORDER BY Operator

```sql
SELECT *
FROM customers
ORDER BY first_name DESC 
-- default is ASC

SELECT *
FROM customers
ORDER BY first_name, state
-- 它會ORDER By 兩個value[first_name, state], 如果遇到first_name
-- 開頭字母相同，它會使用第二個state去sort，誰come first

SELECT first_name, last_name
FROM customers
ORDER BY points
-- 他會sort出points的從小到大,但是table只會裡只出現first_name和last_name

----**************-----
-- Only sort order_id 2
-- Get all item total price and desc order
SELECT *, quantity * unit_price AS total_price
FROM order_items
WHERE order_id = 2
ORDER BY quantity * unit_price DESC

-- clean way

SELECT *, quantity * unit_price AS total_price
FROM order_items
WHERE order_id = 2
ORDER BY total_price DESC
-- 直接用 Order by the total_price
```

### LIMIT Clause

```sql
SELECT *
FROM customers
-- LIMIT 3 OFFSET 6
LIMIT 6, 3 -- 6是OFFSET, and 3 is LIMIT
-- page 1: 1 - 3
-- page 2: 4 - 6
-- page 3: 7 - 9

SELECT *
FROM customers
ORDER BY points DESC
LIMIT 3
```

### INNER JOIN

(只需要寫JOIN 就是說是INNER JOIN而不是OUTER JOIN)

```sql
SELECT * 
FROM orders
JOIN customers
	ON orders.customer_id = customers.customer_id
-- Meger orders table和customers table裡相同的customer_id
-- 就是說把那個customer table和他的order table 都放在同一個table
-- print出來後，會拿到 order column 和屬於那個order的customer在同一個 row
-- 這個query是要找 那些customers和他的order。如果同個customer有2個不同的oder，
-- 那就會出現 那個customer 的數據有2個row，因為他有2個不同的order

SELECT order_id, first_name, last_name, orders.customer_id
FROM orders
JOIN customers
	ON orders.customer_id = customers.customer_id
-- 為什麼需要 orders.customer_id 而不能 customer_id？
-- SQL 不知道你是要拿 customers table 裡的customer_id 還是 orders table的
-- 所以只要兩個table 都有同個column名就是要指向你想要拿哪個table的

SELECT order_id, first_name, last_name, o.customer_id
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id
-- orders 換去o [alias name]
-- customers 換去c [alias name]

------*****----------
-- merge order_items and products table
-- list order_id quantity etc..
SELECT order_id, oi.product_id, quantity, oi.unit_price
FROM order_items oi
JOIN products p
	ON p.product_id = oi.product_id
```

### Joining Across Databases

```sql
SELECT *
FROM order_items oi
JOIN sql_inventory.products p 
	ON oi.product_id = p.product_id
-- sql_inventory 說另外一個database
-- 所以我們在用別的database資料 只要 + prefix 指像那個database的name就行了
```

Database Name - sql_hr

### Self Join

```sql
USE sql_hr;

SELECT 
	e.employee_id,
	e.first_name,
	e.reports_to,
	m.first_name AS manager
FROM employees e
JOIN employees m
	ON e.reports_to = m.employee_id
-- 必須使用 Alias name， 其實他們都是同一個table
-- Merge employees和 他的上司的資料 在同一個row
-- 看下圖理解
```

[Before Join](https://www.notion.so/dc5f0ca4f97540e9847745e313a0e95f)

[After Join](https://www.notion.so/6dee4a727a1440279e8b4a691fd3bbe9)

### Join Multiple Table

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
-- 這個是join 3個同一個database裡的table
-- 有orders, customers and order_statuses table
-- 把這3個table 相同的customer_id都放在同一個row

-----***************------
USE sql_invoicing;
-- JOIN clients, payments and payment method table in same row
SELECT 
	p.date,
  	p.invoice_id,
	p.amount,
	c.name,
	pm.name
FROM payments p
JOIN clients c
	ON p.client_id = c.client_id
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
```

### Compound Join Conditions

```sql
SELECT *
FROM order_items oi
JOIN order_item_notes oin
	ON oi.order_id = oin.order_id
	AND oi.product_id = oin.product_id
-- 有conditon去Join table 就叫做 Compound JOIN Condition
```

### Implicit Join Syntax

```sql
SELECT *
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id
	
SELECT *
FROM orders o, customers c
WHERE o.customer_id = c.customer_id
-- 他們都是一樣的，第二個是implicit join
-- 需要注意WHERE， 如果沒有WHERE 的話。orders table 有 10row data，它會join 10 * 10次
-- 這個叫做 cross joins
```

### JOIN Image

![SQL%20tutorial%20984b81749fb5464dbf5d5b4febc497a5/Untitled.png](SQL%20tutorial%20984b81749fb5464dbf5d5b4febc497a5/Untitled.png)

NOTE: 

1. LEFT Join 會拿出 LEFT Table的全部data，他不會去看下面的condition。
2. RIGHT Join 會拿出 RIGHT Table的全部data，他不會去看下面的condition。
3. INNER Join 會看condition，condition對才print出來
4. 如果用LEFT了 就不要用RIGHT， 因為 nested起來會很亂
5. LEFT JOIN 的LEFT table是哪一個？ 就是 `FROM` 的那一個是LEFT
6. `Long story short:` Left Join會print出null的而不會drop掉你的data，用Inner Join，只要condition不是true，他就會filter掉了

### LEFT｜RIGHT OUTER JOIN

(LEFT｜RIGHT 就是他們的shortKey，不需要寫全部)

```sql
SELECT 
	c.customer_id,
	c.first_name,
	o.order_id
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.customer_id		
ORDER BY c.customer_id
-- LEFT 是 FROM 的Table
-- RIGHT 是 JOIN 的Table
-- 用LEFT Join，FROM的Left的Table 它不管下面的condition是true還是false
-- 它都會return LEFT 的Table 的全部資料
-- 一樣For RIGHT， 用RIGHT JOIN 它會return 全部 RIGHT的data

-----*********-----
-- 找出 全部 products被order的數量
-- order items's quantity
-- products

SELECT 
	p.name,
	oi.quantity,
	oi.unit_price
FROM Products p
LEFT JOIN order_items oi
	ON p.product_id = oi.product_id
```

![SQL%20tutorial%20984b81749fb5464dbf5d5b4febc497a5/Screenshot_2021-01-31_at_3.55.57_PM.png](SQL%20tutorial%20984b81749fb5464dbf5d5b4febc497a5/Screenshot_2021-01-31_at_3.55.57_PM.png)

### Outer Joins Between Multiple Tables

```sql
-- 找出全部custom和他的order 不管已經被ship or not
-- 會用到3個不同的tables
SELECT 
	c.customer_id,
	c.first_name,
	o.order_id,
	sh.name AS shipper
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.customer_id		
LEFT JOIN shippers sh
	ON o.shipper_id = sh.shipper_id
ORDER BY c.customer_id
-- 這個有點複雜 第一次的 Customers和Orders table Left JOIN， 就是說
-- 它會拿全部customer 不會去看condition先， 過後得到的output會是
-- 全部customer和不管這個人有沒有order的row
-- 然後進入第二個Left JOIN，shippers和orders table， ⚠️注意： 這裡的Left JOIN
-- 不是指向 customers table了 而是上一個merge出來的output table
-- 就是說 會output全部Orders不必看condition先，因為orders table 變成LEFT了
-- 如果 第二個LEFT JOIN 寫成 INNER JOIN 那樣出來的result就會是：
-- 只output order和這個order 有shipper_id的數據罷了，
-- 如果這個customer有order但是還沒有安排shipper 那樣就不會被list出來

------*********--------
SELECT 
	o.order_id,
	o.order_date,
	c.first_name AS customer,
	sh.name AS shipper,
	os.name AS status
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.customer_id		
LEFT JOIN shippers sh
	ON o.shipper_id = sh.shipper_id
JOIN order_statuses os
	ON o.status = os.order_status_id
-- 1. 拿到全部customers和order table的 merge row，不管他有沒有order
-- 2. 再LEFT Join shippers，它的Left是指向orders table
-- 3. 拿出全部orders和shipper table的merge row，不管他有沒有shipper
-- 4. Inner JOIN另一個order_statueses的table，
-- 5. 拿出orders裡有order_stauses的資料的order

-- 6. 結論就是，我們會merge customer和order table，
-- 7. 然後拿出全部orders，不管有沒有shipper(3)
-- 8. 再拿出有status的order
```

### Self Outer Join

```sql
USE sql_hr;

SELECT 
	e.employee_id,
	e.first_name,
	m.first_name AS manager
FROM employees e
LEFT JOIN employees m
	ON e.reports_to = m.employee_id;
-- 再次提醒，如果使用INNER JOIN，那樣數據裡沒有符合condition的就會print不出來了
```

### USING Clause

```sql
SELECT *
FROM orders o
JOIN customers c
--	ON o.customer_id = c.customer_id
	USING (customer_id)
LEFT JOIN shippers sh
	USING (shipper_id)
-- 如果condition裡的 column name 都一樣可以用USING

SELECT *
FROM order_items oi
JOIN order_item_notes oin
--	ON oi.order_id = oin.order_id AND
--		oi.product_id = oin.product_id
	USING (order_id, product_id)

----*****---- 
--- 3個table，找出 client和他的payment價錢和付款方式
USE sql_invoicing;

SELECT 
	p.date,
	c.name AS client,
	p.amount,
	pm.name AS payment_method
FROM payments p
JOIN clients c USING (client_id)
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
```

### NATURE JOIN

```sql
SELECT 
	o.order_id,
	c.first_name,
	c.customer_id,
	o.customer_id
FROM Orders o
NATURAL JOIN customers c
-- 這個是讓database engine自己去JOIN，兩個table一樣的column name
-- 雖然很短 但是不推薦 很危險
-- c.customer_id && o.customer_id 一定是一樣的
```

### CROSS JOIN (explicit)

```sql
SELECT 
	c.first_name AS customer,
	p.name AS product
FROM Customers c
CROSS JOIN Products p 
ORDER BY c.first_name
-- 這個是不需要condition的，因為它是直接把customers table 放進去porducts table
```

### CROSS JOIN (implicit)

```sql
SELECT 
	c.first_name AS customer,
	p.name AS product
FROM Customers c, products p
ORDER BY c.first_name
-- 和explicit的output是一樣的 只是寫法不同
-- 推薦使用explicit，比較好理解
```

### Union Operator

```sql
SELECT 
	order_id,
	order_date,
	'Active' AS status
FROM orders
WHERE order_date >= '2019-01-01'	
UNION
SELECT 
	order_id,
	order_date,
	'Archived' AS status
FROM orders
WHERE order_date < '2019-01-01'
-- 在sql query裡，是不能夠寫2 set Select(如上)。
-- 但使用Union它會自動幫我們處理2 set Select
```

### Insert single row into table

```sql
INSERT INTO customers
VALUES (
		DEFAULT,  
		'John', 
		'Smith', 
		'1990-01-01',
		NULL,
		'address',
		'city', 	
		'JH',
		DEFAULT
) -- 這個方法全部row的column attribute都要寫進去

INSERT INTO customers (
	first_name,
	last_name,
	birth_date,
	address,
	city,
	state
)
VALUES (
		'Yee', 
		'Wilker', 
		'1990-01-02',
		'address',
		'city', 	
		'JH'
) -- 這個只需要寫需要的value就要了

```

### Insert multiple row into table

```sql
INSERT INTO shippers (name)
VALUES ('shipper1'),
	     ('shipper2')
```

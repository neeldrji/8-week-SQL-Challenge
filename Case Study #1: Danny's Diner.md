# 🍜 Case Study #1: Danny's Diner 
![image](https://github.com/user-attachments/assets/907cdc1d-9959-4513-beed-aad9e2c2eb0c)

***

## Problem Statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:

- sales
- menu
- members

***

## Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/81607668/127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8.png)

***
## Question and Solution


**1. What is the total amount each customer spent at the restaurant?**

````SQL
SELECT 
  sales.customer_id, 
  SUM(menu.price) AS total_sales
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id ASC; 
````
#### Answer:
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |


**2. How many days has each customer visited the restaurant?**

```SQL
select customer_id, count( distinct ordeR_date)  as 'Count' from sales
group by customer_id
```

#### Answer:
| customer_id | Count |
| ----------- | ------|
| A           | 4     |
| B           | 6     |
| C           | 2     |

**3. What was the first item from the menu purchased by each customer?**
```sql
WITH cust_orders_cte AS(
	SELECT
		customer_id,
		order_date,
		product_name,
		row_number() over (partition by customer_id 
		order by order_date) as rank
	FROM
		sales s,
		menu m
	WHERE m.product_id = s.product_id
)

SELECT 
	customer_id,
	product_name
FROM cust_orders_cte
WHERE rank = 1;
```

#### Answer:
| customer_id | product_name |
|-------------|--------------|
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
```sql
SELECT 
	top(1)
	product_name,
	count(s.product_id) as purchased
FROM
	sales s,
	menu m
WHERE m.product_id = s.product_id
GROUP BY product_name
ORDER BY purchased DESC;
```

#### Answer:
| product_name | purchased |
|--------------|-----------|
| ramen        | 8         |


**5. Which item was the most popular for each customer?**
```sql
WITH fav_item_cte AS(
	SELECT
		customer_id,
		product_name,
		count(s.product_id) as ordered,
		dense_rank() over (partition by customer_id 
		order by count(s.product_id) desc) as rank
	FROM
		sales s,
		menu m
	WHERE m.product_id = s.product_id
	GROUP BY 
		customer_id,
		product_name
)

SELECT 
	customer_id,
	product_name,
	ordered
FROM fav_item_cte
WHERE rank = 1;
```

#### Answer:
| customer_id | product_name | ordered |
|-------------|--------------|---------|
| A           | ramen        | 3       |
| B           | sushi        | 2       |
| B           | curry        | 2       |
| B           | ramen        | 2       |
| C           | ramen        | 3       |


**6. Which item was purchased first by the customer after they became a member?**
```sql
WITH memb_orders_cte AS(
	SELECT
		s.customer_id,
		order_date,
		join_date,
		product_id,
		row_number() over (partition by s.customer_id
		order by order_date) as rank
	FROM
		sales s,
		members m
	WHERE
		m.customer_id = s.customer_id
		AND
		order_date >= join_date
)

SELECT 
	customer_id,
	product_name,
	order_date, 
	join_date
FROM 
	memb_orders_cte mo,
	menu m
WHERE 
	m.product_id = mo.product_id
	AND
	rank = 1
;
```

#### Answer:
| customer_id | product_name | order_date | join_date  |
|-------------|--------------|------------|------------|
| A           | curry        | 2021-01-07 | 2021-01-07 |
| B           | sushi        | 2021-01-11 | 2021-01-09 |


**7. Which item was purchased just before the customer became a member?**
```sql
WITH before_memb_orders_cte AS(
	SELECT
		s.customer_id,
		order_date,
		join_date,
		product_id,
		row_number() over (partition by s.customer_id
		order by order_date desc) as rank
	FROM
		sales s,
		members m
	WHERE
		m.customer_id = s.customer_id
		AND
		order_date < join_date
)

SELECT 
	customer_id,
	product_name,
	order_date, 
	join_date
FROM 
	before_memb_orders_cte mo,
	menu m
WHERE 
	m.product_id = mo.product_id
	AND
	rank = 1
;
```
#### Answer:
| customer_id | product_name | order_date | join_date  |
|-------------|--------------|------------|------------|
| A           | sushi        | 2021-01-01 | 2021-01-07 |
| B           | sushi        | 2021-01-04 | 2021-01-09 |


**8. What is the total items and amount spent for each member before they became a member?**
```sql
SELECT
	s.customer_id,
	count(s.product_id) as items,
	sum(men.price) as spent
FROM
	sales s,
	members mem,
	menu men
WHERE
	mem.customer_id = s.customer_id
	AND
	men.product_id = s.product_id
	AND
	order_date < join_date
GROUP BY s.customer_id;
```

#### Answer:
| customer_id | items | spent |
|-------------|-------|-------|
| A           | 2     | 25    |
| B           | 3     | 40    |


**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
```sql
SELECT
	customer_id,
	sum(CASE
		WHEN s.product_id = 1 THEN price*20
		ELSE price*10 
	END) as total_points
FROM
	sales s,
	menu m
WHERE m.product_id = s.product_id
GROUP BY customer_id;
```

#### Answer:
| customer_id | total_points |
|-------------|--------------|
| A           | 860          |
| B           | 940          |
| C           | 360          |


**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
```sql
WITH dates_cte AS(
	SELECT *, 
		DATEADD(DAY, 6, join_date) AS valid_date, 
		EOMONTH('2021-01-1') AS last_date
	FROM members
)

SELECT
	s.customer_id,
	sum(CASE
		WHEN s.product_id = 1 THEN price*20
		WHEN s.order_date between d.join_date and d.valid_date THEN price*20
		ELSE price*10 
	END) as total_points
FROM
	dates_cte d,
	sales s,
	menu m
WHERE
	d.customer_id = s.customer_id
	AND
	m.product_id = s.product_id
	AND
	s.order_date <= d.last_date
GROUP BY s.customer_id;
```

#### Answer:
| customer_id | total_points |
|-------------|--------------|
| A           | 1370         |
| B           | 820          |


## Bonus Question 

**1. Join All The Things, Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.**
```sql
WITH join_all_cte AS(
    SELECT
        s.customer_id,
        men.product_name,
        men.price,
        s.order_date,
        mem.join_date,
        CASE
            WHEN s.order_date >= mem.join_date THEN 'Y'
            ELSE 'N'
        END as "is_member"
    FROM
        menu men,
        sales s
        LEFT JOIN members mem
        ON s.customer_id = mem.customer_id
    WHERE men.product_id = s.product_id
)

SELECT * FROM join_all_cte;
```

#### Answer:
| customer_id | product_name | price | order_date | join_date  | is_member |
| ----------- | ------------ | ----- | ---------- | ---------- | --------- |
| A           | sushi        | 10    | 2021-01-01 | 2021-01-07 | N         |
| A           | curry        | 15    | 2021-01-01 | 2021-01-07 | N         |
| A           | curry        | 15    | 2021-01-07 | 2021-01-07 | Y         |
| A           | ramen        | 12    | 2021-01-10 | 2021-01-07 | Y         |
| A           | ramen        | 12    | 2021-01-11 | 2021-01-07 | Y         |
| A           | ramen        | 12    | 2021-01-11 | 2021-01-07 | Y         |
| B           | curry        | 15    | 2021-01-01 | 2021-01-09 | N         |
| B           | curry        | 15    | 2021-01-02 | 2021-01-09 | N         |
| B           | sushi        | 10    | 2021-01-04 | 2021-01-09 | N         |
| B           | sushi        | 10    | 2021-01-11 | 2021-01-09 | Y         |
| B           | ramen        | 12    | 2021-01-16 | 2021-01-09 | Y         |
| B           | ramen        | 12    | 2021-02-01 | 2021-01-09 | Y         |
| C           | ramen        | 12    | 2021-01-01 | NULL       | N         |
| C           | ramen        | 12    | 2021-01-01 | NULL       | N         |
| C           | ramen        | 12    | 2021-01-07 | NULL       | N         |


**2. Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.**
```sql
SELECT
	*,
	CASE
		WHEN [is_member] = 'N' THEN null
		ELSE
			dense_rank() over (partition by customer_id, [is_member]
			order by order_date)
	END as rank
FROM join_all_cte;
```

#### Answer:
| customer_id | product_name | price | order_date | join_date  | is_member | rank |
| ----------- | ------------ | ----- | ---------- | ---------- | --------- | ---- |
| A           | sushi        | 10    | 2021-01-01 | 2021-01-07 | N         | NULL |
| A           | curry        | 15    | 2021-01-01 | 2021-01-07 | N         | NULL |
| A           | curry        | 15    | 2021-01-07 | 2021-01-07 | Y         | 1    |
| A           | ramen        | 12    | 2021-01-10 | 2021-01-07 | Y         | 2    |
| A           | ramen        | 12    | 2021-01-11 | 2021-01-07 | Y         | 3    |
| A           | ramen        | 12    | 2021-01-11 | 2021-01-07 | Y         | 3    |
| B           | curry        | 15    | 2021-01-01 | 2021-01-09 | N         | NULL |
| B           | curry        | 15    | 2021-01-02 | 2021-01-09 | N         | NULL |
| B           | sushi        | 10    | 2021-01-04 | 2021-01-09 | N         | NULL |
| B           | sushi        | 10    | 2021-01-11 | 2021-01-09 | Y         | 1    |
| B           | ramen        | 12    | 2021-01-16 | 2021-01-09 | Y         | 2    |
| B           | ramen        | 12    | 2021-02-01 | 2021-01-09 | Y         | 3    |
| C           | ramen        | 12    | 2021-01-01 | NULL       | N         | NULL |
| C           | ramen        | 12    | 2021-01-01 | NULL       | N         | NULL |
| C           | ramen        | 12    | 2021-01-07 | NULL       | N         | NULL |


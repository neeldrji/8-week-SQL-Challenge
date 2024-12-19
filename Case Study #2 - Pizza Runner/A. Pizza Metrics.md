## A. Pizza Metrics
## Question and Solution

**1 How many pizzas were ordered?**
``` SQL
select Count(*)  from customer_orders
```
#### Answer:
![image](https://github.com/user-attachments/assets/a339e772-a5c6-49df-892f-c6e2fe099130)


**2 How many unique customer orders were made?**
``` SQL
Select count(distinct order_id )from customer_orders
```
#### Answer:
![image](https://github.com/user-attachments/assets/101b55d2-ba7b-4390-998b-7b886bf92368)

**3 How many successful orders were delivered by each runner?**
``` SQL
select runner_id, count(*) as 'successful orders' From runner_orders
where cancellation = ' '
group by runner_id
```
#### Answer:
![image](https://github.com/user-attachments/assets/49ebef67-8da2-4884-9d20-1d7b0f59cb31)

**4  How many of each type of pizza was delivered?**

``` SQL
select p.pizza_name,  count(*) as ' count' from runner_orders R
Join  customer_orders C ON R.order_id=c.order_id
join pizza_names P On P.pizza_id=C.pizza_id
WHERE distance <> ' '
Group by  p.pizza_name
```
#### Answer:
![image](https://github.com/user-attachments/assets/81199459-b577-4877-8b03-adad54df5bec)

**5  How many Vegetarian and Meatlovers were ordered by each customer?**


``` SQL
Select customer_id,p.pizza_name, Count(*) as 'How many times'from customer_orders c
join pizza_names P On P.pizza_id=C.pizza_id
group by customer_id,p.pizza_name
order by customer_id
```
#### Answer:
![image](https://github.com/user-attachments/assets/6d8c2b6b-25bd-4715-bf6e-aa306742bf64)



**6 What was the maximum number of pizzas delivered in a single order?**

``` SQL
with CTE as
(
select c.order_id,count(*) as 'Number_OF_Pizza' from customer_orders c
Join runner_orders R On R.order_id=C.order_id
where cancellation=' '
group by C.order_id
)
select * from CTE
where Number_of_Pizza= (Select max(Number_of_Pizza) From CTE)
```
#### Answer:

![image](https://github.com/user-attachments/assets/28c6eb8d-6d25-41bd-b1a2-5c46b52d2f47)

**7 For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
``` SQL
select customer_id,
sum ( Case when C.exclusions<>' ' OR c.extras <> ' ' then 1 else 0 end ) as 'Has_change' ,
sum ( Case when C.exclusions= ' ' AND c.extras = ' ' then 1 else 0 end) as 'Has_NO_change'
From customer_orders C
Join runner_orders R
On C.order_id=R.order_id
where R.cancellation= ' ' 
group by customer_id
```
#### Answer:
![image](https://github.com/user-attachments/assets/2883ce12-dc4b-43b4-a502-d1983943e4c0)



**8 How many pizzas were delivered that had both exclusions and extras?**
``` SQL
select 
Sum (
Case when C.exclusions<>' ' and c.extras <> ' ' then 1 else 0 end 
 ) as 'Has_change' 
From customer_orders C
Join runner_orders R
On C.order_id=R.order_id
where R.cancellation= ' ' 
```
#### Answer:

![image](https://github.com/user-attachments/assets/5ddcbb89-508e-45ca-8813-83de93228aaf)



**9 What was the total volume of pizzas ordered for each hour of the day?**

``` SQL

SELECT 
  DATEPART(HOUR, [order_time]) AS hour_of_day, 
  COUNT(order_id) AS pizza_count
FROM customer_orders
GROUP BY DATEPART(HOUR, [order_time]);
```

#### Answer:

![image](https://github.com/user-attachments/assets/2278f1bd-7aab-4106-b0b6-e6611620f3c2)

**10 What was the volume of orders for each day of the week?**
``` SQL
SELECT 
  FORMAT([order_time], 'dddd') AS 'day_of_week',  -- Returns full day name (e.g., "Monday")
  COUNT(order_id) AS pizza_count
FROM customer_orders
GROUP BY FORMAT([order_time], 'dddd');
```

#### Answer:

![image](https://github.com/user-attachments/assets/4e516a72-1fec-40c9-b60a-50a41b5e9c05)

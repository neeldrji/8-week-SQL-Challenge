# 🍕 Case Study #2 Pizza Runner

![image](https://github.com/user-attachments/assets/eef49583-c0b0-4845-af24-48ccf92e0a4d)

## Introduction

While scrolling through his Instagram feed, Danny stumbled upon a captivating tagline: “80s Retro Styling and Pizza Is The Future!” Instantly intrigued, he knew this concept had potential. However, Danny realized that a great idea and delicious pizza alone wouldn’t attract the seed funding needed to expand his budding pizza empire.

That’s when inspiration struck again—he decided to Uberize the pizza delivery experience. And thus, Pizza Runner was born!

Danny started by recruiting dedicated runners to deliver fresh, mouthwatering pizzas directly from Pizza Runner Headquarters (aka Danny’s home). To bring his vision to life, he maxed out his credit card to hire freelance developers, who built a mobile app for seamless customer ordering.

With retro vibes, a cutting-edge delivery model, and a dream, Danny set out to revolutionize the pizza world.

## Dataset

- runners
- customer_orders
- runner_orders
- pizza_names
- pizza_recipes
- pizza_toppings

## Data Clean
There are some known data issues with few tables. Data cleaning was performed and saved in temporary tables before attempting the case study.


**Customer Orders Table**
- The exclusions and extras columns contain blank spaces and null values that need to be addressed.

**Runner Orders Table**
- The pickup_time, distance, duration, and cancellation columns in the runner_orders table also need to be cleaned prior to use.
- The pickup_time column contains null values.
- The distance column has null values and includes the unit 'km,' which must be removed.
- The duration column contains null values and inconsistent units such as 'minutes,' 'mins,' and 'minute,' which should be standardized.
- The cancellation column has both blank spaces and null values that need to be cleaned.

## Entity Relationship Diagram

![Pizza Runner](https://github.com/katiehuangx/8-Week-SQL-Challenge/assets/81607668/78099a4e-4d0e-421f-a560-b72e4321f530)


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



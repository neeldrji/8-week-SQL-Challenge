
**1 How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**
```SQL
select 
DATEADD(DAY, DATEDIFF(DAY, '2021-01-01', Registration_date) / 7 * 7, '2021-01-01') AS custom_week_start ,
count(runner_id)  as ' number of Runners in this Week ' From  runners
group by DATEADD(DAY, DATEDIFF(DAY, '2021-01-01', Registration_date) / 7 * 7, '2021-01-01')

```
**2 What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**

```SQL
WITH time_taken_cte AS
(
  SELECT 
    DATEDIFF(MINUTE, c.order_time, r.pickup_time) AS pickup_minutes
  FROM customer_orders AS c
  JOIN runner_orders AS r
    ON c.order_id = r.order_id
  WHERE r.cancellation= ' '
)

SELECT 
  AVG(pickup_minutes) AS avg_pickup_minutes
FROM time_taken_cte
WHERE pickup_minutes > 1;
```

**3 Is there any relationship between the number of pizzas and how long the order takes to prepare?**

```SQL
With CTE_A AS
(
select c.order_id,  count(pizza_id) as P ,  round(cast(format(R.pickup_time-c.order_time, 'mm') as int),3) as 'A_TIME' from customer_orders c
Join runner_orders R On C.order_id=R.order_id
where r.cancellation=' ' 
group by c.order_id, cast(format(R.pickup_time-c.order_time, 'mm') as int)
)

select p, avg([A_TIME]) from CTE_A
Group by  p
```
**4 What was the average distance travelled for each customer?**
```SQL
select Customer_id, avg(cast(R.distance as Int )) as 'Distance' From customer_orders C
Join runner_orders R On R.order_id=c.order_id
where R.cancellation= ' '
Group by customer_id
```

**5 What was the difference between the longest and shortest delivery times for all orders?**
```SQL
SELECT
  MAX(duration) - MIN(duration) AS time_difference
FROM 
runner_orders
```

**6 What was the average speed for each runner for each delivery and do you notice any trend for these values?**

```SQL
select runner_id, Avg(Distance/Duration) as Speed from runner_orders
where cancellation= ' '
Group by runner_id
```

**7 What is the successful delivery percentage for each runner?**

```SQL
SELECT 
  runner_id, 
  ROUND(100 * SUM(
    CASE WHEN distance = 0 THEN 0
    ELSE 1 END) / COUNT(*), 0) AS success_perc
FROM runner_orders
GROUP BY runner_id;
```

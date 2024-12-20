## B. Runner and Customer Experience
## Question and Solution


**1 How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**
```SQL
select 
DATEADD(DAY, DATEDIFF(DAY, '2021-01-01', Registration_date) / 7 * 7, '2021-01-01') AS custom_week_start ,
count(runner_id)  as ' number of Runners in this Week ' From  runners
group by DATEADD(DAY, DATEDIFF(DAY, '2021-01-01', Registration_date) / 7 * 7, '2021-01-01')

```

#### Answer:
![image](https://github.com/user-attachments/assets/2294a62b-1b1e-4c77-b710-d0f9fa472c7b)


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

#### Answer:
![image](https://github.com/user-attachments/assets/8bdd201c-ae50-4f17-a8db-53a5c0e77a89)



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
#### Answer:

![image](https://github.com/user-attachments/assets/476d78b6-186e-4b72-9d6e-d8a402608ba5)


**4 What was the average distance travelled for each customer?**
```SQL
SELECT customer_id,
       round(avg(distance), 2) AS 'average_distance_travelled'
FROM runner_orders_temp
INNER JOIN customer_orders_temp USING (order_id)
WHERE cancellation IS NULL
GROUP BY customer_id;
```

#### Answer:
![image](https://github.com/user-attachments/assets/500be76d-89f3-4e86-8a8b-f22c951b5dab)



**5 What was the difference between the longest and shortest delivery times for all orders?**
```SQL
SELECT MIN(duration) minimum_duration,
       MAX(duration) AS maximum_duration,
       MAX(duration) - MIN(duration) AS maximum_difference
FROM runner_orders_temp;
```

#### Answer: 
![image](https://github.com/user-attachments/assets/552744af-880f-4fd5-ac0f-c45434c4b0dc)



**6 What was the average speed for each runner for each delivery and do you notice any trend for these values?**

```SQL
SELECT 
  r.runner_id, 
  c.customer_id, 
  c.order_id, 
  COUNT(c.order_id) AS pizza_count, 
  r.distance, (r.duration / 60) AS duration_hr , 
  ROUND((r.distance/r.duration * 60), 2) AS avg_speed
FROM #runner_orders AS r
JOIN #customer_orders AS c
  ON r.order_id = c.order_id
WHERE distance != 0
GROUP BY r.runner_id, c.customer_id, c.order_id, r.distance, r.duration
ORDER BY c.order_id;
```

#### Answer:
![image](https://github.com/user-attachments/assets/b2e07521-72e4-4e01-95a1-b7a7980b945f)



**7 What is the successful delivery percentage for each runner?**

```SQL
SELECT 
  runner_id, 
  ROUND(100 * SUM(
    CASE WHEN distance = 0 THEN 0
    ELSE 1 END) / COUNT(*), 0) AS success_perc
FROM #runner_orders
GROUP BY runner_id;
```

#### Answer:
![image](https://github.com/user-attachments/assets/ef84ee49-734b-4715-a2e4-74eafeac4765)


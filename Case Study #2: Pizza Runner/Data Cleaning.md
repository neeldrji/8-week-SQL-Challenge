## Data Cleaning
There are some known data issues with few tables. Data cleaning was performed and saved in temporary tables before attempting the case study.


**Customer Orders Table**
- The exclusions and extras columns contain blank spaces and null values that need to be addressed.

```SQL
-- Drop the temporary table if it exists
IF OBJECT_ID('tempdb..#customer_orders_temp') IS NOT NULL
    DROP TABLE #customer_orders_temp;

-- Create the temporary table and populate it with data
SELECT 
    order_id,
    customer_id,
    pizza_id,
    CASE
        WHEN exclusions = '' THEN NULL
        WHEN exclusions = 'null' THEN NULL
        ELSE exclusions
    END AS exclusions,
    CASE
        WHEN extras = '' THEN NULL
        WHEN extras = 'null' THEN NULL
        ELSE extras
    END AS extras,
    order_time
INTO #customer_orders_temp
FROM customer_orders;

-- Select data from the temporary table
SELECT * FROM #customer_orders_temp;

```
#### Answer:
![image](https://github.com/user-attachments/assets/a4a5c964-1a90-4a4f-bcad-b360e25c7829)


**Runner Orders Table**
- The pickup_time, distance, duration, and cancellation columns in the runner_orders table also need to be cleaned prior to use.
- The pickup_time column contains null values.
- The distance column has null values and includes the unit 'km,' which must be removed.
- The duration column contains null values and inconsistent units such as 'minutes,' 'mins,' and 'minute,' which should be standardized.
- The cancellation column has both blank spaces and null values that need to be cleaned.

```SQL
-- Drop the temporary table if it exists
IF OBJECT_ID('tempdb..#runner_orders_temp') IS NOT NULL
    DROP TABLE #runner_orders_temp;

-- Create the temporary table and populate it with transformed data
SELECT 
    order_id, 
    runner_id,
    CASE
        WHEN pickup_time LIKE 'null' THEN ' '
        ELSE pickup_time
    END AS pickup_time,
    CASE
        WHEN distance LIKE 'null' THEN ' '
        WHEN distance LIKE '%km' THEN REPLACE(distance, 'km', '')
        ELSE distance 
    END AS distance,
    CASE
        WHEN duration LIKE 'null' THEN ' '
        WHEN duration LIKE '%mins' THEN REPLACE(duration, 'mins', '')
        WHEN duration LIKE '%minute' THEN REPLACE(duration, 'minute', '')
        WHEN duration LIKE '%minutes' THEN REPLACE(duration, 'minutes', '')
        ELSE duration
    END AS duration,
    CASE
        WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ' '
        ELSE cancellation
    END AS cancellation
INTO #runner_orders_temp
FROM runner_orders;

-- View the data from the temporary table
SELECT * FROM #runner_orders_temp;


```

#### Answer:
![image](https://github.com/user-attachments/assets/df1ed554-18f6-4e81-8b5b-63a0e6a1b3e1)

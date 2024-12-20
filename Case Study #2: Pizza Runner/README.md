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

- Click [Here](https://github.com/neeldrji/8-week-SQL-Challenge/blob/eb856910d8f80803c52b6e4b660a4de9cd794159/Case%20Study%20%232%3A%20Pizza%20Runner/Data%20Cleaning.md) to view Data cleaning & transfomation.

## Entity Relationship Diagram

![Pizza Runner](https://github.com/katiehuangx/8-Week-SQL-Challenge/assets/81607668/78099a4e-4d0e-421f-a560-b72e4321f530)

## Solutions
- [A. Pizza Metrics](https://github.com/neeldrji/8-week-SQL-Challenge/blob/c82b677d8d9b0b14ba93f90c75ea5939911ad8c5/Case%20Study%20%232%20-%20Pizza%20Runner/A.%20Pizza%20Metrics.md)
- [B. Runner and Customer Experience](https://github.com/neeldrji/8-week-SQL-Challenge/blob/121795ced2878064a62a438af5d314821f3d494a/Case%20Study%20%232%3A%20Pizza%20Runner/B.%20Runner%20and%20Customer%20Experience.md)

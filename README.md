#  Business Task 
Case study of a Zomato-like startup where I answer questions related to Pizza Metrics, Runner & Customer Experience, Ingredient Optimisation, and Pricing and Ratings so that the venture can better direct its runners and optimize operations. Also, a significant focus on handling data quality issues. View the Question here🙋‍♀️  [Link](https://8weeksqlchallenge.com/case-study-2/)

-- Creation of Database
````
CREATE DATABASE pizza_runner;
USE pizza_runner;

DROP TABLE IF EXISTS runners;
CREATE TABLE runners (
  "runner_id" INTEGER,
  "registration_date" DATE
);
INSERT INTO runners
  ("runner_id", "registration_date")
VALUES
  (1, '2021-01-01'),
  (2, '2021-01-03'),
  (3, '2021-01-08'),
  (4, '2021-01-15');
````
````
DROP TABLE IF EXISTS customer_orders;
CREATE TABLE customer_orders (
  "order_id" INTEGER,
  "customer_id" INTEGER,
  "pizza_id" INTEGER,
  "exclusions" VARCHAR(4),
  "extras" VARCHAR(4),
  "order_time" DATETIME  -- I changed the data type of "order_date" to DATETIME instead of TIMESTAMP because SQL Server didn't allow insertion of explicit values of timestamp in the column.With DATETIME , it worked fine.
);
````
````
INSERT INTO customer_orders
  ("order_id", "customer_id", "pizza_id", "exclusions", "extras", "order_time")
VALUES
  ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
  ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
  ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
  ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
  ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
  ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
  ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
  ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
  ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
  ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
  ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');
````
````
DROP TABLE IF EXISTS runner_orders;
CREATE TABLE runner_orders (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "pickup_time" VARCHAR(19),
  "distance" VARCHAR(7),
  "duration" VARCHAR(10),
  "cancellation" VARCHAR(23)
);
````
````
INSERT INTO runner_orders
  ("order_id", "runner_id", "pickup_time", "distance", "duration", "cancellation")
VALUES
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
  ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
  ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');


DROP TABLE IF EXISTS pizza_names;
CREATE TABLE pizza_names (
  "pizza_id" INTEGER,
  "pizza_name" TEXT
);
INSERT INTO pizza_names
  ("pizza_id", "pizza_name")
VALUES
  (1, 'Meatlovers'),
  (2, 'Vegetarian');


DROP TABLE IF EXISTS pizza_recipes;
CREATE TABLE pizza_recipes (
  "pizza_id" INTEGER,
  "toppings" TEXT
);
INSERT INTO pizza_recipes
  ("pizza_id", "toppings")
VALUES
  (1, '1, 2, 3, 4, 5, 6, 8, 10'),
  (2, '4, 6, 7, 9, 11, 12');


DROP TABLE IF EXISTS pizza_toppings;
CREATE TABLE pizza_toppings (
  "topping_id" INTEGER,
  "topping_name" TEXT
);
INSERT INTO pizza_toppings
  ("topping_id", "topping_name")
VALUES
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');
````
 -- Database Creation Completed.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
Danny has already mentioned in the Case Study that some of the tables provided have some data quality issues.
 
 1. The columns "exclusions" and "extras" in the table "customer_orders" have null values.
 2. The columns "pickup_time", "distance" , "duration" and "cancellation" in the table "runner_orders" have null values.
 3. Both the tables "customer_orders" and "runner_orders" have issues relating to data type of the columns.

 So, first we need to deal with these data quality issues before moving on to querying and solving the Case Study questions.
   Dealing with DATA QUALITY Issues 
  Cleaning the table "customer_orders"
  1. exclusions: remove NULL and nulls and replace with ''
  2. extras: remove NULL and nulls and replace with ''
  SELECT order_id,customer_id,pizza_id,
  CASE 
      WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN ''
      ELSE exclusions
      END AS exclusions,
  CASE
      WHEN extras IS NULL OR extras LIKE 'null' THEN ''
      ELSE extras 
      END AS extras,
	  order_time

 INTO #customer_orders        -- #customer_orders is a temporary table which is stored somewhere in a database. So, it can be used in other queries.
 FROM customer_orders;

-- Cleaning the table "runner_orders"

-- 1. Clean the values
-- 2. Change the data types

 1. pickup_time: remove nulls and replace with ''
 2. distance: remove km and replace nulls with ''
 3. duration: remove minutes and replace nulls with ''
 4. cancellation: remove NULL and nulls and replace with ''
````
 SELECT order_id, runner_id,

 CASE 
      WHEN pickup_time LIKE 'null' THEN ''
	  ELSE pickup_time
	  END AS pickup_time,
 CASE 
      WHEN distance LIKE 'null' THEN ''
	  WHEN distance LIKE '%km' THEN TRIM( 'km' FROM distance)
	  ELSE distance 
	  END AS distance,
CASE 
	  WHEN duration LIKE 'null' THEN ''
	  WHEN duration LIKE '%mins' THEN TRIM('mins' from duration)
	  WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)
	  WHEN duration LIKE '%minutes' THEN TRIM( 'minutes' FROM duration)
	  ELSE duration
	  END AS duration,
CASE 
	  WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ''
	  ELSE cancellation
	  END AS cancellation
````

INTO #runner_orders         -- #runner_orders is a temporary table which is stored somewhere in a database. So, it can be used in other queries.
 FROM runner_orders;

-- Changing the Dataypes

 1. pickup_time: VARCHAR to DATETIME
 2. distance : VARCHAR to FLOAT
 3. duration: VARCHAR to INT


 ALTER TABLE #runner_orders
 ALTER COLUMN pickup_time DATETIME;

 ALTER TABLE #runner_orders
 ALTER COLUMN distance FLOAT;

 ALTER TABLE #runner_orders
 ALTER COLUMN duration INT;
 -- Data Cleaning and Transformation Completed.

# PIZA METRICS🍕
This file covers Solutions to Questions regarding Pizza Metrics.

## Q1. How many pizzas were ordered? 
````
SELECT 
COUNT(pizza_id) AS pizza_order_count
 FROM #customer_orders;
````
 So, 14 pizzas were ordered.


## Q2. How many unique customer orders were made?  
````
 SELECT 
COUNT(DISTINCT customer_id) AS unique_customer_count
 FROM #customer_orders;
````
 So, 5 unique customer orders were made.

### Q3. How many successful orders were delivered by each runner? 
````
SELECT
runner_id, 
COUNT(pickup_time) AS successful_order_count
 FROM #runner_orders
 WHERE pickup_time != ''
 GROUP BY runner_id;
````

 #### Q4. How many of each type of pizza was delivered? 
````
 SELECT 
c.pizza_id , 
COUNT(r.pickup_time) AS pizza_count
 FROM #customer_orders c
 JOIN #runner_orders r
 ON c.order_id =r.order_id
 WHERE r.pickup_time != ''
 GROUP BY c.pizza_id;
````
#### Q5. How many Vegetarian and Meatlovers were ordered by each customer?  
````
SELECT
c.customer_id, 
CAST(p.pizza_name AS VARCHAR(20)) AS pizza_name,
COUNT(CAST(p.pizza_name AS VARCHAR(20))) AS pizza_count
 FROM #customer_orders c
 JOIN pizza_names p                                                    
 ON c.pizza_id = p.pizza_id
 GROUP BY c.customer_id,CAST(p.pizza_name AS VARCHAR(20));
````

 Here, pizza_name (TEXT) is cast as VARCHAR(20) to enable comparisons and calculations.

### Q6. What was the maximum number of pizzas delivered in a single order?  
````
 WITH pizza_per_order_cte AS
(SELECT 
c.order_id, 
COUNT(c.pizza_id) AS pizza_count_per_order
 FROM #customer_orders c
 JOIN #runner_orders r
 ON c.order_id = r.order_id
 WHERE r.pickup_time != ''
 GROUP BY c.order_id
 )
```
````
SELECT 
MAX(pizza_count_per_order) AS max_pizza_count
FROM pizza_per_order_cte;
````
So, the maximum number of pizzas delivered in a single order is 3.
 Q7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````
SELECT 
c.customer_id,
SUM
(CASE WHEN c.exclusions != '' OR c.extras != '' THEN 1
ELSE 0
END) AS at_least_1_change,
SUM
(CASE WHEN c.exclusions = '' AND c.extras = '' THEN 1
ELSE 0
END) AS no_change

 FROM #customer_orders c
 JOIN #runner_orders r
 ON c.order_id = r.order_id
 WHERE r.pickup_time != ''
 GROUP BY c.customer_id;
````


Q8. How many pizzas were delivered that had both exclusions and extras? 
````
SELECT 
COUNT(c.pizza_id) AS pizza_count_w_exclusions_extras
  FROM #customer_orders c
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE
		(c.exclusions != '' AND c.extras != '') 
		AND (r.pickup_time != '');
````
-- So, there is just 1 pizza which had both exclusions and extras.

### Q9. What was the total volume of pizzas ordered for each hour of the day
````
SELECT 
DATEPART(HOUR,order_time) AS hour_of_the_day,
COUNT(pizza_id) as pizza_count
FROM #customer_orders 
GROUP BY DATEPART(HOUR,order_time);
````
  
### Q10. What was the volume of orders for each day of the week?

````
 SELECT 
FORMAT(DATEADD(DAY, 2, order_time),'dddd') AS day_of_week, -- add 2 to adjust 1st day of the week as Monday
COUNT(order_id) AS order_count
FROM #customer_orders
GROUP BY FORMAT(DATEADD(DAY, 2, order_time),'dddd');
  ````
  
  
  ---------------------------------------------------------------------------------------------------------------------------------------------------
  
 Runner And Customer Experience










 

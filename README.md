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
````WITH pizza_per_order_cte AS
(SELECT 
c.order_id, 
COUNT(c.pizza_id) AS pizza_count_per_order
 FROM #customer_orders c
 JOIN #runner_orders r
 ON c.order_id = r.order_id
 WHERE r.pickup_time != ''
 GROUP BY c.order_id
 )
SELECT 
MAX(pizza_count_per_order) AS max_pizza_count
FROM pizza_per_order_cte;
````
So, the maximum number of pizzas delivered in a single order is 3.

### Q7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````SELECT 
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
### Q8. How many pizzas were delivered that had both exclusions and extras?

````SELECT 
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

````SELECT 
DATEPART(HOUR,order_time) AS hour_of_the_day,
COUNT(pizza_id) as pizza_count
FROM #customer_orders 
GROUP BY DATEPART(HOUR,order_time);
````
  
### Q10. What was the volume of orders for each day of the week?

````SELECT 
FORMAT(DATEADD(DAY, 2, order_time),'dddd') AS day_of_week, -- add 2 to adjust 1st day of the week as Monday
COUNT(order_id) AS order_count
FROM #customer_orders
GROUP BY FORMAT(DATEADD(DAY, 2, order_time),'dddd');
  ````
  
  
  ---------------------------------------------------------------------------------------------------------------------------------------------------
  #👩  Runner and Customer Experience
  -

 --This file covers Solutions to Questions regarding Runner and Customer Experience.
 
  ### Q1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)  
````SELECT DATEPART(WEEK,registration_date) AS registration_week,
  COUNT(runner_id) AS runner_signups
  FROM runners
  GROUP BY DATEPART(WEEK,registration_date);
````



###  Q2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?  

 ```` SELECT r.runner_id,AVG((DATEDIFF(MINUTE,c.order_time,r.pickup_time))) AS average_arrival_time
  FROM #customer_orders 
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE r.pickup_time != ''
  GROUP BY r.runner_id;
````
  
### Q3. Is there any relationship between the number of pizzas and how long the order takes to prepare? 


 ```` WITH cooking_time_cte AS
  (
  SELECT c.order_id, COUNT(pizza_id) AS pizza_count, DATEDIFF(MINUTE,c.order_time,r.pickup_time) AS cooking_time
  FROM #customer_orders c
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE r.pickup_time != ''
  GROUP BY c.order_id, DATEDIFF(MINUTE,c.order_time,r.pickup_time)
  )
  SELECT pizza_count, AVG(cooking_time) AS avg_preparation_time
  FROM cooking_time_cte
  GROUP BY pizza_count;
````

  -- Thus, on an average , it takes 12 minutes to prepare an order of 1 pizza.
  -- It takes 18 minutes to prepare an order of 2 pizzas.
  -- And, it takes 30 minutes to prepare an order of 3 pizzas.
  -- So, there is a direct relationship between the number of pizzas oordered and the preparation time.

###  Q4. What was the average distance travelled for each customer? 

  ````SELECT c.customer_id, ROUND((AVG(r.distance)),2) AS avg_distance_travelled  --ROUND() to limit the value to 2 decimal places.
  FROM #customer_orders c
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE r.distance != 0
  GROUP BY c.customer_id;

  SELECT c.customer_id, CAST((AVG(r.distance))AS DECIMAL(5,2)) AS avg_distance_travelled -- CAST() can also do the job here but it keeps the insignificant zeros attached.
  FROM #customer_orders c
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE r.distance != 0
  GROUP BY c.customer_id;

  SELECT c.customer_id, 
  FORMAT((AVG(r.distance)),'###.##') AS avg_distance_travelled  --FORMAT() formats a number to the specified format, though it changes the data type to string.
  FROM #customer_orders c
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE r.distance != 0
  GROUP BY c.customer_id;
````
 ### Q5. What was the difference between the longest and shortest delivery times for all orders?  */

  ````SELECT order_id,duration
  FROM #runner_orders
  WHERE duration != 0;

  SELECT MAX(duration) - MIN(duration) AS delivery_time_difference
  FROM #runner_orders
  WHERE duration != 0;
````
### Q6. What was the average speed for each runner for each delivery and do you notice any trend for these values? 

```` SELECT runner_id,order_id , ROUND(AVG(distance/duration*60),2) AS avg_speed   
  FROM #runner_orders
  WHERE distance != 0
  GROUP BY order_id,runner_id;
````
 -- Distance is in KM and Duration is converted into hour. So, Speed is in KM/Hr.

  -- Runner A's average speed varies from 37.5 KM/Hr to 60 KM/Hr.
  -- Runner B's average speed varies from 35.1 KM/Hr to 93.6 KM/Hr.
  -- Runner C's average speed is 40 KM/Hr.

### Q7. What is the successful delivery percentage for each runner?  
````SELECT runner_id,
  100* SUM(CASE WHEN duration = 0 THEN 0
            ELSE 1 END)/COUNT(order_id) AS success_percentage
  FROM #runner_orders
  GROUP BY runner_id;
````

  -- Runner 1 has 100% successful delivery.
  -- Runner 2 has 75% successful delivery.
  -- Runner 3 has 50% successful delivery.

  --(It does not make sense to calculate runner's delivery success here as the unsuccessful deliveries are based on order cancellations which are out of runner's control).


  ------------------------------------------------------------------------------------------------------------------------------------
 









 

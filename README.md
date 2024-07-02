#  Pizza Case study 
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
 
# :hammer: Handling Data Quality Issues

Danny has already mentioned in the Case Study that some of the tables provided have some data quality issues.
 
 1. The columns "exclusions" and "extras" in the table "customer_orders" have null values.
 2. The columns "pickup_time", "distance" , "duration" and "cancellation" in the table "runner_orders" have null values.
 3. Both the tables "customer_orders" and "runner_orders" have issues relating to data type of the columns.

 So, first we need to deal with these data quality issues before moving on to querying and solving the Case Study questions.

 **Approach:** 
 - We first create a temporary table with the same table_name and make the required changes in the values of the columns (e.g. getting rid of nulls and NULL) 
 - Then we change the datatype of the columns to be able to perform the desired operations. And then we'll use the temporary tables for all our queries.
  
### 1. Cleaning the table "customer_orders"

**Steps:**
  -  exclusions: remove NULL and nulls and replace with ''
  -  extras: remove NULL and nulls and replace with ''

````sql
SELECT 
    order_id,customer_id,pizza_id,
    CASE 
      WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN ''
      ELSE exclusions
      END AS exclusions,
    CASE
      WHEN extras IS NULL OR extras LIKE 'null' THEN ''
      ELSE extras 
      END AS extras,
    order_time
INTO #customer_orders -- #customer_orders is a temporary table which is stored somewhere in a database.
FROM customer_orders;
````
 |Before|After|
 |-------|--------|
 |![Screenshot 2022-12-29 203413](https://user-images.githubusercontent.com/96012488/209971744-266c04ae-e1cf-41b0-b58a-65dc99d4b45c.png)|![CS 2 2](https://user-images.githubusercontent.com/96012488/209969596-5ef1f925-73dc-4554-b342-240b4348350f.png)


### 2. Cleaning the table "runner_orders"

**Step: Cleaning the values**
- pickup_time: remove nulls and replace with ''
- distance: remove km and replace nulls with ''
- duration: remove minutes and replace nulls with ''
- cancellation: remove NULL and nulls and replace with ''
 
 
````sql
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
INTO #runner_orders-- #runner_orders is a temporary table which is stored somewhere in a database.
FROM runner_orders;
````

|Before|After|
|---|---|
![Screenshot 2022-12-29 203139](https://user-images.githubusercontent.com/96012488/209972858-a68c0e17-e479-42f0-8309-021de2656589.png)|![Screenshot 2022-12-29 204103](https://user-images.githubusercontent.com/96012488/209972778-42785ab3-4288-417a-a18c-8c3b05b4253d.png)


 **Step: Changing the Dataypes**
- pickup_time: VARCHAR to DATETIME
- distance : VARCHAR to FLOAT
- duration: VARCHAR to INT

````sql
ALTER TABLE #runner_orders
ALTER COLUMN pickup_time DATETIME;

ALTER TABLE #runner_orders
ALTER COLUMN distance FLOAT;

ALTER TABLE #runner_orders
ALTER COLUMN duration INT;
````

# :pizza: Case Study #2 - Pizza Runner

## :memo: Solution A. Pizza Metrics

### 1. How many Pizzas were ordered?

````sql
SELECT 
   COUNT(pizza_id) AS pizza_order_count
FROM #customer_orders;
 ````
 **Answer:**
 
 
 ![Pizza Metrics 1](https://user-images.githubusercontent.com/96012488/187139840-1ee891f6-c891-4dfa-8275-5e703b996674.png)

 
 - 14 Pizzas were ordered in total. 
 
 ### 2. How many unique customer orders were made? 
 
 ````sql
 SELECT 
	COUNT(DISTINCT customer_id) AS unique_customer_count
 FROM #customer_orders;
 ````
 
 **Answer**
 
 ![Pizza Metrics 2](https://user-images.githubusercontent.com/96012488/187223705-a89831b2-f497-4451-ab3b-5503e9d4ab59.png)


-  5 unique customer orders were made.
 
 ### 3. How many successful orders were delivered by each runner?
 
 ````sql
 SELECT
	runner_id, 
	COUNT(pickup_time) AS successful_order_count
 FROM #runner_orders
 WHERE pickup_time != ''
 GROUP BY runner_id;
 ````
 
 **Answer**
 
 ![Pizza Metrics 3](https://user-images.githubusercontent.com/96012488/187222071-758047bb-116a-4180-944c-9811f6695439.png)

 
 ### 4. How many of each type of pizza was delivered?
 
 ````sql
 SELECT 
	c.pizza_id , 
	COUNT(r.pickup_time) AS pizza_count
 FROM #customer_orders c
 JOIN #runner_orders r
 ON c.order_id =r.order_id
 WHERE r.pickup_time != ''
 GROUP BY c.pizza_id;
 ````
 
 **Answer**
 
 ![Pizza Metrics 4](https://user-images.githubusercontent.com/96012488/187222284-440d594c-378e-4188-ae86-6fd4dd1dcff1.png)

 
 ### 5. How many Vegetarian and Meatlovers were ordered by each customer?
 
 ````sql
 SELECT
	c.customer_id, 
	CAST(p.pizza_name AS VARCHAR(20)) AS pizza_name,
	COUNT(CAST(p.pizza_name AS VARCHAR(20))) AS pizza_count
 FROM #customer_orders c
 JOIN pizza_names p                                                    
 ON c.pizza_id = p.pizza_id
 GROUP BY c.customer_id,CAST(p.pizza_name AS VARCHAR(20));        -- Here, pizza_name (TEXT) is cast as VARCHAR(20) to enable comparisons and calculations.
 ````
 
 **Answer**
 
 ![Pizza Metrics 5](https://user-images.githubusercontent.com/96012488/187222463-3e51d82b-f18c-4552-a546-0b505938199d.png)

 
### 6. What was the maximum number of pizzas delivered in a single order?

````sql
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

 SELECT 
	MAX(pizza_count_per_order) AS max_pizza_count
 FROM pizza_per_order_cte;
 ````
 
 **Answer**
 
 ![Pizza Metrics 6 1](https://user-images.githubusercontent.com/96012488/187222656-2dd47996-39cb-4a9a-b158-485efb661744.png)

![Pizza Metrics 6 2](https://user-images.githubusercontent.com/96012488/187222773-49b81fab-68ba-427e-ba39-17f3b74f425c.png)

-  The maximum number of pizzas delivered in a single order is 3.

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````sql
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
 
 **Answer**
 
 ![Pizza Metrics 7](https://user-images.githubusercontent.com/96012488/187222969-7d3e7995-d60d-4f94-866e-dc4966bbbcc6.png)


### 8. How many pizzas were delivered that had both exclusions and extras?

````sql
SELECT 
   COUNT(c.pizza_id) AS pizza_count_w_exclusions_extras
 FROM #customer_orders c
 JOIN #runner_orders r
 ON c.order_id = r.order_id
 WHERE (c.exclusions != '' AND c.extras != '') AND (r.pickup_time != '');
 ````
 
 **Answer**
 
 ![Pizza Metrics 8](https://user-images.githubusercontent.com/96012488/187223216-136799d2-fd60-4303-b698-79f547fb841d.png)

 
 -- Thus, there is just 1 pizza which had both exclusions and extras.

### 9. What was the total volume of pizzas ordered for each hour of the day?

````sql
SELECT 
  DATEPART(HOUR,order_time) AS hour_of_the_day,
  COUNT(pizza_id) as pizza_count
 FROM #customer_orders 
 GROUP BY DATEPART(HOUR,order_time);
 ````
 
 **Answer**
 
 ![Pizza Metrics 9](https://user-images.githubusercontent.com/96012488/187223368-77faea8c-36cd-4671-975d-2a80b22af75c.png)



### 10. What was the volume of orders for each day of the week?

````sql
SELECT 
  FORMAT(DATEADD(DAY, 2, order_time),'dddd') AS day_of_week, -- add 2 to adjust 1st day of the week as Monday
  COUNT(order_id) AS order_count
FROM #customer_orders
GROUP BY FORMAT(DATEADD(DAY, 2, order_time),'dddd');
 ````
 
 **Answer**
 
 ![Pizza Metrics 10](https://user-images.githubusercontent.com/96012488/187223486-1cbf4ec2-200e-40b8-b0f2-d28be93652b3.png)


# :pizza: Case Study #2 - Pizza Runner

## 📝 Solution - B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
SELECT
  DATEPART(WEEK,registration_date) AS registration_week,
  COUNT(runner_id) AS runner_signups
FROM runners
GROUP BY DATEPART(WEEK,registration_date);
````

**Answer:**

![R C Exp 1](https://user-images.githubusercontent.com/96012488/189708057-7edfdacb-65ab-45a1-9f1e-fa27e338d9f8.png)


### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql
SELECT
  r.runner_id,AVG((DATEDIFF(MINUTE,c.order_time,r.pickup_time))) AS average_arrival_time
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.pickup_time != ''
GROUP BY r.runner_id;
````

**Answer:**

![R C Exp 2](https://user-images.githubusercontent.com/96012488/189708604-8243dcff-c93a-4072-8a97-0a76510f05ba.png)


### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql
WITH cooking_time_cte AS
  (
  SELECT c.order_id, COUNT(pizza_id) AS pizza_count, DATEDIFF(MINUTE,c.order_time,r.pickup_time) AS cooking_time
  FROM #customer_orders c
  JOIN #runner_orders r
  ON c.order_id = r.order_id
  WHERE r.pickup_time != ''
  GROUP BY c.order_id, DATEDIFF(MINUTE,c.order_time,r.pickup_time)
  )
  SELECT 
    pizza_count, AVG(cooking_time) AS avg_preparation_time
  FROM cooking_time_cte
  GROUP BY pizza_count;
  ````
  
  **Answer:**
  
  ![R C Exp 3](https://user-images.githubusercontent.com/96012488/189709415-c7be5bd8-de5e-4c46-a5aa-8c837395a9f6.png)

  * Thus, on an average , it takes 12 minutes to prepare an order of 1 pizza.
  * It takes 18 minutes to prepare an order of 2 pizzas.
  * And, it takes 30 minutes to prepare an order of 3 pizzas.
  * So, there is a direct relationship between the number of pizzas oordered and the preparation time.


### 4.  What was the average distance travelled for each customer?

````sql
SELECT 
  c.customer_id, ROUND((AVG(r.distance)),2) AS avg_distance_travelled  --ROUND() to limit the value to 2 decimal places.
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.customer_id;
````

````sql
SELECT 
  c.customer_id, CAST((AVG(r.distance))AS DECIMAL(5,2)) AS avg_distance_travelled -- CAST() can also do the job here but it keeps the insignificant zeros attached.
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.customer_id;

 SELECT 
    c.customer_id, 
    FORMAT((AVG(r.distance)),'###.##') AS avg_distance_travelled  --FORMAT() formats a number to the specified format, though it changes the data type to string.
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.customer_id;
````

````sql
--Any of the above three functions can be used to get the values with desired decimal places but each of them have their own functionalities.
````
  
  **Answer:**
  
  ![R C Exp 4](https://user-images.githubusercontent.com/96012488/189710374-db11bf2e-e306-46cb-acc1-6e69023f9be7.png)

### 5. What was the difference between the longest and shortest delivery times for all orders?

````sql
SELECT 
  order_id,duration
FROM #runner_orders
WHERE duration != 0;

  
SELECT 
  MAX(duration) - MIN(duration) AS delivery_time_difference
FROM #runner_orders
WHERE duration != 0;
````

**Answer:**

![R C Exp 5 1](https://user-images.githubusercontent.com/96012488/189710884-2b45fe71-c091-4fa1-9897-6219f854143e.png)

![R C Exp 5 2](https://user-images.githubusercontent.com/96012488/189710928-5edd9d09-7d05-426f-87ac-5bff05a11c9c.png)


### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql
SELECT 
  runner_id,order_id , ROUND(AVG(distance/duration*60),2) AS avg_speed   
FROM #runner_orders
WHERE distance != 0
GROUP BY order_id,runner_id;
````

**Answer:**

![R C Exp 6](https://user-images.githubusercontent.com/96012488/189711222-c274b863-719a-4477-9206-7dd681a17048.png)

````sql
-- Distance is in KM and Duration is converted into hour. So, Speed is in KM/Hr.
````

* Runner A's average speed varies from 37.5 KM/Hr to 60 KM/Hr.
* Runner B's average speed varies from 35.1 KM/Hr to 93.6 KM/Hr.
* Runner C's average speed is 40 KM/Hr.

### 7. What is the successful delivery percentage for each runner?

````sql
SELECT 
  runner_id,
  100* SUM(CASE WHEN duration = 0 THEN 0
            ELSE 1 END)/COUNT(order_id) AS success_percentage
FROM #runner_orders
GROUP BY runner_id;
````


**Answer:**

![R C Exp 7](https://user-images.githubusercontent.com/96012488/189711446-3fb33474-ec30-436d-8884-bdb594d2555c.png)


* Runner 1 has 100% successful delivery.
* Runner 2 has 75% successful delivery.
* Runner 3 has 50% successful delivery.

# :pizza: Case Study #2 - Pizza Runner

## 📝 Solution C. Ingredient Optimization

*In this section, I first got my hands dirty with solving each query in bits and pieces. Then, I realised there are certain results(tables) which are repeatedly used in many queries. So, I created them as temporary tables.*
  
*While solving the queries, I also realised that data type of certain TEXT columns needs to be converted to Character type (Nvarchar) so that the various String Functions (like STRING_SPLIT(), STRING_AGG(), and CONCAT_WS()) can be performed on them.*

### Required Data Type Transformations 

- 1. Creating a temporary table `#pizza_recipes` and changing the data type of `toppings` from **TEXT** to **NVARCHAR(100)** to be able to perform STRING_SPLIT() on it.

````sql
SELECT pizza_id , toppings
INTO #pizza_recipes
FROM pizza_recipes;

ALTER TABLE #pizza_recipes
ALTER COLUMN toppings NVARCHAR(100);
````
- 2. Creating a temporary table `#pizza_toppings` and changing the data type of `topping_name` from **TEXT** to **NVARCHAR(100)** to be able to perform STRING_AGG() on it.

````sql
SELECT topping_id,topping_name
INTO #pizza_toppings
FROM pizza_toppings;

ALTER TABLE #pizza_toppings
ALTER COLUMN topping_name NVARCHAR(100);
````
- 3. Creating a temporary table `#pizza_names` and changing the data type of `pizza_name` from **TEXT** to **NVARCHAR(100)** to be able to perform CONCAT_WS() on it.

````sql
SELECT * 
INTO #pizza_names
FROM pizza_names;

ALTER TABLE #pizza_names
ALTER COLUMN pizza_name NVARCHAR(100);
````

*** 
*We need to create a few temporary tables with more simple and clear formats using the given tables, so that we can easily answer the given questions.*

### Required Temporary tables 

**1.**  Creating the `#pizzas_with_toppings` table (*It contains pizza ids with separated topping ids and separated topping names with one row for each topping name*).
- STRING_SPLIT() is a user_defined table_valued function that splits a string array into multiple rows of substrings based on the specified separator.
- Input_string (1st argument for this function) should be of the type NVARCHAR, VARCHAR, NCHAR or CHAR.

````sql
SELECT	
 r.pizza_id,TRIM(t.value) AS topping_id,pt.topping_name  
--TRIM() removes any leading or trailing spaces and makes sure the substrings are aligned properly.
INTO #pizzas_with_toppings
FROM #pizza_recipes as r
CROSS APPLY STRING_SPLIT(r.toppings, ',') as t
JOIN #pizza_toppings pt
ON TRIM(t.value) = pt.topping_id;
````
**Before:**

|pizza_recipes|pizza_toppings|
|---|---|
|![Screenshot 2022-12-31 140551](https://user-images.githubusercontent.com/96012488/210130640-70a025ec-e642-4831-a49c-5b08d0c8645d.png)|![Screenshot 2022-12-31 140635](https://user-images.githubusercontent.com/96012488/210130657-104f4396-0d93-4a6f-8461-31ab08e558ac.png)|

**After:   `pizzas_with_toppings`**

![Screenshot 2022-12-31 140130](https://user-images.githubusercontent.com/96012488/210130552-f9b319c6-5aa2-4182-866c-4d541d10da44.png)

**2.**  Adding a column `record_id` to the table `#customer_orders` to select each pizza in an order individually, using IDENTITY() function.
- IDENTITY() is a function used to create auto-incrementing columns, i.e., the value of the column increases automatically everytime a new row is inserted into the table.
- It has two optional arguments Seed and Step, default values for both is 1.

````sql
ALTER TABLE #customer_orders
ADD record_id INT IDENTITY(1,1)
````
| Before|After|
|---|---|
|![Screenshot 2022-12-31 141736](https://user-images.githubusercontent.com/96012488/210130906-5a5d2ffc-5b51-4841-a827-3d3231a68564.png)|![Screenshot 2022-12-31 141930](https://user-images.githubusercontent.com/96012488/210130942-7d14e085-b252-427a-9ddc-6d44eeb52797.png)|



**3.** Creating the table `records_with_exclusions` (*It contains record_ids and exclusions for each record*).

````sql
SELECT 
 record_id, value AS exclusions
INTO #records_with_exclusions
FROM #customer_orders c
CROSS APPLY STRING_SPLIT(c.exclusions,',') 
WHERE exclusions != '';
````

![Screenshot 2022-12-31 142124](https://user-images.githubusercontent.com/96012488/210130980-c378d835-cf1a-4b11-b0ca-7dcf5bbcfca5.png)


**4.**  Creating the table` records_with_extras` (*It contains record_ids and extras for each record*)

````sql
SELECT 
 record_id, value AS extras
INTO #records_with_extras
FROM #customer_orders c
CROSS APPLY STRING_SPLIT(c.extras,',') 
WHERE extras != '';
````

![Screenshot 2022-12-31 142209](https://user-images.githubusercontent.com/96012488/210130991-f06f5273-ed24-41d8-bdaf-5ca702122673.png)

***

***Let's answer the Questions now!***

#### 1. What are the standard ingredients for each pizza?

**Approach:**
- We will use the newly created temporary table #pizzasa_with_toppings which contains pizza ids with separated topping ids and separated topping names.
- Using this table, we will retrieve a string array consisting of topping names for each of the Pizza id.
- To create a string array of topping names, we need to perform STRING_AGG() on it.
- STRING_AGG() is a user-defined function that concatenates rows of strings into a single string, separated by a specified separator.

````sql
SELECT 
 pizza_id, STRING_AGG(topping_name,', ') AS Toppings
FROM #pizzas_with_toppings
GROUP BY pizza_id;
````

**Answer:**

![Screenshot 2022-12-31 142652](https://user-images.githubusercontent.com/96012488/210131121-00cfd5d9-85c5-4a84-aed7-08196c004ffa.png)


#### 2. What was the most commonly added extra?  

**Approach:**
- We will use the temporary table #records_with_extras and join it with #pizza_toppings for topping_name.

````sql
SELECT 
 pt.topping_name, COUNT(r.record_id) AS number_of_addition
FROM #records_with_extras r
JOIN #pizza_toppings pt
ON r.extras = pt.topping_id
GROUP BY pt.topping_name;
````
**Answer:**

![Screenshot 2022-12-31 142804](https://user-images.githubusercontent.com/96012488/210131142-d6bd8fbe-0740-490d-af51-3094b6fbff7f.png)

- Bacon was the most commonly added extra.

#### 3. What was the most common exclusion?  

**Approach:**
- We will use the temporary table #records_with_exclusions and join it with #pizza_topping for topping_name.

````sql
SELECT 
 pt.topping_name, COUNT(r.record_id) AS number_of_removals
FROM #records_with_exclusions r
JOIN #pizza_toppings pt
ON r.exclusions = pt.topping_id
GROUP BY pt.topping_name;
````

**Answer:**

![Screenshot 2022-12-31 142838](https://user-images.githubusercontent.com/96012488/210131160-a796435c-0583-4bb7-9572-50f42de2a621.png)

- Cheese was the most common exclusion.


#### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
####    Meat Lovers
####    Meat Lovers - Exclude Beef
####    Meat Lovers - Extra Bacon
####    Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers   

**Approach:**
- Here, we are using 3 ctes: exclusions_cte, extras_cte and union_cte. Then we join these with #customer_orders and pizza_names to get the desired result.
- exclusions_cte contains record_id and the exclusions in the 'Exclude ... ' format.
- extras_cte contains record_id and the extras in the 'Extra ... ' format.
- union_cte contains the union of the two ctes.

- And then, we have used CONCAT_WS() function to obtain the pizza_name and optimisation in the required format for each order item.
- CONCAT_WS() function concatenates two or more strings into one string with a separator. CONCAT_WS() means concatenate with separator. Again, the input_string should be NVARCHAR, VARCHAR etc.

````sql
WITH exclusions_cte AS
(
SELECT 
 r.record_id, 'Exclude ' + STRING_AGG(pt.topping_name, ', ') AS optimisation
FROM #records_with_exclusions r
JOIN #pizza_toppings pt
ON r.exclusions = pt.topping_id
GROUP BY r.record_id
),

extras_cte AS

(
SELECT 
 r.record_id, 'Extra ' + STRING_AGG(pt.topping_name, ', ') AS optimisation
FROM #records_with_extras r
JOIN #pizza_toppings pt
ON r.extras = pt.topping_id
GROUP BY r.record_id
),

union_cte AS
(
SELECT * FROM exclusions_cte
UNION
SELECT * FROM extras_cte
) 

SELECT 
 c.record_id,
 CONCAT_WS('-', p.pizza_name, STRING_AGG(optimisation, '-')) AS desired format
FROM #customer_orders c
JOIN #pizza_names p
ON c.pizza_id = p.pizza_id
LEFT JOIN union_cte cte
ON c.record_id = cte.record_id
GROUP BY c.record_id,p.pizza_name
ORDER BY c.record_id;
````
**Answer:**

![Screenshot 2022-12-31 143135](https://user-images.githubusercontent.com/96012488/210131210-e459e7b5-e811-4ee1-970d-4ca72554e102.png)


#### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
#### For example: "Meat Lovers: 2xBacon, Beef, ... , Salami" 

**Approach:**
- Here, we create a cte by joining three tables $customer_orders, #pizza_names and #pizzas_with_toppings so that we have each of the topping name for each of the record.
- Then, we create a case based column 'toppings' where if the ingredient is an extra for a specific record, it should have 2x in front of it. Else, just the topping name.
- And, by the WHERE clause we make sure that if an ingredient is excluded for a record, it should not show up in the toppings column.
- At the end, CONCAT_WS() function is used to get the list of ingredients in the desired format for each order item.

````sql
WITH ingredients_cte AS
(
SELECT
 c.record_id, p.pizza_name,
 CASE WHEN pt.topping_id
 IN (SELECT extras FROM #records_with_extras r WHERE c.record_id = r.record_id) 
 --Here, c.record_id = r.record_id to make sure we are giving 2x to extras for the specific records and not all the records
 THEN '2x ' + pt.topping_name
 ELSE pt.topping_name
 END AS toppings
FROM #customer_orders c
JOIN #pizza_names p
ON c.pizza_id = p.pizza_id
JOIN #pizzas_with_toppings pt
ON c.pizza_id = pt.pizza_id
WHERE pt.topping_id NOT IN (SELECT exclusions FROM #records_with_exclusions r WHERE c.record_id = r.record_id) 
--Here, WHERE c.record_id = r.record_id is like there are some records which have exclusions and for those records, the topping_id should not be in the record's excluded list of toppings.
)
SELECT 
 record_id, CONCAT_WS(' :', pizza_name, STRING_AGG(toppings, ', ')) AS ingredients_list
FROM ingredients_cte
GROUP BY record_id,pizza_name
ORDER BY record_id;
````
- *Note: In this query, we have used the same alias 'r' for both the tables #record_with_extras and #records_with_exclusions but in different subqueries and that works just fine.*

**Answer:**

![Screenshot 2022-12-31 143259](https://user-images.githubusercontent.com/96012488/210131239-0731b809-6682-426d-b7cf-0aaa2099cf38.png)


#### 6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first? 

**Approach:**
- Here, the approach for cte is very similar to the previous question. 
- But to include only the delivered pizzas, I have added another table #runner_orders and added a WHERE clause with pickup_time not equal to ''.
- The case based column 'times_used' is such that for an extra, the value is 2, for an exclusion, the value is 0 and for a normal ingredient, the value is 1.
- At the end , we have just summed up the 'times_used' column , grouping by 'ingredient' to  get the total_count of number of times an ingredient is used.

````sql
WITH count_cte AS
(
SELECT
 record_id,topping_name AS ingredient,
 CASE -- if extra ingredient add 2
 WHEN pt.topping_id 
 IN (SELECT extras FROM #records_with_extras r WHERE c.record_id = r.record_id) 
 THEN 2 
 -- if exclusion ingredient add 0
 WHEN pt.topping_id
 IN (SELECT exclusions FROM #records_with_exclusions r WHERE c.record_id = r.record_id)
 THEN 0
 -- if normal ingredient add 1
 ELSE 1
 END AS times_used
FROM #customer_orders c
JOIN #pizzas_with_toppings pt
ON c.pizza_id = pt.pizza_id
LEFT JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.pickup_time != ''
)
SELECT 
 ingredient, SUM(times_used) AS total_count
FROM count_cte
GROUP BY ingredient
ORDER BY total_count DESC;
````

**Answer:**

![Screenshot 2022-12-31 143351](https://user-images.githubusercontent.com/96012488/210131260-ed356e25-e03d-414a-abc4-fd7f775b48d5.png)

- Thus, **Mushroom** is the most frequently used ingredient.

### Learnings  

-  Various new STRING Manipulation Functions like STRING_SPLIT(), STRING_AGG() , CONCAT with + and CONCAT_WS()
-  The tables provided were not in the format that you can directly fetch the results from. I had to create many new tables, by combining the existing tables, in the desired formats to get the desired results.
-  It was not easy to picture the required CTEs in mind as the designs were quite complex involving many tables and newly created temporary tables. 
So, my approach was this: first what columns do I need, which tables will i fetch those columns from and then how to join all those tables and then any required conditions.

# :pizza: Case Study #2 - Pizza Runner

## 📝 Solution - D. Pricings and Ratings

#### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes how much money has Pizza Runner made so far if there are no delivery fees?

````sql
WITH revenue_cte AS
(	
SELECT 
   p.pizza_name,
   CASE WHEN c.pizza_id = 1 THEN 12*COUNT(c.pizza_id)
   ELSE 10*COUNT(c.pizza_id) 
   END AS Revenue
FROM #customer_orders c
JOIN #pizza_names p
ON c.pizza_id = p.pizza_id
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.pizza_id,p.pizza_name
)
SELECT 
  SUM(Revenue) AS Total_Revenue
FROM revenue_cte;
````

**Answer:**

![Screenshot 2022-12-31 170403](https://user-images.githubusercontent.com/96012488/210135187-d1025a35-837d-44c1-895e-214819617cb0.png)

#### 2. What if there was an additional $1 charge for any pizza extras? Add cheese is $1 extra.

**Approach:**

- I have created two CTEs, one for the revenue for each kind of pizzas without any other charges and another for the extras charges for each kind of pizza.
- Then, I have joined the two CTEs on 'pizza_name' column and added the Revenue without any other charges and the extra charges.

````sql
WITH revenue_cte AS
(
SELECT 
  p.pizza_name,
  CASE WHEN c.pizza_id = 1 THEN 12*COUNT(c.pizza_id)
  ELSE 10*COUNT(c.pizza_id) 
  END AS Revenue
FROM #customer_orders c
JOIN #pizza_names p
ON c.pizza_id = p.pizza_id
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.pizza_id,p.pizza_name
)
, extras_charges_cte AS
(
SELECT 
  p.pizza_name,COUNT(re.extras) AS extras_charges
FROM #records_with_extras re
JOIN #customer_orders c
ON c.record_id = re.record_id
LEFT JOIN #runner_orders r
ON c.order_id = r.order_id
JOIN #pizza_names p
ON c.pizza_id = p.pizza_id
WHERE r.distance != 0
GROUP BY p.pizza_name
)
SELECT 
  (SUM(Revenue) + SUM(extras_charges)) AS Total_Revenue_with_extras_charges
FROM revenue_cte r
JOIN extras_charges_cte e
ON r.pizza_name = e.pizza_name
````

**Answer:**

![Screenshot 2022-12-31 170756](https://user-images.githubusercontent.com/96012488/210135312-e3f34f40-eeed-4e43-8e3f-88049807d203.png)

#### 3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.

**Approach:**
- The ratings table should contain 4 columns: runner_id,order_id,customer_id and the rating.

````sql
-- Creating table `ratings`

DROP TABLE IF EXISTS ratings;
CREATE TABLE ratings
(
runner_id INT,
order_id INT,
customer_id INT,
rating INT 
);


-- Inserting values

INSERT INTO ratings(runner_id,order_id,customer_id,rating)
VALUES  (1,1,101,5),
        (1,2,101,4),
        (1,3,102,5),
        (2,4,103,4),
        (3,5,104,5),
        (2,7,105,4),
        (2,8,102,5),
        (1,10,104,3);
````

**Answer:**

![Screenshot 2022-12-31 171155](https://user-images.githubusercontent.com/96012488/210135416-9853f23c-4ee6-48c6-80e0-58a7be2c2e10.png)

#### 4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
      
-  **customer_id**
-  **order_id**
-  **runner_id**
-  **rating**
-  **order_time**
-  **pickup_time**
-  **Time between order and pickup**
-  **Delivery duration**
-  **Average speed**
-  **Total number of pizzas**

````sql
SELECT 
  c.customer_id,
  c.order_id,
  r.runner_id,
  ra.rating,
  c.order_time,
  r.pickup_time, 
  DATEDIFF(MINUTE,c.order_time,r.pickup_time) AS Time_between,
  r.duration,
  ROUND((r.distance/r.duration*60),2)AS average_speed,
  COUNT(c.pizza_id) AS pizza_count
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id =r.order_id
JOIN ratings ra
ON c.order_id = ra.order_id
WHERE r.duration != 0
GROUP BY c.order_id,c.customer_id,r.runner_id,ra.rating,c.order_time,r.pickup_time,r.duration,DATEDIFF(MINUTE,c.order_time,r.pickup_time),r.duration,(r.distance/r.duration*60)
````
**Answer:**

![Screenshot 2022-12-31 171827](https://user-images.githubusercontent.com/96012488/210135616-616268ca-bd59-4513-adcd-008d9568bc41.png)

#### 5.  If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?

**Approach:**
- I have created two CTEs. One for the revenue per order per pizza_id and another for the overall delivery fee. 
- Then, I subtracted the overall delivery fee from the revenue to get the net profit.

````sql
WITH revenue_cte AS
(	
SELECT 
  c.order_id,
  CASE WHEN c.pizza_id = 1 THEN 12*COUNT(c.pizza_id)
  ELSE 10*COUNT(c.pizza_id) 
  END AS Revenue
FROM #customer_orders c
JOIN #runner_orders r
ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.order_id,c.pizza_id,r.distance
)
,delivery_cte AS
(
SELECT 
  SUM((0.3*distance)) AS delivery_fee
FROM #runner_orders
WHERE distance != 0
)
SELECT 
  (SUM(Revenue) - delivery_fee) AS net_profit
FROM revenue_cte, delivery_cte
GROUP BY delivery_fee;
````
	
- Here, I have not joined the two ctes. I have simply selected the required columns from the two ctes separating them with a comma in the FROM clause.

#### Bonus Question: If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu? 

- The addition of a newSupreme Pizza to the pizza_runner's menu will affect the design of two tables in the database, `pizza_names` and `pizza_recipes`.

````sql
INSERT INTO pizza_names (pizza_id,pizza_name)
VALUES(3,'Supreme');

INSERT INTO pizza_recipes (pizza_id, toppings)
VALUES(3, '1, 2, 3, 4, 5, 6, 7, 8 ,9, 10, 11, 12');

-- Let's see how the new addition has modified the two tables.

SELECT * FROM pizza_names;
SELECT * FROM pizza_recipes;
````

**Result:**

| Before|After|
|---|---|
|![Screenshot 2022-12-31 172641](https://user-images.githubusercontent.com/96012488/210135898-baad0c26-2827-40f9-872a-c77807a4a21f.png)|![Screenshot 2022-12-31 172703](https://user-images.githubusercontent.com/96012488/210135935-b4206100-dc7b-4c4b-bb96-d0a219503a21.png)|
|![Screenshot 2022-12-31 172723](https://user-images.githubusercontent.com/96012488/210135951-8a5a1f85-07bc-4e3f-b0e8-4f792a93b626.png)|![Screenshot 2022-12-31 172745](https://user-images.githubusercontent.com/96012488/210135957-cef7ba69-f8e5-441b-904f-3a5a4e38136e.png)|












 

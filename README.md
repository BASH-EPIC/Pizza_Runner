# Pizza_Runner


The link of the case study are as follow's:
https://8weeksqlchallenge.com/case-study-2/

TABLE Information :

CREATE SCHEMA pizza_runner;
SET search_path = pizza_runner;

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


DROP TABLE IF EXISTS customer_orders;
CREATE TABLE customer_orders (
  "order_id" INTEGER,
  "customer_id" INTEGER,
  "pizza_id" INTEGER,
  "exclusions" VARCHAR(4),
  "extras" VARCHAR(4),
  "order_time" TIMESTAMP
);

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


DROP TABLE IF EXISTS runner_orders;
CREATE TABLE runner_orders (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "pickup_time" VARCHAR(19),
  "distance" VARCHAR(7),
  "duration" VARCHAR(10),
  "cancellation" VARCHAR(23)
);

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
  
  
  ANSWER'S :

-- How many pizzas were ordered?

SELECT count(order_id) as total_orders
FROM customer_orders;

SELECT COUNT(order_id) AS total_orders
FROM pizza_runner.customer_orders;

--How many unique customer orders were made?

SELECT COUNT(DISTINCT order_id) AS unique_orders
FROM customer_orders;

--How many successful orders were delivered by each runner?

SELECT runner_id, COUNT(*) AS successful_orders
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;

--How many of each type of pizza was delivered?
SELECT pn.pizza_name, COUNT(*) AS total_pizzas
FROM customer_orders co
JOIN pizza_names pn ON co.pizza_id = pn.pizza_id
GROUP BY pn.pizza_name;

--How many Vegetarian and Meatlovers were ordered by each customer?
SELECT cn.customer_id, pn.pizza_name, COUNT(*) AS total_pizzas
FROM customer_orders cn
JOIN pizza_names pn ON cn.pizza_id = pn.pizza_id
WHERE pn.pizza_name IN ('Vegetarian', 'Meatlovers')
GROUP BY cn.customer_id, pn.pizza_name;

--What was the maximum number of pizzas delivered in a single order?
SELECT MAX(order_count) AS max_pizzas
FROM (
  SELECT order_id, COUNT(*) AS order_count
  FROM customer_orders
  GROUP BY order_id
) AS subquery;

--For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
SELECT customer_id,
  COUNT(CASE WHEN exclusions <> '' OR extras <> '' THEN 1 END) AS pizzas_with_changes,
  COUNT(CASE WHEN exclusions = '' AND extras = '' THEN 1 END) AS pizzas_without_changes
FROM customer_orders
GROUP BY customer_id;

--How many pizzas were delivered that had both exclusions and extras?
SELECT COUNT(*) AS pizzas_with_exclusions_and_extras
FROM customer_orders
WHERE exclusions <> '' AND extras <> '';

---What was the total volume of pizzas ordered for each hour of the day?

SELECT DATE_PART('hour', order_time) AS hour_of_day, COUNT(*) AS total_pizzas
FROM customer_orders
GROUP BY hour_of_day
ORDER BY hour_of_day;

--What was the volume of orders for each day of the week?

SELECT EXTRACT(DOW order_time) AS day_of_week, COUNT(*) AS total_pizzas
FROM customer_orders
GROUP BY day_of_week
ORDER BY day_of_week;

----------------B. Runner and Customer Experience

------ How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01) ?
SELECT DATE_TRUNC('week', registration_date) AS runners_week, COUNT(*) AS weekly_runners
FROM runners
GROUP BY runners_week
ORDER BY runners_week;

------What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

SELECT ro.runner_id, AVG(EXTRACT(EPOCH FROM ro.pickup_time - co.order_time) / 60) AS avg_pickup_time_minutes
FROM runner_orders ro
JOIN customer_orders co ON ro.order_id = co.order_id
GROUP BY ro.runner_id;

------Is there any relationship between the number of pizzas and how long the order takes to prepare?

SELECT co.pizza_id, COUNT(*) AS num_pizzas,AVG(EXTRACT(EPOCH FROM ro.pickup_time - cn.order_time) / 60) AS avg_pickup_time_minutes
FROM customer_orders cn
FROM customer_orders co
JOIN runner_orders ro on co.order_id = ro.order_id
JOIN pizza_names pn ON cn.pizza_id = pn.pizza_id
GROUP BY co.pizza_id
ORDER BY co.pizza_id;

------What was the average distance travelled for each customer?

SELECT co.customer_id,AVG(ro.duration) as average_distance
FROM customer_orders co
JOIN runner_orders ro ON co.order_id = ro.order_id
GROUP BY co.customer_id
ORDER BY co.customer_id;

------What was the difference between the longest and shortest delivery times for all orders?

SELECT MAX(ro.duration) - MIN(ro.duration) AS time_difference
FROM customer_orders co
JOIN runner_orders ro ON co.order_id = ro.order_id;


------What was the average speed for each runner for each delivery and do you notice any trend for these values?


------What is the successful delivery percentage for each runner?
SELECT ro.runner_id, 
   COUNT(ro.order_id) as runners_orders
      COUNT(ro.cancellation)  FILTER (WHERE ro.cancellation IS NULL) AS successful_orders,
FROM runner_orders ro
GROUP BY ro.runner_id;


------ What are the standard ingredients for each pizza?

select pn.pizza_id,pn.toppings
from pizza_names 
JOIN pizza_recipes pr ON pn.pizza_id = pn.pizza_id ;

------ What was the most commonly added extra?

SELECT extras, COUNT(*) AS count
FROM customer_orders
WHERE extras IS NOT NULL
GROUP BY extras
ORDER BY count DESC
LIMIT 1;


------ What was the most common exclusion?

SELECT DISTINCT(exclusions) as exclusions, COUNT(*) AS count
FROM customer_orders
WHERE exclusions IS NOT NULL
GROUP BY exclusions
ORDER BY count DESC
LIMIT 1;

------ Generate an order item for each record in the customers_orders table in the format of one of the following:
------ Meat Lovers
------ Meat Lovers - Exclude Beef
------ Meat Lovers - Extra Bacon
------ Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

SELECT 
  CONCAT(pn.pizza_name, 
         CASE WHEN co.exclusions IS NOT NULL THEN CONCAT(' - Exclude ', pt1.topping_name) ELSE '' END,
         CASE WHEN co.extras IS NOT NULL THEN CONCAT(' - Extra ', pt2.topping_name) ELSE '' END) AS order_item
FROM customer_orders co
JOIN pizza_names pn ON co.pizza_id = pn.pizza_id
LEFT JOIN pizza_toppings pt1 ON co.exclusions = pt1.topping_id
LEFT JOIN pizza_toppings pt2 ON co.extras = pt2.topping_id;


---- What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

SELECT
  pt.topping_name,
  COUNT(*) AS total_quantity
FROM
  customer_orders co
JOIN
  pizza_recipes pr ON co.pizza_id = pr.pizza_id
JOIN
  pizza_toppings pt ON pt.topping_id = ANY(string_to_array(pr.toppings, ', '))
GROUP BY
  pt.topping_name
ORDER BY
  total_quantity DESC;
  
  ---------- D. Pricing and Ratings

 ---- If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
 
SELECT SUM(
  CASE
    WHEN pn.pizza_name = 'Meatlovers' THEN 12
    WHEN pn.pizza_name = 'Vegetarian' THEN 10
    ELSE 0
  END
) AS total_revenue
FROM customer_orders co
JOIN pizza_names pn ON co.pizza_id = pn.pizza_id;

----What if there was an additional $1 charge for any pizza extras?Add cheese is $1 extra

SELECT count(*)as additional_charges,
  sum(case when extras <> '' then 1 else 0 end) _sum(case when extras LIKE '%Cheese%') as THEN 1 ELSE 0 END
FROM customer_orders;

----The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.

DROP TABLE IF EXISTS runners_ratings;
CREATE TABLE runners_ratings (
  runner_id INTEGER,
  pickup_time TIMESTAMP,
  duration TIME,
  ratings INTEGER
);
INSERT INTO runners_ratings (runner_id, pickup_time, duration, ratings)
VALUES
  (1, '2023-05-27 10:15:00', '00:30:00', 4),
  (2, '2023-05-27 11:30:00', '00:25:00', 5),
  (3, '2023-05-27 12:45:00', '00:40:00', 3),
  (4, '2023-05-27 13:55:00', '00:35:00', 4),
  (5, '2023-05-27 14:10:00', '00:20:00', 2);


----If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?

SELECT
  (SUM(CASE
        WHEN pn.pizza_name = 'Meat Lovers' THEN 12
        WHEN pn.pizza_name = 'Vegetarian' THEN 10
        ELSE 0
      END) - (0.30 * SUM(ro.distance))) AS remaining_amount
FROM
  customer_orders co
  JOIN pizza_names pn ON co.pizza_id = pn.pizza_id
  JOIN runner_orders ro ON co.order_id = ro.order_id;



----Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
--- customer_id
--- order_id
--- runner_id
--- rating
--- order_time
--- pickup_time
--- Time between order and pickup
--- Delivery duration
--- Average speed
--- Total number of pizzas

SELECT co.customer_id,
       co.order_id,
       ro.runner_id,
       rr.rating,
       co.order_time,
       ro.pickup_time,
       EXTRACT(EPOCH FROM (ro.pickup_time - co.order_time)) / 60 AS time_between_order_and_pickup,
       ro.duration AS delivery_duration,
       (CAST(REPLACE(ro.distance, 'km', '') AS FLOAT) / ro.duration) * 60 AS average_speed,
       COUNT(*) AS total_number_of_pizzas
FROM customer_orders co
JOIN runner_orders ro ON co.order_id = ro.order_id
JOIN runner_ratings rr ON co.order_id = rr.order_id
WHERE ro.cancellation IS NULL
GROUP BY co.customer_id, co.order_id, ro.runner_id, rr.rating, co.order_time, ro.pickup_time, ro.duration, ro.distance;


--- -- Add Supreme pizza to pizza_names table

INSERT INTO pizza_names(pizza_names)
VALUES('Supreme')
RETURNING pizza_id;


----INSERT VALUES

INSERT INTO pizza_recipes (pizza_id, toppings)
VALUES (
  (SELECT pizza_id FROM pizza_names WHERE pizza_name = 'Supreme'),
  'Bacon, Beef, Cheese, Mushroom, Onion, Peppers, Salami'
);


  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

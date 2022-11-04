#### Case Study 2 
### 🍕 Pizza Runner Part 1

Source : <a href='https://8weeksqlchallenge.com/case-study-2/' target='_blank'>8 Week SQL Challenge</a>
***

<img src='images/pizza.png' style='width:500px'>

<a id='top'></a>

#### Table of content

* [Creating Tables](#tables)
* <a href='case_study_2_pizza_part_2.ipynb'>Data Cleaning</a>
* Case Questions <br>
This case study has LOTS of questions - they are broken up by area of focus including:
    - <a href='case_study_2_pizza_part_3.ipynb'> Pizza Metrics </a>
    - <a href='case_study_2_pizza_part_3.ipynb#experience'> Runner and Customer Experience </a>
    - <a href='case_study_2_pizza_part_4.ipynb#ingredient'> Ingredient Optimisation</a>
    - <a href='case_study_2_pizza_part_5.ipynb#price'> Pricing and Ratings </a>


Load Extension


```python
%load_ext sql
```

Creating database file with name **runner.sqlite** 


```python
%sql sqlite:///datasets/pizza_runner.sqlite
```

<a id='tables'></a>


    
### Table 1 : runners

Listing each runner's registration date


```sql
%%sql
DROP TABLE IF EXISTS runners;
CREATE TABLE runners(
    "runner_id" INTERGER,
    "registration_date" DATE
);
INSERT INTO runners
    ("runner_id","registration_date")
VALUES
    (1,'2021-01-01'),
    (2,'2021-01-03'),
    (3,'2021-01-08'),
    (4,'2021-01-15');
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    4 rows affected.
    




    []



### Table 2 : customer_orders

Customer pizza order is captured in <font color='blue'>customer_orders </font>table with 1 row for each individual pizza that is part of order.

Customer can order multiple pizzas in single order with varying **exclusions** and **extras**

**exclusions** = list **ingredient_id** values which should be removie from pizza <br>
**extras** = list **ingredient_id** values which need to be added to the pizza


```sql
%%sql
DROP TABLE IF EXISTS customer_orders;
CREATE TABLE customer_orders(
    "order_id" INTEGER,
    "customer_id" INTEGER,
    "pizza_id" INTEGER,
    "exclusions" VARCHAR(4),
    "extras" VARCHAR(4),
    "order_time" TIMESTAMP
);
INSERT_INTO customer_orders
    ("order_id","customer_id","pizza_id","exclusions","extras","order_time")
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

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    (sqlite3.OperationalError) near "INSERT_INTO": syntax error
    [SQL: INSERT_INTO customer_orders
        ("order_id","customer_id","pizza_id","exclusions","extras","order_time")
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
      ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');]
    (Background on this error at: http://sqlalche.me/e/13/e3q8)
    

### Table 3: runner_orders

After each orders are received through system- they are assigned to runner. However not all orders are fully completed and can be cancelled by restaurant or the customer.

**pickup_time** is the timestamp at which runner arrive at the pizza runner headquarter to pick up freshly cooked pizza. The **distance** and **duration** fields are related to how far and long the runnerr had to travel to deliver the order to the respective customer


```sql
%%sql
DROP TABLE IF EXISTS runner_orders;

CREATE TABLE runner_orders(
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
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    10 rows affected.
    




    []




```sql
%%sql
DROP TABLE runners_orders
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




    []



### Table 4 : pizza_names

Pizza Runner only has 2 pizzas availabel 


```sql
%%sql
DROP TABLE IF EXISTS pizza_names;

CREATE TABLE pizza_names(
    "pizza_id" INTEGER,
    "pizza_name" TEXT
);
INSERT INTO pizza_names
    ("pizza_id","pizza_name")
VALUES
    (1, 'Meatlovers'),
    (2, 'Vegetarian');
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    2 rows affected.
    




    []



### Table 5 : pizza_recipes

**toppings** list set of ingredient_id


```sql
%%sql
DROP TABLE IF EXISTS pizza_recipes;
CREATE TABLE pizza_recipes(
    "pizza_id" INTEGER,
    "toppings" TEXT
);
INSERT INTO pizza_recipes
    ("pizza_id","toppings")
VALUES
    (1, '1, 2, 3, 4, 5, 6, 8, 10'),
    (2, '4, 6, 7, 9, 11, 12');

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    2 rows affected.
    




    []



### Table 6: pizza_toppings
This table contains all ingredients name as **topping_name** with their corresponding **topping_id**


```sql
%%sql
DROP TABLE IF EXISTS pizza_toppings;
CREATE TABLE pizza_toppings(
    "topping_id" INTEGER,
    "topping_name" TEXT
);
INSERT INTO pizza_toppings
    ("topping_id","topping_name")
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
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    12 rows affected.
    




    []




```sql
%%sql
SELECT name
FROM sqlite_master
WHERE TYPE ='table'
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>name</th>
    </tr>
    <tr>
        <td>runners</td>
    </tr>
    <tr>
        <td>customer_orders</td>
    </tr>
    <tr>
        <td>runners_orders</td>
    </tr>
    <tr>
        <td>pizza_names</td>
    </tr>
    <tr>
        <td>pizza_recipes</td>
    </tr>
    <tr>
        <td>pizza_toppings</td>
    </tr>
</table>





#### Next : 🍕 <a href='case_study_2_pizza_part_2.ipynb'> Pizza Runner PART 2 </a>

#### Back to : [Top](#top) ▲


<a href='https://github.com/sumedhadewan/8-week-sql-challenge' target='_blank'>See this Project on Github</a>

### Case Study 2
### 🍕 Pizza Runner Part 3

Source : <a href='https://8weeksqlchallenge.com/case-study-2/'>8 Weel SQL Challenge</a>
***


```python
%load_ext sql
```


```python
%sql sqlite:///datasets/pizza_runner.sqlite
```

<a id="top"></a>

### <font color='green'>Pizza Metrics</font>

#### 1.  How many pizzas were ordered?

Checking data types in **customer_orders**


```sql
%%sql
PRAGMA TABLE_INFO(customer_orders)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>cid</th>
        <th>name</th>
        <th>type</th>
        <th>notnull</th>
        <th>dflt_value</th>
        <th>pk</th>
    </tr>
    <tr>
        <td>0</td>
        <td>order_id</td>
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>customer_id</td>
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>pizza_id</td>
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>3</td>
        <td>exclusions</td>
        <td>VARCHAR(4)</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>4</td>
        <td>extras</td>
        <td>VARCHAR(4)</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>5</td>
        <td>order_time</td>
        <td>TIMESTAMP</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>




```sql
%%sql

SELECT COUNT(pizza_id) AS pizza_ordered
FROM customer_orders
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_ordered</th>
    </tr>
    <tr>
        <td>14</td>
    </tr>
</table>



#### 2. How many unique customer orders were made?


```sql
%%sql
SELECT COUNT(DISTINCT order_id) AS unique_order
FROM customer_orders

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>unique_order</th>
    </tr>
    <tr>
        <td>10</td>
    </tr>
</table>



#### 3. How many successful orders were delivered by each runner?


```sql
%%sql
SELECT COUNT(DISTINCT order_id) AS unique_orders_delivered_by_runner
FROM runner_orders
WHERE cancellation = ''

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>unique_orders_delivered_by_runner</th>
    </tr>
    <tr>
        <td>8</td>
    </tr>
</table>



#### 4.  How many of each type of pizza was delivered?

#### Creating temp table of `customer_orders` with only pizza delivered


```sql
%%sql
CREATE TEMP TABLE customer_orders_delivered AS
SELECT *
FROM customer_orders
WHERE order_id IN (
    SELECT DISTINCT order_id
    FROM runner_orders
    WHERE cancellation = ''
)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




    []




```sql
%%sql
SELECT
    pizza_name,
    num_ordered
FROM(
    SELECT pizza_id AS pizza_id,
           COUNT(pizza_id) AS num_ordered
    FROM customer_orders_delivered
    GROUP BY pizza_id
) AS t1
JOIN pizza_names t2
ON t1.pizza_id = t2.pizza_id


```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_name</th>
        <th>num_ordered</th>
    </tr>
    <tr>
        <td>Meatlovers</td>
        <td>9</td>
    </tr>
    <tr>
        <td>Vegetarian</td>
        <td>3</td>
    </tr>
</table>



#### 5.  How many Vegetarian and Meatlovers were ordered by each customer?


```sql
%%sql
SELECT *
FROM pizza_names
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_id</th>
        <th>pizza_name</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Meatlovers</td>
    </tr>
    <tr>
        <td>2</td>
        <td>Vegetarian</td>
    </tr>
</table>




```sql
%%sql
SELECT
    customer_id,
    SUM(CASE WHEN pizza_id = 1 THEN 1 ELSE 0 END) AS meatlover_ordered,
    SUM(CASE WHEN pizza_id = 2 THEN 1 ELSE 0 END) AS vegetarian_ordered
FROM customer_orders t1
GROUP BY customer_id
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>meatlover_ordered</th>
        <th>vegetarian_ordered</th>
    </tr>
    <tr>
        <td>101</td>
        <td>2</td>
        <td>1</td>
    </tr>
    <tr>
        <td>102</td>
        <td>2</td>
        <td>1</td>
    </tr>
    <tr>
        <td>103</td>
        <td>3</td>
        <td>1</td>
    </tr>
    <tr>
        <td>104</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>105</td>
        <td>0</td>
        <td>1</td>
    </tr>
</table>



#### 6.  What was the maximum number of pizzas delivered in a single order?


```sql
%%sql
SELECT
    customer_id,
    order_id,
    COUNT(pizza_id)  AS num_pizza  
FROM customer_orders_delivered
GROUP BY customer_id,order_id
ORDER BY num_pizza DESC
LIMIT 1
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>order_id</th>
        <th>num_pizza</th>
    </tr>
    <tr>
        <td>103</td>
        <td>4</td>
        <td>3</td>
    </tr>
</table>



#### 7.  For each customer, how many delivered pizzas had at least 1 change and how many had no changes?


```sql
%%sql
SELECT customer_id,
       COUNT(CASE WHEN exclusions = '' AND extras ='' THEN 1 END) AS num_pizza_no_change ,
       COUNT(CASE WHEN exclusions <> '' OR extras <> '' THEN 1 END) AS num_pizza_change
FROM customer_orders_delivered
GROUP BY customer_id
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>num_pizza_no_change</th>
        <th>num_pizza_change</th>
    </tr>
    <tr>
        <td>101</td>
        <td>2</td>
        <td>0</td>
    </tr>
    <tr>
        <td>102</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>103</td>
        <td>0</td>
        <td>3</td>
    </tr>
    <tr>
        <td>104</td>
        <td>1</td>
        <td>2</td>
    </tr>
    <tr>
        <td>105</td>
        <td>0</td>
        <td>1</td>
    </tr>
</table>



#### 8.  How many pizzas were delivered that had both exclusions and extras?


```sql
%%sql
SELECT COUNT(*) AS pizza_with_exclusion_and_extras
FROM customer_orders_delivered
WHERE exclusions <> '' AND extras <> ''
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_with_exclusion_and_extras</th>
    </tr>
    <tr>
        <td>1</td>
    </tr>
</table>



#### 9. What was the total volume of pizzas ordered for each hour of the day?


```sql
%%sql
SELECT 
    strftime('%H',order_time) AS hr,
    COUNT(pizza_id) AS pizza_ordered
FROM customer_orders
GROUP BY hr
ORDER BY pizza_ordered DESC
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>hr</th>
        <th>pizza_ordered</th>
    </tr>
    <tr>
        <td>23</td>
        <td>3</td>
    </tr>
    <tr>
        <td>21</td>
        <td>3</td>
    </tr>
    <tr>
        <td>18</td>
        <td>3</td>
    </tr>
    <tr>
        <td>13</td>
        <td>3</td>
    </tr>
    <tr>
        <td>19</td>
        <td>1</td>
    </tr>
    <tr>
        <td>11</td>
        <td>1</td>
    </tr>
</table>



#### 10. What was the volume of orders for each day of the week?


```sql
%%sql
SELECT 
    CASE CAST(strftime('%w',order_time) AS INTEGER)
    WHEN 0 THEN 'Sunday'
    WHEN 1 THEN 'Monday'
    WHEN 2 THEN 'Tuesday'
    WHEN 3 THEN 'Wenesday'
    WHEN 4 THEN 'Thursday'
    WHEN 5 THEN 'Friday'
    ELSE 'Saturday' END AS weekday,
    COUNT(pizza_id) AS pizza_ordered
FROM customer_orders
GROUP BY weekday
ORDER BY pizza_ordered DESC
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>weekday</th>
        <th>pizza_ordered</th>
    </tr>
    <tr>
        <td>Wenesday</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Saturday</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Thursday</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Friday</td>
        <td>1</td>
    </tr>
</table>



<a id='experience'></a>
### <font color='green'>Runner and Customer Experience </font>


```sql
%%sql

PRAGMA TABLE_INFO(runner_orders)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>cid</th>
        <th>name</th>
        <th>type</th>
        <th>notnull</th>
        <th>dflt_value</th>
        <th>pk</th>
    </tr>
    <tr>
        <td>0</td>
        <td>order_id</td>
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>runner_id</td>
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>pickup_time</td>
        <td>VARCHAR(19)</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>3</td>
        <td>distance</td>
        <td>VARCHAR(7)</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>4</td>
        <td>duration</td>
        <td>VARCHAR(10)</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>5</td>
        <td>cancellation</td>
        <td>VARCHAR(23)</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>




```sql
%%sql

DROP TABLE IF EXISTS runner_orders_2;
CREATE TEMP TABLE runner_orders_2 AS 
    SELECT 
        order_id,
        runner_id,
        pickup_time,
        CAST(distance AS DECIMAL) AS distance,
        CAST(duration AS INTEGER) AS duration
    FROM runner_orders

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT *
FROM runner_orders_2
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>order_id</th>
        <th>runner_id</th>
        <th>pickup_time</th>
        <th>distance</th>
        <th>duration</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>2020-01-01 18:15:34</td>
        <td>20</td>
        <td>32</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>2020-01-01 19:10:54</td>
        <td>20</td>
        <td>27</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>2020-01-03 00:12:37</td>
        <td>13.4</td>
        <td>20</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2</td>
        <td>2020-01-04 13:53:03</td>
        <td>23.4</td>
        <td>40</td>
    </tr>
    <tr>
        <td>5</td>
        <td>3</td>
        <td>2020-01-08 21:10:57</td>
        <td>10</td>
        <td>15</td>
    </tr>
    <tr>
        <td>6</td>
        <td>3</td>
        <td>null</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>7</td>
        <td>2</td>
        <td>2020-01-08 21:30:45</td>
        <td>25</td>
        <td>25</td>
    </tr>
    <tr>
        <td>8</td>
        <td>2</td>
        <td>2020-01-10 00:15:02</td>
        <td>23.4</td>
        <td>15</td>
    </tr>
    <tr>
        <td>9</td>
        <td>2</td>
        <td>null</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>10</td>
        <td>1</td>
        <td>2020-01-11 18:50:20</td>
        <td>10</td>
        <td>10</td>
    </tr>
</table>




```sql
%%sql
PRAGMA TABLE_INFO(runner_orders_2)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>cid</th>
        <th>name</th>
        <th>type</th>
        <th>notnull</th>
        <th>dflt_value</th>
        <th>pk</th>
    </tr>
    <tr>
        <td>0</td>
        <td>order_id</td>
        <td>INT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>runner_id</td>
        <td>INT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>pickup_time</td>
        <td>TEXT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>3</td>
        <td>distance</td>
        <td>NUM</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>4</td>
        <td>duration</td>
        <td>INT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>



#### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)


```sql
%%sql
SELECT 
    strftime('%W',registration_date)+1 AS week,
    COUNT(DISTINCT runner_id) AS runner_registered
FROM runners
GROUP BY week
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>week</th>
        <th>runner_registered</th>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
    </tr>
</table>




```sql
%%sql
SELECT COUNT(DISTINCT runner_id)
FROM runners
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>COUNT(DISTINCT runner_id)</th>
    </tr>
    <tr>
        <td>4</td>
    </tr>
</table>



#### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?


```sql
%%sql
DROP TABLE IF EXISTS order_time_table;
CREATE TEMP TABLE order_time_table AS 
SELECT 
    order_id , 
    customer_id,
    MIN(order_time) AS order_time,
    COUNT(pizza_id) AS pizza_count
FROM customer_orders
GROUP BY order_id
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT 
    t1.runner_id,
    ROUND(AVG((julianday(t1.pickup_time) - julianday(t2.order_time))*24*60),2) AS avg_runner_pickup_time
FROM runner_orders t1
JOIN order_time_table t2
ON t1.order_id = t2.order_id
WHERE t1.cancellation = ''
GROUP BY runner_id
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>runner_id</th>
        <th>avg_runner_pickup_time</th>
    </tr>
    <tr>
        <td>1</td>
        <td>14.33</td>
    </tr>
    <tr>
        <td>2</td>
        <td>20.01</td>
    </tr>
    <tr>
        <td>3</td>
        <td>10.47</td>
    </tr>
</table>



#### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?


```sql
%%sql

SELECT 
    t2.pizza_count,
    ROUND(AVG((julianday(t1.pickup_time)- julianday(t2.order_time))*24*60),2) AS avg_prep_time_mins
FROM runner_orders t1
JOIN order_time_table t2
ON t1.order_id = t2.order_id
WHERE t1.cancellation = ''
GROUP BY t2.pizza_count
ORDER BY avg_prep_time_mins DESC
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_count</th>
        <th>avg_prep_time_mins</th>
    </tr>
    <tr>
        <td>3</td>
        <td>29.28</td>
    </tr>
    <tr>
        <td>2</td>
        <td>18.37</td>
    </tr>
    <tr>
        <td>1</td>
        <td>12.36</td>
    </tr>
</table>



#### 4.  What was the average distance travelled for each customer?


```sql
%%sql
DROP TABLE IF EXISTS customer_order_table;
CREATE TEMP TABLE customer_order_table AS
SELECT
    t1.*,
    t2.*
FROM runner_orders t1
JOIN order_time_table t2
ON t1.order_id = t2.order_id 

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```python

ORDER BY avg_distance_km DESC
_km
```


```sql
%%sql
SELECT
    customer_id,
    ROUND(AVG(CAST(distance AS DECIMAL)),2) AS avg_distance_km
FROM customer_order_table
WHERE cancellation = ''
GROUP BY customer_id
ORDER BY customer_id

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>avg_distance_km</th>
    </tr>
    <tr>
        <td>101</td>
        <td>20.0</td>
    </tr>
    <tr>
        <td>102</td>
        <td>18.4</td>
    </tr>
    <tr>
        <td>103</td>
        <td>23.4</td>
    </tr>
    <tr>
        <td>104</td>
        <td>10.0</td>
    </tr>
    <tr>
        <td>105</td>
        <td>25.0</td>
    </tr>
</table>



#### 5. What was the difference between the longest and shortest delivery times for all orders?


```sql
%%sql
SELECT
    MIN(duration),
    MAX(duration),
    MAX(duration) - MIN(duration) AS diff_mins
FROM customer_order_table
WHERE cancellation = ''
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>MIN(duration)</th>
        <th>MAX(duration)</th>
        <th>diff_mins</th>
    </tr>
    <tr>
        <td>10</td>
        <td>40</td>
        <td>30</td>
    </tr>
</table>



#### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?


```sql
%%sql

SELECT runner_id,
       distance, 
       ROUND(distance/duration*60,2) AS avg_speed_km_min
FROM customer_order_table
WHERE cancellation = ''
ORDER BY 1 
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>runner_id</th>
        <th>distance</th>
        <th>avg_speed_km_min</th>
    </tr>
    <tr>
        <td>1</td>
        <td>20.0</td>
        <td>37.5</td>
    </tr>
    <tr>
        <td>1</td>
        <td>20.0</td>
        <td>44.44</td>
    </tr>
    <tr>
        <td>1</td>
        <td>13.4</td>
        <td>40.2</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10.0</td>
        <td>60.0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>23.4</td>
        <td>35.1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>25.0</td>
        <td>60.0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>23.4</td>
        <td>93.6</td>
    </tr>
    <tr>
        <td>3</td>
        <td>10.0</td>
        <td>40.0</td>
    </tr>
</table>



#### 7. What is the successful delivery percentage for each runner?


```sql
%%sql

SELECT
    runner_id,
    delivered_order,
    total_orders,
    delivered_order*1.0/total_orders*100 AS delivery_sucess_perc
FROM(
    SELECT 
        runner_id,
        COUNT(*) AS delivered_order,
        (
            SELECT COUNT(order_id) 
            FROM runner_orders t1
            WHERE t1.runner_id = t2.runner_id
        ) AS total_orders
    FROM runner_orders t2
    WHERE pickup_time <> 'null'
    GROUP BY runner_id
)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>runner_id</th>
        <th>delivered_order</th>
        <th>total_orders</th>
        <th>delivery_sucess_perc</th>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>100.0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>4</td>
        <td>75.0</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>2</td>
        <td>50.0</td>
    </tr>
</table>




```sql
%%sql
SELECT *
FROM customer_orders
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>order_id</th>
        <th>customer_id</th>
        <th>pizza_id</th>
        <th>exclusions</th>
        <th>extras</th>
        <th>order_time</th>
    </tr>
    <tr>
        <td>1</td>
        <td>101</td>
        <td>1</td>
        <td></td>
        <td></td>
        <td>2020-01-01 18:05:02</td>
    </tr>
    <tr>
        <td>2</td>
        <td>101</td>
        <td>1</td>
        <td></td>
        <td></td>
        <td>2020-01-01 19:00:52</td>
    </tr>
    <tr>
        <td>3</td>
        <td>102</td>
        <td>1</td>
        <td></td>
        <td></td>
        <td>2020-01-02 23:51:23</td>
    </tr>
    <tr>
        <td>3</td>
        <td>102</td>
        <td>2</td>
        <td></td>
        <td></td>
        <td>2020-01-02 23:51:23</td>
    </tr>
    <tr>
        <td>4</td>
        <td>103</td>
        <td>1</td>
        <td>4</td>
        <td></td>
        <td>2020-01-04 13:23:46</td>
    </tr>
    <tr>
        <td>4</td>
        <td>103</td>
        <td>1</td>
        <td>4</td>
        <td></td>
        <td>2020-01-04 13:23:46</td>
    </tr>
    <tr>
        <td>4</td>
        <td>103</td>
        <td>2</td>
        <td>4</td>
        <td></td>
        <td>2020-01-04 13:23:46</td>
    </tr>
    <tr>
        <td>5</td>
        <td>104</td>
        <td>1</td>
        <td></td>
        <td>1</td>
        <td>2020-01-08 21:00:29</td>
    </tr>
    <tr>
        <td>6</td>
        <td>101</td>
        <td>2</td>
        <td></td>
        <td></td>
        <td>2020-01-08 21:03:13</td>
    </tr>
    <tr>
        <td>7</td>
        <td>105</td>
        <td>2</td>
        <td></td>
        <td>1</td>
        <td>2020-01-08 21:20:29</td>
    </tr>
    <tr>
        <td>8</td>
        <td>102</td>
        <td>1</td>
        <td></td>
        <td></td>
        <td>2020-01-09 23:54:33</td>
    </tr>
    <tr>
        <td>9</td>
        <td>103</td>
        <td>1</td>
        <td>4</td>
        <td>1, 5</td>
        <td>2020-01-10 11:22:59</td>
    </tr>
    <tr>
        <td>10</td>
        <td>104</td>
        <td>1</td>
        <td></td>
        <td></td>
        <td>2020-01-11 18:34:49</td>
    </tr>
    <tr>
        <td>10</td>
        <td>104</td>
        <td>1</td>
        <td>2, 6</td>
        <td>1, 4</td>
        <td>2020-01-11 18:34:49</td>
    </tr>
</table>



[Top](#top) ▲

#### Prev : 🍕 <a href='case_study_2_part_2.md'> Pizza Runner PART 2 </a>

#### Next : 🍕 <a href='case_study_2_part_4.md'> Pizza Runner PART 4 </a>


#### Back to : <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-2/'>This Project Table of Content</a>

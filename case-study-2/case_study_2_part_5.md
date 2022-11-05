### Case Study 2
### 🍕 Pizza Runner Part 5

Source : <a href='https://8weeksqlchallenge.com/case-study-2/' style="text-decoration:none">8 Week SQL Challenge</a>
***


```python
%load_ext sql
```


```python
%sql sqlite:///datasets/pizza_runner.sqlite
```

<a id='top'></a>

<a id='price'></a>
### <font color='green'> Price and Ratings </font>

#### 1. If a Meat Lovers pizza costs 12 dollar and Vegetarian costs 10 dollar and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?


```sql
%%sql
DROP TABLE IF EXISTS customer_orders_2;
CREATE TEMP TABLE customer_orders_2 AS
SELECT t1.*
FROM customer_orders t1
WHERE t1.order_id IN (
    SELECT order_id
    FROM runner_orders
    WHERE cancellation = ''
)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
DROP TABLE IF EXISTS meat_veggie_count;
CREATE TEMP TABLE meat_veggie_count AS
SELECT 
    SUM(CASE WHEN pizza_id = 1 THEN 1 ELSE 0 END) AS meatlover_ordered,
    SUM(CASE WHEN pizza_id = 2 THEN 1 ELSE 0 END) AS vegetarian_ordered
FROM customer_orders_2

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT
    meatlover_ordered * 12 + vegetarian_ordered * 10 AS money_made_by_pizza
FROM meat_veggie_count
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>money_made_by_pizza</th>
    </tr>
    <tr>
        <td>138</td>
    </tr>
</table>



#### 2. What if there was an additional 1 dollar charge for any pizza extras?

    Add cheese is $1 extra



```sql
%%sql
WITH count_extra_toppings AS(
SELECT 
    SUM(CASE 
    WHEN LENGTH(extras) >1 
    THEN LENGTH(REPLACE(extras,', ',''))
    ELSE LENGTH(extras) END) AS extras
FROM customer_orders_2
)

SELECT
    meatlover_ordered * 12 + vegetarian_ordered * 10 +
    (SELECT extras FROM count_extra_toppings )AS money_made_by_pizza
FROM meat_veggie_count
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>money_made_by_pizza</th>
    </tr>
    <tr>
        <td>142</td>
    </tr>
</table>



### 3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.


```sql
%%sql
DROP TABLE IF EXISTS ratings;
CREATE TABLE ratings
    (
    order_id INTEGER,
    rating INTEGER
    );
INSERT INTO ratings
    (order_id, rating)
VALUES
    (1,4),
    (2,3),
    (3,5),
    (4,3),
    (5,3),
    (6,2),
    (7,4),
    (8,2),
    (9,3),
    (10,4);
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    10 rows affected.
    




    []



#### 4.Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?

    customer_id
    order_id
    runner_id
    rating
    order_time
    pickup_time
    Time between order and pickup
    Delivery duration
    Average speed
    Total number of pizzas



```sql
%%sql
SELECT t1.customer_id,
       t1.order_id,
       t2.runner_id,
       t3.rating, 
       t1.order_time,
       t2.pickup_time,
       Round((julianday(pickup_time) - julianday(order_time))*24*60) AS time_between_order_pickup ,
       t2.duration AS delivery_duration,
       ROUND(t2.distance / t2.duration ,2) AS avg_speed,
       t1.num_pizza_ordered 
    
FROM(
    SELECT order_id,customer_id,pizza_id,order_time, count(pizza_id) AS num_pizza_ordered
    FROM customer_orders_2
    GROUP BY 1,2,3,4
) t1
JOIN runner_orders t2
ON t1.order_id = t2.order_id
JOIN ratings t3
ON t3.order_id = t1.order_id
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>order_id</th>
        <th>runner_id</th>
        <th>rating</th>
        <th>order_time</th>
        <th>pickup_time</th>
        <th>time_between_order_pickup</th>
        <th>delivery_duration</th>
        <th>avg_speed</th>
        <th>num_pizza_ordered</th>
    </tr>
    <tr>
        <td>101</td>
        <td>1</td>
        <td>1</td>
        <td>4</td>
        <td>2020-01-01 18:05:02</td>
        <td>2020-01-01 18:15:34</td>
        <td>11.0</td>
        <td>32</td>
        <td>0.63</td>
        <td>1</td>
    </tr>
    <tr>
        <td>101</td>
        <td>2</td>
        <td>1</td>
        <td>3</td>
        <td>2020-01-01 19:00:52</td>
        <td>2020-01-01 19:10:54</td>
        <td>10.0</td>
        <td>27</td>
        <td>0.74</td>
        <td>1</td>
    </tr>
    <tr>
        <td>102</td>
        <td>3</td>
        <td>1</td>
        <td>5</td>
        <td>2020-01-02 23:51:23</td>
        <td>2020-01-03 00:12:37</td>
        <td>21.0</td>
        <td>20</td>
        <td>0.67</td>
        <td>1</td>
    </tr>
    <tr>
        <td>102</td>
        <td>3</td>
        <td>1</td>
        <td>5</td>
        <td>2020-01-02 23:51:23</td>
        <td>2020-01-03 00:12:37</td>
        <td>21.0</td>
        <td>20</td>
        <td>0.67</td>
        <td>1</td>
    </tr>
    <tr>
        <td>103</td>
        <td>4</td>
        <td>2</td>
        <td>3</td>
        <td>2020-01-04 13:23:46</td>
        <td>2020-01-04 13:53:03</td>
        <td>29.0</td>
        <td>40</td>
        <td>0.59</td>
        <td>1</td>
    </tr>
    <tr>
        <td>103</td>
        <td>4</td>
        <td>2</td>
        <td>3</td>
        <td>2020-01-04 13:23:46</td>
        <td>2020-01-04 13:53:03</td>
        <td>29.0</td>
        <td>40</td>
        <td>0.59</td>
        <td>2</td>
    </tr>
    <tr>
        <td>104</td>
        <td>5</td>
        <td>3</td>
        <td>3</td>
        <td>2020-01-08 21:00:29</td>
        <td>2020-01-08 21:10:57</td>
        <td>10.0</td>
        <td>15</td>
        <td>0.67</td>
        <td>1</td>
    </tr>
    <tr>
        <td>105</td>
        <td>7</td>
        <td>2</td>
        <td>4</td>
        <td>2020-01-08 21:20:29</td>
        <td>2020-01-08 21:30:45</td>
        <td>10.0</td>
        <td>25</td>
        <td>1.0</td>
        <td>1</td>
    </tr>
    <tr>
        <td>102</td>
        <td>8</td>
        <td>2</td>
        <td>2</td>
        <td>2020-01-09 23:54:33</td>
        <td>2020-01-10 00:15:02</td>
        <td>20.0</td>
        <td>15</td>
        <td>1.56</td>
        <td>1</td>
    </tr>
    <tr>
        <td>104</td>
        <td>10</td>
        <td>1</td>
        <td>4</td>
        <td>2020-01-11 18:34:49</td>
        <td>2020-01-11 18:50:20</td>
        <td>16.0</td>
        <td>10</td>
        <td>1.0</td>
        <td>2</td>
    </tr>
</table>



####  5. If a Meat Lovers pizza was 12 DOLLARS and Vegetarian 10 dollars fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?


```sql
%%sql
WITH paid_to_runners AS(
    SELECT SUM((distance+0)*0.3) AS paid_to_runners
    FROM runner_orders
),
earned AS(
    SELECT meatlover_ordered *12 + vegetarian_ordered * 10 AS earned
    FROM meat_veggie_count
)

SELECT earned - (SELECT * FROM paid_to_runners) AS leftover_money
FROM earned
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>leftover_money</th>
    </tr>
    <tr>
        <td>94.44</td>
    </tr>
</table>



[Top](#top) ▲

#### Prev : 🍕 <a href='case_study_2_part_4.md'> Pizza Runner PART 4 </a>




#### Back to : <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-2/'>This Project Table of Content</a>

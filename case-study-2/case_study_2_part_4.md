### Case Study 2 
### 🍕 Pizza Runner Part 4

Source : <a href='https://8weeksqlchallenge.com/case-study-2/' style='text-decoration:None'>8 Week SQL Challenge</a>
***


```python
%load_ext sql
```


```python
%sql sqlite:///datasets/pizza_runner.sqlite
```

<a id='top'></a>

<a id='ingredient'></a>
### <font color='green'> Ingredient Optimisation </font>

#### 1. What are the standard ingredients for each pizza? 

### Creating temp table `pizza_recipes_toppings`

- STEP 1. Spitting toppings text from **`pizza_recipes`** and inserting in column, making long-format USING **recursive CTE** 
- STEP 2. Combining result from Step 1 with **`pizza_toppings`** returning pizza_id, topping_id and topping_name


```sql
%%sql
DROP TABLE IF EXISTS pizza_recipes_toppings;
CREATE TABLE pizza_recipes_toppings AS 
WITH RECURSIVE split(pizza_id,toppings,str) AS(
    SELECT pizza_id,'',toppings||','FROM pizza_recipes
    UNION ALL
    SELECT pizza_id,
    substr(str,0,instr(str,',')),
    substr(str,instr(str,',')+1)
    FROM split WHERE str !=''
)
SELECT
    pizza_id,
    topping_id,
    (
        SELECT topping_name
        FROM pizza_toppings t2
        WHERE t1.topping_id = t2.topping_id
    ) AS topping_name
FROM(
    SELECT
        pizza_id , 
        CAST(toppings AS INTEGER) AS topping_id
    FROM split
    WHERE toppings != ''
) AS t1
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT *
FROM pizza_recipes_toppings
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_id</th>
        <th>topping_id</th>
        <th>topping_name</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>Bacon</td>
    </tr>
    <tr>
        <td>2</td>
        <td>4</td>
        <td>Cheese</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>BBQ Sauce</td>
    </tr>
    <tr>
        <td>2</td>
        <td>6</td>
        <td>Mushrooms</td>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>Beef</td>
    </tr>
    <tr>
        <td>2</td>
        <td>7</td>
        <td>Onions</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>Cheese</td>
    </tr>
    <tr>
        <td>2</td>
        <td>9</td>
        <td>Peppers</td>
    </tr>
    <tr>
        <td>1</td>
        <td>5</td>
        <td>Chicken</td>
    </tr>
    <tr>
        <td>2</td>
        <td>11</td>
        <td>Tomatoes</td>
    </tr>
    <tr>
        <td>1</td>
        <td>6</td>
        <td>Mushrooms</td>
    </tr>
    <tr>
        <td>2</td>
        <td>12</td>
        <td>Tomato Sauce</td>
    </tr>
    <tr>
        <td>1</td>
        <td>8</td>
        <td>Pepperoni</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>Salami</td>
    </tr>
</table>




```sql
%%sql
PRAGMA TABLE_INFO(pizza_recipes_toppings)
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
        <td>pizza_id</td>
        <td>INT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>topping_id</td>
        <td>INT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>topping_name</td>
        <td>TEXT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>




```sql
%%sql
SELECT
    t2.pizza_name,
    t1.pizza_id,
    group_concat(t1.topping_name) AS toppings
FROM pizza_recipes_toppings t1
JOIN pizza_names t2
ON t1.pizza_id = t2.pizza_id
GROUP BY 1,2

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_name</th>
        <th>pizza_id</th>
        <th>toppings</th>
    </tr>
    <tr>
        <td>Meatlovers</td>
        <td>1</td>
        <td>Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami</td>
    </tr>
    <tr>
        <td>Vegetarian</td>
        <td>2</td>
        <td>Cheese,Mushrooms,Onions,Peppers,Tomatoes,Tomato Sauce</td>
    </tr>
</table>



#### 2. What are the mostt commonly added extra?

#### Creating Temp table `extra_table`with order_id, pizza_id, EXTRAs topping_id in long format


```sql
%%sql
DROP TABLE IF EXISTS extra_table;
CREATE TABLE extra_table AS 
WITH RECURSIVE cte2 AS(
    SELECT pizza_id,order_id,extras,
    extras + 0 col
    FROM customer_orders
    UNION ALL
    SELECT pizza_id,
    order_id,
    TRIM(SUBSTR(extras,LENGTH(col)+2)),
    TRIM(SUBSTR(extras,LENGTH(col)+2))+0
    FROM cte2 WHERE INSTR(extras,',')
)
SELECT pizza_id,order_id, col topping_id
FROM cte2
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT *
FROM extra_table
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_id</th>
        <th>order_id</th>
        <th>topping_id</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>5</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>6</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>7</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>8</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>5</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
</table>




```sql
%%sql
SELECT 
        topping_name,
       COUNT(t1.topping_id) AS times_added 
FROM extra_table t1
JOIN pizza_toppings t2 
ON t1.topping_id = t2.topping_id
WHERE t1.topping_id >0
GROUP BY t1.topping_id
ORDER BY times_added DESC
LIMIT 1
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>topping_name</th>
        <th>times_added</th>
    </tr>
    <tr>
        <td>Bacon</td>
        <td>4</td>
    </tr>
</table>



#### 3. What was the most common exclusion?

#### Creating Temp table `exclusion_table` with order_id, pizza_id, EXCLUSIONs topping_id in long format i.e splitting comma separated text into rows


```sql
%%sql

DROP TABLE IF EXISTS exclusion_table;
CREATE TEMP TABLE exclusion_table AS
WITH RECURSIVE cte AS(
    SELECT pizza_id,order_id,exclusions,
    exclusions+ 0 col
    FROM customer_orders
    UNION ALL
    SELECT pizza_id,
    order_id,
    TRIM(SUBSTR(exclusions,LENGTH(col)+2)),
    TRIM(SUBSTR(exclusions,LENGTH(col)+2))+0
    FROM cte WHERE INSTR(exclusions,',')
)

SELECT pizza_id,order_id, col topping_id
FROM cte

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT *
FROM exclusion_table
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_id</th>
        <th>order_id</th>
        <th>topping_id</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
    </tr>
    <tr>
        <td>2</td>
        <td>4</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>5</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>6</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>7</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>8</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
    </tr>
</table>




```sql
%%sql
SELECT 
       topping_name,
       COUNT(t1.topping_id) AS times_excluded 
FROM exclusion_table t1
JOIN pizza_toppings t2 
ON t1.topping_id = t2.topping_id
WHERE t1.topping_id >0
GROUP BY t1.topping_id
ORDER BY times_excluded DESC
LIMIT 1
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>topping_name</th>
        <th>times_excluded</th>
    </tr>
    <tr>
        <td>Cheese</td>
        <td>4</td>
    </tr>
</table>



#### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:

    - Meat Lovers
    - Meat Lovers - Exclude Beef
    - Meat Lovers - Extra Bacon
    - Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers


#### Combining both <font color='blue'>exclusion_table </font> and <font color='blue'> extra_table </font>
# ?


```python
DROP TABLE IF EXISTS extra_exclusions;
CREATE TEMP TABLE extra_exclusions
```


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
SELECT SUBSTR('SQLITE SUBSTR',1,6)
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>SUBSTR(&#x27;SQLITE SUBSTR&#x27;,1,6)</th>
    </tr>
    <tr>
        <td>SQLITE</td>
    </tr>
</table>




```sql
%%sql
SELECT *
FROM customer_orders
WHERE exclusions IS NOT NULL 

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




```sql
%%sql

SELECT exclusions,
        LENGTH(TRIM(exclusions)),
        SUBSTR(exclusions,1,1) AS exclusion_1,
        CASE WHEN exclusions REGEX ',' THEN 1
FROM customer_orders
WHERE LENGTH(TRIM(exclusions)) >0
    
```

     * sqlite:///datasets/pizza_runner.sqlite
    (sqlite3.OperationalError) near "REGEX": syntax error
    [SQL: SELECT exclusions,
            LENGTH(TRIM(exclusions)),
            SUBSTR(exclusions,1,1) AS exclusion_1,
            CASE WHEN exclusions REGEX ',' THEN 1
    FROM customer_orders
    WHERE LENGTH(TRIM(exclusions)) >0]
    (Background on this error at: http://sqlalche.me/e/13/e3q8)
    


```sql
%%sql
DROP TABLE IF EXISTS extra_exclusion_table;
CREATE TABLE extra_exclusion_table AS 
    WITH extra_exclusion_table AS(
        SELECT
           t1.pizza_id,
            t1.order_id,
            t1.topping_id,
           t2.pizza_id,
            t2.order_id,
            t2.topping_id
        FROM exclusion_table t1
        LEFT JOIN extra_table t2
        USING (pizza_id, order_id)
        UNION ALL
        SELECT  t1.pizza_id,
            t1.order_id,
            t1.topping_id,
           t2.pizza_id,
            t2.order_id,
            t2.topping_id
        FROM extra_table t2
        LEFT JOIN exclusion_table t1
        USING(pizza_id,order_id)
        
    )
    SELECT *
    FROM extra_exclusion_table

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    Done.
    




    []




```sql
%%sql
SELECT *
FROM extra_exclusion_table
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_id</th>
        <th>order_id</th>
        <th>topping_id</th>
        <th>pizza_id:1</th>
        <th>order_id:1</th>
        <th>topping_id:1</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>0</td>
        <td>1</td>
        <td>2</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>0</td>
        <td>1</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>0</td>
        <td>2</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>4</td>
        <td>4</td>
        <td>2</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>5</td>
        <td>0</td>
        <td>1</td>
        <td>5</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>6</td>
        <td>0</td>
        <td>2</td>
        <td>6</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>7</td>
        <td>0</td>
        <td>2</td>
        <td>7</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>8</td>
        <td>0</td>
        <td>1</td>
        <td>8</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>4</td>
        <td>1</td>
        <td>9</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>4</td>
        <td>1</td>
        <td>9</td>
        <td>5</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>0</td>
        <td>1</td>
        <td>2</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>0</td>
        <td>1</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>0</td>
        <td>2</td>
        <td>3</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>4</td>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>4</td>
        <td>4</td>
        <td>2</td>
        <td>4</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>5</td>
        <td>0</td>
        <td>1</td>
        <td>5</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>6</td>
        <td>0</td>
        <td>2</td>
        <td>6</td>
        <td>0</td>
    </tr>
    <tr>
        <td>2</td>
        <td>7</td>
        <td>0</td>
        <td>2</td>
        <td>7</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>8</td>
        <td>0</td>
        <td>1</td>
        <td>8</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>4</td>
        <td>1</td>
        <td>9</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
        <td>1</td>
        <td>10</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
        <td>1</td>
        <td>10</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>9</td>
        <td>4</td>
        <td>1</td>
        <td>9</td>
        <td>5</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>0</td>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>2</td>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
    <tr>
        <td>1</td>
        <td>10</td>
        <td>6</td>
        <td>1</td>
        <td>10</td>
        <td>4</td>
    </tr>
</table>




```sql
%%sql
CREATE TABLE dogs (
    type       TEXT,
    color TEXT
);

INSERT INTO dogs(type, color) 
VALUES('Hunting','Black'), ('Guard','Brown');

CREATE TABLE cats (
    type       TEXT,
    color TEXT
);

INSERT INTO cats(type,color) 
VALUES('Indoor','White'), 
      ('Outdoor','Black');
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    2 rows affected.
    Done.
    2 rows affected.
    




    []




```sql
%%sql
SELECT d.type,
         d.color,
         c.type,
         c.color
FROM dogs d
LEFT JOIN cats c USING(color)
UNION ALL
SELECT d.type,
         d.color,
         c.type,
         c.color
FROM cats c
LEFT JOIN dogs d USING(color)

```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>type</th>
        <th>color</th>
        <th>type_1</th>
        <th>color_1</th>
    </tr>
    <tr>
        <td>Hunting</td>
        <td>Black</td>
        <td>Outdoor</td>
        <td>Black</td>
    </tr>
    <tr>
        <td>Guard</td>
        <td>Brown</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>None</td>
        <td>None</td>
        <td>Indoor</td>
        <td>White</td>
    </tr>
    <tr>
        <td>Hunting</td>
        <td>Black</td>
        <td>Outdoor</td>
        <td>Black</td>
    </tr>
</table>




```sql
%%sql

SELECT 
   t.row_num,
   t.order_id,
   t.customer_id,
   t.pizza_id,
   t.order_time,
   TRIM(j1.exclusions) AS exclusions,
   TRIM(j2.extras) AS extras
FROM
  (SELECT *,
          row_number() over() AS row_num
   FROM customer_orders) AS t
JOIN json_table(trim(replace(json_array(t.exclusions), ',', '","')),
                      '$[*]' columns (exclusions varchar(50) PATH '$')) j1
JOIN json_table(trim(replace(json_array(t.extras), ',', '","')),
                      '$[*]' columns (extras varchar(50) PATH '$')) j2 ;

```

     * sqlite:///datasets/pizza_runner.sqlite
    (sqlite3.OperationalError) near "columns": syntax error
    [SQL: SELECT 
       t.row_num,
       t.order_id,
       t.customer_id,
       t.pizza_id,
       t.order_time,
       TRIM(j1.exclusions) AS exclusions,
       TRIM(j2.extras) AS extras
    FROM
      (SELECT *,
              row_number() over() AS row_num
       FROM customer_orders) AS t
    JOIN json_table(trim(replace(json_array(t.exclusions), ',', '","')),
                          '$[*]' columns (exclusions varchar(50) PATH '$')) j1
    JOIN json_table(trim(replace(json_array(t.extras), ',', '","')),
                          '$[*]' columns (extras varchar(50) PATH '$')) j2 ;]
    (Background on this error at: http://sqlalche.me/e/13/e3q8)
    


```python
JOIN json_table(trim(replace(json_array(t.exclusions), ',', '","')),
                      '$[*]' columns (exclusions varchar(50) PATH '$')) j1
JOIN json_table(trim(replace(json_array(t.extras), ',', '","')),
                      '$[*]' columns (extras varchar(50) PATH '$')) j2 ;

```


```python

```

#### 6. 
What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

**customer_order_2** - formed combining two tables `customer_order` and `pizza_recipes_toppings` [temp table created above]

**toppings_count** - count each toppings used in all order_id of table `customer_order`

**exclusion_count** - count each excluded topping using topping_id from temp table `exclusion_table`

**extra_count** - count each extra topping using topping_id from temp table `extra_table`

**toppings_count_after_exclusion** - substract count of excluded topping from `exclusion_count`

**toppings_count_after_extra** - add count of excluded topping to `toppings_after_exclusion`


```sql
%%sql
WITH customer_order_2 AS(
    SELECT t1.*,
            t2.topping_name,
            t2.topping_id
    FROM customer_orders t1
    JOIN pizza_recipes_toppings t2
    ON t1.pizza_id = t2.pizza_id
),
toppings_count AS(
    SELECT 
           topping_name,
           topping_id, 
           COUNT(topping_name) AS topping_count
    FROM customer_order_2
    GROUP BY 1,2
),
exclusion_count AS(
    SELECT exclusions AS topping_id,
           count(*) AS num
    FROM exclusion_table
    WHERE exclusions > 0
    GROUP BY exclusions
),
extra_count AS(
    SELECT extras AS topping_id,
       count(*) AS num 
    FROM extra_table
    WHERE extras > 0
    GROUP BY extras
),
toppings_count_after_exclusion AS(
    SELECT 
        topping_id,
        topping_name,
        CASE WHEN t1.topping_id IN (
            SELECT topping_id
            FROM exclusion_count
        ) THEN 
            t1.topping_count - (
                SELECT num
                FROM exclusion_count t2
                WHERE t1.topping_id = t2.topping_id
            )
            ELSE t1.topping_count
            END AS topping_count

    FROM toppings_count t1
),
 toppings_count_after_extra AS (SELECT 
        topping_id,
        topping_name,
        CASE WHEN t1.topping_id IN (
            SELECT topping_id
            FROM extra_count
        ) THEN 
            t1.topping_count + (
                SELECT num
                FROM extra_count t2
                WHERE t1.topping_id = t2.topping_id
            )
            ELSE t1.topping_count
            END AS topping_count
    FROM toppings_count_after_exclusion t1
    ORDER BY topping_id
)

SELECT *
FROM toppings_count_after_extra
ORDER BY topping_count DESC
                                
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>topping_id</th>
        <th>topping_name</th>
        <th>topping_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Bacon</td>
        <td>14</td>
    </tr>
    <tr>
        <td>6</td>
        <td>Mushrooms</td>
        <td>13</td>
    </tr>
    <tr>
        <td>4</td>
        <td>Cheese</td>
        <td>11</td>
    </tr>
    <tr>
        <td>5</td>
        <td>Chicken</td>
        <td>11</td>
    </tr>
    <tr>
        <td>3</td>
        <td>Beef</td>
        <td>10</td>
    </tr>
    <tr>
        <td>8</td>
        <td>Pepperoni</td>
        <td>10</td>
    </tr>
    <tr>
        <td>10</td>
        <td>Salami</td>
        <td>10</td>
    </tr>
    <tr>
        <td>2</td>
        <td>BBQ Sauce</td>
        <td>9</td>
    </tr>
    <tr>
        <td>7</td>
        <td>Onions</td>
        <td>4</td>
    </tr>
    <tr>
        <td>9</td>
        <td>Peppers</td>
        <td>4</td>
    </tr>
    <tr>
        <td>11</td>
        <td>Tomatoes</td>
        <td>4</td>
    </tr>
    <tr>
        <td>12</td>
        <td>Tomato Sauce</td>
        <td>4</td>
    </tr>
</table>



[Top](#top) ▲

#### Prev : 🍕 <a href='case_study_2_part_3.md'> Pizza Runner PART 3 </a>

#### Next : 🍕 <a href='case_study_2_part_5.md'> Pizza Runner PART 5 </a>


#### Back to : <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-2/'>This Project Table of Content</a>

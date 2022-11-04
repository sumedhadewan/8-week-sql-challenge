## Case Study 1
### Danny's Dinner

Source : <a href='https://8weeksqlchallenge.com/case-study-1/' target='_blank'>8 Week SQL Challenge</a>

<img src="images/danny.png" style='width:500px'/> 

<a id='top'></a>

#### Table of Content

 * [Introduction](#introduction)
 * [Creating Tables in Database](#creating)
 * [Entity Relationship Diagram](#diagram)
 * [Case study questions and solutions](#solution)

<a id='introduction'></a>


### Introduction


Danny love for Japanese food lead him to embark on risky venture opening a little cute resturant serving his 3 favourite foods : sushi , curry , ramen.

Danny is in need of assitance to help the restaurant stay afloat. He want to use basic data collected over few months of operation and answer few simple questions about his customer , their visiting pattern, how much money they've spent and which menu items are their favourite. 
Having this deeper connection with his customers will help him deliver better and more personalized experience for his loyal customers.

Further he plans on using these insights to help him decide whether he should expand the existing customer loyalty progran,

Load Extension


```python
%load_ext sql
```

Initialize connection

It will create a database file with the name dannys_diner under datasets directory, it is not existing already else it will simply initialize the connection to existing database.


I'm using sqlite for this project


```python
%sql sqlite:///datasets/dannys_diner.db
```

<a id="creating"></a>

### Creating tables in Database

### Table 1 : `sales`

The sales table with three columns - customer id, order_date, product_id.

capturing all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered


```sql
%%sql 

CREATE TABLE sales (
  "customer_id" VARCHAR(1),
  "order_date" DATE,
  "product_id" INTEGER
);
INSERT INTO sales
  ("customer_id", "order_date", "product_id")
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    15 rows affected.
    




    []



Querying table 'sales' just created


```sql

%%sql
SELECT *
FROM sales
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>order_date</th>
        <th>product_id</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-01</td>
        <td>1</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-01</td>
        <td>2</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-07</td>
        <td>2</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-10</td>
        <td>3</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-11</td>
        <td>3</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-11</td>
        <td>3</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-01</td>
        <td>2</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-02</td>
        <td>2</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-04</td>
        <td>1</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-11</td>
        <td>1</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-16</td>
        <td>3</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-02-01</td>
        <td>3</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2021-01-01</td>
        <td>3</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2021-01-01</td>
        <td>3</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2021-01-07</td>
        <td>3</td>
    </tr>
</table>



### Table 2 : `menu`

The menu table maps the product_id to the actual product_name and price of each menu item.


```sql
%%sql
CREATE TABLE menu(
    "product_id" INTEGER,
    "product_name" VARCHAR(5),
    "price" INTEGER
);
INSERT INTO menu
    ("product_id","product_name","price")
VALUES
    ('1','sushi','10'),
    ('2','curry','15'),
    ('3','ramen','12');
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    3 rows affected.
    




    []




```sql
%%sql
SELECT *
FROM menu
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>product_id</th>
        <th>product_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>1</td>
        <td>sushi</td>
        <td>10</td>
    </tr>
    <tr>
        <td>2</td>
        <td>curry</td>
        <td>15</td>
    </tr>
    <tr>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
    </tr>
</table>



### Table 3: `members`

The final members table captures the join_date when a customer_id joined the beta version of the Danny’s Diner loyalty program


```sql
%%sql
DROP TABLE members
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




    []




```sql
%%sql
CREATE TABLE members(
    "customer_id" VARCHAR(1),
    "join_date" DATE
);
INSERT INTO members
    ("customer_id","join_date")
VALUES
    ('A','2021-01-07'),
    ('B','2021-01-09');
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    2 rows affected.
    




    []




```sql
%%sql
SELECT *
FROM members
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>join_date</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-07</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-09</td>
    </tr>
</table>




```sql
%%sql

SELECT column_name, data_type
FROM INFORMATION_SCHEMA.TABLES

```

     * sqlite:///datasets/dannys_diner.db
    (sqlite3.OperationalError) no such table: INFORMATION_SCHEMA.TABLES
    [SQL: SELECT column_name, data_type
    FROM INFORMATION_SCHEMA.TABLES]
    (Background on this error at: http://sqlalche.me/e/13/e3q8)
    

### Querying table names


```sql
%%sql
SELECT name
FROM sqlite_master
WHERE TYPE ='table'
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>name</th>
    </tr>
    <tr>
        <td>sales</td>
    </tr>
    <tr>
        <td>menu</td>
    </tr>
    <tr>
        <td>members</td>
    </tr>
</table>



<a id="diagram"></a>


## Entity Relationship Diagram

<img src="images/Danny's Diner.png">

## Join All the Things

Generating temp table `basic_table` so Danny and his team can easily inspect the data without needing to use SQL


```sql
%%sql
CREATE TEMP TABLE basic_table AS
SELECT
    s.customer_id,
    s.order_date,
    m.product_id,
    m.product_name,
    m.price,
    CASE 
    WHEN s.customer_id IN 
        (SELECT customer_id 
         FROM members mem
         WHERE s.order_date >= mem.join_date
        )
    THEN 'Y'
    ELSE 'N' END AS member
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
ORDER BY s.customer_id,s.order_date , price DESC

```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




    []




```sql
%%sql
SELECT *
FROM basic_table


```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>order_date</th>
        <th>product_id</th>
        <th>product_name</th>
        <th>price</th>
        <th>member</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-01</td>
        <td>2</td>
        <td>curry</td>
        <td>15</td>
        <td>N</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-01</td>
        <td>1</td>
        <td>sushi</td>
        <td>10</td>
        <td>N</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-07</td>
        <td>2</td>
        <td>curry</td>
        <td>15</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-10</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-11</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-11</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-01</td>
        <td>2</td>
        <td>curry</td>
        <td>15</td>
        <td>N</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-02</td>
        <td>2</td>
        <td>curry</td>
        <td>15</td>
        <td>N</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-04</td>
        <td>1</td>
        <td>sushi</td>
        <td>10</td>
        <td>N</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-11</td>
        <td>1</td>
        <td>sushi</td>
        <td>10</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-16</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-02-01</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>Y</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2021-01-01</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>N</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2021-01-01</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>N</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2021-01-07</td>
        <td>3</td>
        <td>ramen</td>
        <td>12</td>
        <td>N</td>
    </tr>
</table>



<a id="solution"></a>


# Case study questions

### 1.  What is the total amount each customer spent at the restaurant?


```sql
%%sql
SELECT customer_id, SUM(price) AS total_spent
FROM basic_table
GROUP BY customer_id
ORDER BY customer_id
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>total_spent</th>
    </tr>
    <tr>
        <td>A</td>
        <td>76</td>
    </tr>
    <tr>
        <td>B</td>
        <td>74</td>
    </tr>
    <tr>
        <td>C</td>
        <td>36</td>
    </tr>
</table>



### 2. How many days has each customer visited the restaurant?


```sql
%%sql

SELECT 
    customer_id,
    COUNT(DISTINCT order_date) AS days_vistied 
FROM basic_table
GROUP BY customer_id

```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>days_vistied</th>
    </tr>
    <tr>
        <td>A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>B</td>
        <td>6</td>
    </tr>
    <tr>
        <td>C</td>
        <td>2</td>
    </tr>
</table>



B visited often more than A and C.
But customers may have visited restaurants twice a day.


```sql
%%sql
SELECT 
    customer_id,
    COUNT(order_date) AS days_vistied 
FROM basic_table
GROUP BY customer_id
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>days_vistied</th>
    </tr>
    <tr>
        <td>A</td>
        <td>6</td>
    </tr>
    <tr>
        <td>B</td>
        <td>6</td>
    </tr>
    <tr>
        <td>C</td>
        <td>3</td>
    </tr>
</table>



### 3. What was the first item from the menu purchased by each customer? 


```sql
%%sql
SELECT
    customer_id,
    GROUP_CONCAT(product_name,',') AS first_item
FROM basic_table t2
WHERE order_date = (
    SELECT MIN(order_date)
    FROM basic_table as t1
    WHERE t1.customer_id = t2.customer_id 
    GROUP BY customer_id
)
GROUP BY customer_id
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>first_item</th>
    </tr>
    <tr>
        <td>A</td>
        <td>curry,sushi</td>
    </tr>
    <tr>
        <td>B</td>
        <td>curry</td>
    </tr>
    <tr>
        <td>C</td>
        <td>ramen,ramen</td>
    </tr>
</table>



Ramen is popular item from the menu

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?


```sql
%%sql
SELECT
    product_name,
    COUNT(*) AS n_times_purchased
FROM basic_table
GROUP BY product_name
ORDER BY n_times_purchased DESC
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>product_name</th>
        <th>n_times_purchased</th>
    </tr>
    <tr>
        <td>ramen</td>
        <td>8</td>
    </tr>
    <tr>
        <td>curry</td>
        <td>4</td>
    </tr>
    <tr>
        <td>sushi</td>
        <td>3</td>
    </tr>
</table>



### 5. Which item was the most popular for each customer?


```sql
%%sql
WITH purchase_table AS(
    SELECT
        customer_id,
        product_name,
        COUNT(*) AS n_times_purchased
    FROM basic_table
    GROUP BY customer_id,product_name
),
purchase_rank_table AS(
    SELECT
        customer_id,
        product_name,
        n_times_purchased,
        RANK() OVER (
            PARTITION BY customer_id
            ORDER BY n_times_purchased DESC) AS rank
    FROM purchase_table
)

SELECT 
    customer_id,
    product_name,
    n_times_purchased
FROM purchase_rank_table
WHERE rank = 1
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>product_name</th>
        <th>n_times_purchased</th>
    </tr>
    <tr>
        <td>A</td>
        <td>ramen</td>
        <td>3</td>
    </tr>
    <tr>
        <td>B</td>
        <td>curry</td>
        <td>2</td>
    </tr>
    <tr>
        <td>B</td>
        <td>ramen</td>
        <td>2</td>
    </tr>
    <tr>
        <td>B</td>
        <td>sushi</td>
        <td>2</td>
    </tr>
    <tr>
        <td>C</td>
        <td>ramen</td>
        <td>3</td>
    </tr>
</table>



Ramen is still popular among Danny's customers


### 6. Which item was purchased first by the customer after they became a member?


```sql
%%sql

SELECT *
FROM
(
    SELECT
        customer_id,
        product_name,
        order_date,
        RANK() OVER(
        PARTITION BY customer_id
        ORDER BY order_date) AS rank
    FROM basic_table
    WHERE member = 'Y'
)
WHERE rank = 1
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>product_name</th>
        <th>order_date</th>
        <th>rank</th>
    </tr>
    <tr>
        <td>A</td>
        <td>curry</td>
        <td>2021-01-07</td>
        <td>1</td>
    </tr>
    <tr>
        <td>B</td>
        <td>sushi</td>
        <td>2021-01-11</td>
        <td>1</td>
    </tr>
</table>




```sql
%%sql
SELECT *
FROM members
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>join_date</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-07</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-09</td>
    </tr>
</table>



### 7. Which item was purchased just before the customer became a member

C is still not a member of loyalty program. so we are excluding C for this enquiry


```sql
%%sql
WITH before_membership AS(
    SELECT *
    FROM basic_table
    WHERE member = 'N' AND 
    customer_id IN (
        SELECT 
            DISTINCT customer_id
        FROM members
        )
),
before_membership_rank AS (
    SELECT customer_id,
        order_date,
        product_name,
        RANK() OVER(PARTITION BY customer_id
        ORDER BY order_date DESC) AS rank
    FROM
        before_membership
)
SELECT *
FROM before_membership_rank
WHERE rank = 1
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>order_date</th>
        <th>product_name</th>
        <th>rank</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-01</td>
        <td>curry</td>
        <td>1</td>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-01</td>
        <td>sushi</td>
        <td>1</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-04</td>
        <td>sushi</td>
        <td>1</td>
    </tr>
</table>



A and B customer last purchased curry and sushi respectively before becoming member of loyalty program.

Again A and B customer first purchased curry and sushi respectively after becoming member.

### 8. a. What is the total items and amount spent for each member before they became a member?


```sql
%%sql
SELECT 
    customer_id,
    COUNT(product_name) AS total_purchased,
    SUM(price) AS total_spent
FROM basic_table
WHERE member = 'N'
GROUP BY customer_id
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>total_purchased</th>
        <th>total_spent</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2</td>
        <td>25</td>
    </tr>
    <tr>
        <td>B</td>
        <td>3</td>
        <td>40</td>
    </tr>
    <tr>
        <td>C</td>
        <td>3</td>
        <td>36</td>
    </tr>
</table>



### 8.b. What is the total items and amount spent for each member after they became a member?


```sql
%%sql
SELECT 
    customer_id,
    COUNT(product_name) AS total_purchased,
    SUM(price) AS total_spent
FROM basic_table
WHERE member = 'Y'
GROUP BY customer_id
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>total_purchased</th>
        <th>total_spent</th>
    </tr>
    <tr>
        <td>A</td>
        <td>4</td>
        <td>51</td>
    </tr>
    <tr>
        <td>B</td>
        <td>3</td>
        <td>34</td>
    </tr>
</table>



### 9. If each 1dollar spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?


```sql
%%sql
WITH loyalty_point_table AS(
    SELECT
        *,
        CASE 
        WHEN product_id = 1 THEN price*20
        ELSE price*10
        END AS points
    FROM basic_table
)
SELECT customer_id,
    SUM(points) AS total_loyalty_points
FROM loyalty_point_table
GROUP BY customer_id
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>total_loyalty_points</th>
    </tr>
    <tr>
        <td>A</td>
        <td>860</td>
    </tr>
    <tr>
        <td>B</td>
        <td>940</td>
    </tr>
    <tr>
        <td>C</td>
        <td>360</td>
    </tr>
</table>



### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January? 

Day 1 - Day 7 (1 week from the 1st day joined as Loyalty member) 
Each $1 spent on all items is 20 points.

Day x to Day 1 (first day as loyalty member) Or Week after Day 1 
Each $1 spent on Sushi  is 20 points And on rest item is 10 points




```sql
%%sql
SELECT *,
    date(join_date, '7 days')
FROM members
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>join_date</th>
        <th>date(join_date, &#x27;7 days&#x27;)</th>
    </tr>
    <tr>
        <td>A</td>
        <td>2021-01-07</td>
        <td>2021-01-14</td>
    </tr>
    <tr>
        <td>B</td>
        <td>2021-01-09</td>
        <td>2021-01-16</td>
    </tr>
</table>



Cte table **dates_cte** to record 7th day from join_date


```sql
%%sql
WITH dates_cte AS(
    SELECT *,
        date(join_date, '7 days') AS valid_date
    FROM members
    
),
loyalty_point_table AS(
    SELECT 
        t1.customer_id,
        t1.order_date, 
        CASE 
            WHEN (t1.order_date < t2.join_date)
            OR (t1.order_date > t2.valid_date)
            THEN
                CASE WHEN t1.product_id = 1
                        THEN t1.price*10*2
                        ELSE t1.price*10
                END
            WHEN (t1.order_date >= t2.join_date) 
            AND (t1.order_date <= t2.valid_date)
            THEN price * 2*10
        END AS points
    FROM basic_table t1
    JOIN dates_cte t2
    ON t1.customer_id = t2.customer_id
)

SELECT
    customer_id,
    SUM(points) AS loyalty_points
FROM loyalty_point_table
WHERE STRFTIME('%m',order_date) = '01'
GROUP BY customer_id





```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>loyalty_points</th>
    </tr>
    <tr>
        <td>A</td>
        <td>1370</td>
    </tr>
    <tr>
        <td>B</td>
        <td>940</td>
    </tr>
</table>



### 11. Calculating days from first purchase till the day they become loyalty member?


```sql
%%sql
WITH first_order_table AS (
    SELECT
        customer_id,
        MIN(order_date) AS first_order_date
    FROM basic_table t1
    WHERE customer_id IN (
        SELECT DISTINCT customer_id
        FROM members
    )
    GROUP BY customer_id
)

SELECT 
    customer_id,
    CAST(JULIANDAY((SELECT join_date
        FROM members t2
        WHERE t1.customer_id = t2.customer_id)) - JULIANDAY(first_order_date)
    AS INTEGER) AS days
FROM first_order_table t1
```

     * sqlite:///datasets/dannys_diner.db
    Done.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>days</th>
    </tr>
    <tr>
        <td>A</td>
        <td>6</td>
    </tr>
    <tr>
        <td>B</td>
        <td>8</td>
    </tr>
</table>



### Insights
From the analysis, we discover few interesting insights that would be certainly useful for Danny

- Danny' Diner's most popular item is Ramen.
- The last item Customer A and B order before becoming loyalty members are sushi and curry. Also the first thing they purchased after becoming member are sushi and curry. Apart from popular item as Ramen, other item sushi and curry must be really delicious. 
- Loyatly customer spend more at restaurant. Customer A & B spend more than customer C.
- Loyalty customer visited more often those who are not in loyalty program
- Both loyalty member signed up for this program within a week from their first visit.

[Back to top ](#top) ▲

#### <a href='https://github.com/sumedhadewan/8-week-sql-challenge' target='_blank'> Main Table of content</a> 

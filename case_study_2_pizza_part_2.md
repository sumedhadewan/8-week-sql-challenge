<a id='top'></a>

#### Case Study 2
### 🍕 Pizza Runner PART 2 (Data cleaning)

Source : <a href='https://8weeksqlchallenge.com/case-study-2/'>8 Week SQL Challenge</a>

Load extension


```python
%load_ext sql
```

Initialize the connection to existing **pizza_runner** database


```python
%sql sqlite:///datasets/pizza_runner.sqlite
```

There are 6 tables with **pizza_runner**


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
        <td>pizza_names</td>
    </tr>
    <tr>
        <td>pizza_recipes</td>
    </tr>
    <tr>
        <td>pizza_toppings</td>
    </tr>
    <tr>
        <td>runner_orders</td>
    </tr>
</table>



#### Entity Relationship Diagram

<img src = 'images/Pizza Runner.png'>

####  Data types  And Data Cleaning

**runners** has no null values and Data type is correct


```sql
%%sql
PRAGMA table_info(runners)
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
        <td>runner_id</td>
        <td>INTERGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>registration_date</td>
        <td>DATE</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>




```sql
%%sql
SELECT *
FROM runners
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>runner_id</th>
        <th>registration_date</th>
    </tr>
    <tr>
        <td>1</td>
        <td>2021-01-01</td>
    </tr>
    <tr>
        <td>2</td>
        <td>2021-01-03</td>
    </tr>
    <tr>
        <td>3</td>
        <td>2021-01-08</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2021-01-15</td>
    </tr>
</table>



There are NULL and STRING 'null' values in columns *extras* and *exclusions* of **customer_orders**. Replacing with empty string.


```sql
%%sql
PRAGMA table_info(customer_orders)
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
        <td>None</td>
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
        <td>null</td>
        <td>1</td>
        <td>2020-01-08 21:00:29</td>
    </tr>
    <tr>
        <td>6</td>
        <td>101</td>
        <td>2</td>
        <td>null</td>
        <td>null</td>
        <td>2020-01-08 21:03:13</td>
    </tr>
    <tr>
        <td>7</td>
        <td>105</td>
        <td>2</td>
        <td>null</td>
        <td>1</td>
        <td>2020-01-08 21:20:29</td>
    </tr>
    <tr>
        <td>8</td>
        <td>102</td>
        <td>1</td>
        <td>null</td>
        <td>null</td>
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
        <td>null</td>
        <td>null</td>
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



Replacing with Null values with empty string


```sql
%%sql
UPDATE customer_orders
SET extras = COALESCE(extras,'')
WHERE extras IS NULL
```

     * sqlite:///datasets/pizza_runner.sqlite
    3 rows affected.
    




    []



Replacing string"null" with empty string


```sql
%%sql
UPDATE customer_orders
SET exclusions = ''
WHERE exclusions = 'null'
```

     * sqlite:///datasets/pizza_runner.sqlite
    5 rows affected.
    




    []




```sql
%%sql
UPDATE customer_orders
SET extras = ''
WHERE extras = "null"
```

     * sqlite:///datasets/pizza_runner.sqlite
    0 rows affected.
    




    []




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




```sql
%%sql
SELECT *
FROM customer_orders
WHERE exclusions = ''

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
        <td>10</td>
        <td>104</td>
        <td>1</td>
        <td></td>
        <td></td>
        <td>2020-01-11 18:34:49</td>
    </tr>
</table>



### runner_orders


```sql
%%sql
SELECT *
FROM runner_orders
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
        <th>cancellation</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>2020-01-01 18:15:34</td>
        <td>20km</td>
        <td>32 minutes</td>
        <td></td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>2020-01-01 19:10:54</td>
        <td>20km</td>
        <td>27 minutes</td>
        <td></td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>2020-01-03 00:12:37</td>
        <td>13.4km</td>
        <td>20 mins</td>
        <td>None</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2</td>
        <td>2020-01-04 13:53:03</td>
        <td>23.4</td>
        <td>40</td>
        <td>None</td>
    </tr>
    <tr>
        <td>5</td>
        <td>3</td>
        <td>2020-01-08 21:10:57</td>
        <td>10</td>
        <td>15</td>
        <td>None</td>
    </tr>
    <tr>
        <td>6</td>
        <td>3</td>
        <td>null</td>
        <td>null</td>
        <td>null</td>
        <td>Restaurant Cancellation</td>
    </tr>
    <tr>
        <td>7</td>
        <td>2</td>
        <td>2020-01-08 21:30:45</td>
        <td>25km</td>
        <td>25mins</td>
        <td>null</td>
    </tr>
    <tr>
        <td>8</td>
        <td>2</td>
        <td>2020-01-10 00:15:02</td>
        <td>23.4 km</td>
        <td>15 minute</td>
        <td>null</td>
    </tr>
    <tr>
        <td>9</td>
        <td>2</td>
        <td>null</td>
        <td>null</td>
        <td>null</td>
        <td>Customer Cancellation</td>
    </tr>
    <tr>
        <td>10</td>
        <td>1</td>
        <td>2020-01-11 18:50:20</td>
        <td>10km</td>
        <td>10minutes</td>
        <td>null</td>
    </tr>
</table>




```sql
%%sql
PRAGMA table_info(runner_orders)
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



Replacing null with empty string under **cancellation** column


```sql
%%sql
UPDATE runner_orders
SET cancellation = ''
WHERE cancellation IS NULL

```

     * sqlite:///datasets/pizza_runner.sqlite
    3 rows affected.
    




    []



Replacing string 'null' with empty string under **cancellation** column


```sql
%%sql
UPDATE runner_orders
SET cancellation = ''
WHERE cancellation = 'null'

```

     * sqlite:///datasets/pizza_runner.sqlite
    3 rows affected.
    




    []




```sql
%%sql
SELECT DISTINCT cancellation
FROM runner_orders
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>cancellation</th>
    </tr>
    <tr>
        <td></td>
    </tr>
    <tr>
        <td>Restaurant Cancellation</td>
    </tr>
    <tr>
        <td>Customer Cancellation</td>
    </tr>
</table>



Eliminating str "km" from column **distance**


```sql
%%sql
UPDATE runner_orders
SET distance = CAST(replace(distance,'km','') AS real)

```

     * sqlite:///datasets/pizza_runner.sqlite
    10 rows affected.
    




    []




```python

```


```sql
%%sql
UPDATE runner_orders
SET duration = REPLACE(duration,'minutes','')
WHERE duration LIKE '%min%';
 
UPDATE runner_orders
SET duration = REPLACE(duration,'minute','')
WHERE duration LIKE '%min%';

UPDATE runner_orders
SET duration = REPLACE(duration,'mins','')
WHERE duration LIKE '%min%';

UPDATE runner_orders
SET duration = CAST(duration AS INTEGER) 

```

     * sqlite:///datasets/pizza_runner.sqlite
    6 rows affected.
    3 rows affected.
    2 rows affected.
    10 rows affected.
    




    []




```sql
%%sql
SELECT distance,duration
FROM runner_orders
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>distance</th>
        <th>duration</th>
    </tr>
    <tr>
        <td>20.0</td>
        <td>32</td>
    </tr>
    <tr>
        <td>20.0</td>
        <td>27</td>
    </tr>
    <tr>
        <td>13.4</td>
        <td>20</td>
    </tr>
    <tr>
        <td>23.4</td>
        <td>40</td>
    </tr>
    <tr>
        <td>10.0</td>
        <td>15</td>
    </tr>
    <tr>
        <td>0.0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>25.0</td>
        <td>25</td>
    </tr>
    <tr>
        <td>23.4</td>
        <td>15</td>
    </tr>
    <tr>
        <td>0.0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>10.0</td>
        <td>10</td>
    </tr>
</table>




```sql
%%sql

SELECT datetime(pickup_time)
FROM runner_orders
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>datetime(pickup_time)</th>
    </tr>
    <tr>
        <td>2020-01-01 18:15:34</td>
    </tr>
    <tr>
        <td>2020-01-01 19:10:54</td>
    </tr>
    <tr>
        <td>2020-01-03 00:12:37</td>
    </tr>
    <tr>
        <td>2020-01-04 13:53:03</td>
    </tr>
    <tr>
        <td>2020-01-08 21:10:57</td>
    </tr>
    <tr>
        <td>None</td>
    </tr>
    <tr>
        <td>2020-01-08 21:30:45</td>
    </tr>
    <tr>
        <td>2020-01-10 00:15:02</td>
    </tr>
    <tr>
        <td>None</td>
    </tr>
    <tr>
        <td>2020-01-11 18:50:20</td>
    </tr>
</table>




```sql
%%sql
select *
FROM runner_orders

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
        <th>cancellation</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>2020-01-01 18:15:34</td>
        <td>20.0</td>
        <td>32</td>
        <td></td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>2020-01-01 19:10:54</td>
        <td>20.0</td>
        <td>27</td>
        <td></td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>2020-01-03 00:12:37</td>
        <td>13.4</td>
        <td>20</td>
        <td></td>
    </tr>
    <tr>
        <td>4</td>
        <td>2</td>
        <td>2020-01-04 13:53:03</td>
        <td>23.4</td>
        <td>40</td>
        <td></td>
    </tr>
    <tr>
        <td>5</td>
        <td>3</td>
        <td>2020-01-08 21:10:57</td>
        <td>10.0</td>
        <td>15</td>
        <td></td>
    </tr>
    <tr>
        <td>6</td>
        <td>3</td>
        <td>null</td>
        <td>0.0</td>
        <td>0</td>
        <td>Restaurant Cancellation</td>
    </tr>
    <tr>
        <td>7</td>
        <td>2</td>
        <td>2020-01-08 21:30:45</td>
        <td>25.0</td>
        <td>25</td>
        <td></td>
    </tr>
    <tr>
        <td>8</td>
        <td>2</td>
        <td>2020-01-10 00:15:02</td>
        <td>23.4</td>
        <td>15</td>
        <td></td>
    </tr>
    <tr>
        <td>9</td>
        <td>2</td>
        <td>null</td>
        <td>0.0</td>
        <td>0</td>
        <td>Customer Cancellation</td>
    </tr>
    <tr>
        <td>10</td>
        <td>1</td>
        <td>2020-01-11 18:50:20</td>
        <td>10.0</td>
        <td>10</td>
        <td></td>
    </tr>
</table>




```sql
%%sql
PRAGMA TABLE_INFO(runners_orders)
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



### pizza_name

There are only two entries for **pizza_name** with correct data type


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
PRAGMA TABLE_INFO(pizza_names)
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
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>pizza_name</td>
        <td>TEXT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>



### pizza_recipes


```sql
%%sql
SELECT *
FROM pizza_recipes
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>pizza_id</th>
        <th>toppings</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1, 2, 3, 4, 5, 6, 8, 10</td>
    </tr>
    <tr>
        <td>2</td>
        <td>4, 6, 7, 9, 11, 12</td>
    </tr>
</table>




```sql
%%sql
PRAGMA TABLE_INFO(pizza_recipes)
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
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>toppings</td>
        <td>TEXT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>



### pizza_toppings


```sql
%%sql
SELECT *
FROM pizza_toppings
```

     * sqlite:///datasets/pizza_runner.sqlite
    Done.
    




<table>
    <tr>
        <th>topping_id</th>
        <th>topping_name</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Bacon</td>
    </tr>
    <tr>
        <td>2</td>
        <td>BBQ Sauce</td>
    </tr>
    <tr>
        <td>3</td>
        <td>Beef</td>
    </tr>
    <tr>
        <td>4</td>
        <td>Cheese</td>
    </tr>
    <tr>
        <td>5</td>
        <td>Chicken</td>
    </tr>
    <tr>
        <td>6</td>
        <td>Mushrooms</td>
    </tr>
    <tr>
        <td>7</td>
        <td>Onions</td>
    </tr>
    <tr>
        <td>8</td>
        <td>Pepperoni</td>
    </tr>
    <tr>
        <td>9</td>
        <td>Peppers</td>
    </tr>
    <tr>
        <td>10</td>
        <td>Salami</td>
    </tr>
    <tr>
        <td>11</td>
        <td>Tomatoes</td>
    </tr>
    <tr>
        <td>12</td>
        <td>Tomato Sauce</td>
    </tr>
</table>




```sql
%%sql
PRAGMA TABLE_INFO(pizza_toppings)
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
        <td>topping_id</td>
        <td>INTEGER</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
        <td>topping_name</td>
        <td>TEXT</td>
        <td>0</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>



#### Prev  : 🍕 <a href='case_study_2_pizza_part_1.ipynb'> Pizza Runner PART 1 </a>

#### Next : 🍕 <a href='case_study_2_pizza_part_3.ipynb'> Pizza Runner PART 3 </a>

#### Back to : [Top](#top) ▲

<a href='https://github.com/sumedhadewan/8-week-sql-challenge' target='_blank'>See this Project on Github</a>


```python

```

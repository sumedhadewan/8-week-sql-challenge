### Case Study # 4 Part 2
## Data Bank

source : <a href='https://8weeksqlchallenge.com/case-study-4/'> 8 Week SQL challenges </a>


* Case Study Questions

    1. Customer Node Exploration
    2. Customer Transactions
    3. Data Allocation Challenge
    4. Extra challenge


```python
%load_ext sql
```

    The sql extension is already loaded. To reload it, use:
      %reload_ext sql



```python
%sql postgresql://postgres:dewan@localhost/databank
```

### <font color='green'>Customer Node Exploration</font>


```sql
%%sql
SELECT *
FROM customer_nodes
LIMIT 3
```

     * postgresql://postgres:***@localhost/databank
    3 rows affected.





<table>
    <tr>
        <th>customer_id</th>
        <th>region_id</th>
        <th>node_id</th>
        <th>start_date</th>
        <th>end_date</th>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>4</td>
        <td>2020-01-02</td>
        <td>2020-01-03</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>5</td>
        <td>2020-01-03</td>
        <td>2020-01-17</td>
    </tr>
    <tr>
        <td>3</td>
        <td>5</td>
        <td>4</td>
        <td>2020-01-27</td>
        <td>2020-02-18</td>
    </tr>
</table>




```sql
%%sql
SELECT COUNT(*) AS records,COUNT(DISTINCT customer_id) AS Customer_count,
COUNT(DISTINCT region_id) AS Region_count ,COUNT(DISTINCT node_id) AS Node_count
FROM customer_nodes
```

     * postgresql://postgres:***@localhost/databank
    1 rows affected.





<table>
    <tr>
        <th>records</th>
        <th>customer_count</th>
        <th>region_count</th>
        <th>node_count</th>
    </tr>
    <tr>
        <td>3500</td>
        <td>500</td>
        <td>5</td>
        <td>5</td>
    </tr>
</table>



There are 5 distinct nodes id for each region


```sql
%%sql
SELECT region_id, COUNT(DISTINCT node_id) AS unique_node_count
FROM customer_nodes
GROUP BY 1
ORDER BY 1
```

     * postgresql://postgres:***@localhost/databank
    5 rows affected.





<table>
    <tr>
        <th>region_id</th>
        <th>unique_node_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>5</td>
    </tr>
    <tr>
        <td>2</td>
        <td>5</td>
    </tr>
    <tr>
        <td>3</td>
        <td>5</td>
    </tr>
    <tr>
        <td>4</td>
        <td>5</td>
    </tr>
    <tr>
        <td>5</td>
        <td>5</td>
    </tr>
</table>




```sql
%%sql
SELECT COUNT(DISTINCT customer_id) , COUNT(DISTINCT txn_type)
FROM customer_transactions
LIMIT 5
```

     * postgresql://postgres:***@localhost/databank
    1 rows affected.





<table>
    <tr>
        <th>count</th>
        <th>count_1</th>
    </tr>
    <tr>
        <td>500</td>
        <td>3</td>
    </tr>
</table>



### 1. How many unique nodes are there on the Data Bank system?

There are 5 unique nodes on Data bank system


```sql
%%sql
SELECT COUNT(DISTINCT node_id) AS unique_nodes
FROM customer_nodes
```

     * postgresql://postgres:***@localhost/databank
    1 rows affected.





<table>
    <tr>
        <th>unique_nodes</th>
    </tr>
    <tr>
        <td>5</td>
    </tr>
</table>



### 2. What is the number of nodes per region?

Each region has unique 5 nodes


```sql
%%sql
SELECT region_id, 
    region_name,
    COUNT(DISTINCT node_id) AS nodes_count
FROM customer_nodes
JOIN regions USING(region_id)
GROUP BY 1,2
```

     * postgresql://postgres:***@localhost/databank
    5 rows affected.





<table>
    <tr>
        <th>region_id</th>
        <th>region_name</th>
        <th>nodes_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Australia</td>
        <td>5</td>
    </tr>
    <tr>
        <td>2</td>
        <td>America</td>
        <td>5</td>
    </tr>
    <tr>
        <td>3</td>
        <td>Africa</td>
        <td>5</td>
    </tr>
    <tr>
        <td>4</td>
        <td>Asia</td>
        <td>5</td>
    </tr>
    <tr>
        <td>5</td>
        <td>Europe</td>
        <td>5</td>
    </tr>
</table>



### 3. How many customers are allocated to each region?


```sql
%%sql
SELECT region_id, 
    region_name , 
    COUNT(DISTINCT customer_id) AS customer_unique_count
FROM customer_nodes
JOIN regions USING(region_id)
GROUP BY 1,2
```

     * postgresql://postgres:***@localhost/databank
    5 rows affected.





<table>
    <tr>
        <th>region_id</th>
        <th>region_name</th>
        <th>customer_unique_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Australia</td>
        <td>110</td>
    </tr>
    <tr>
        <td>2</td>
        <td>America</td>
        <td>105</td>
    </tr>
    <tr>
        <td>3</td>
        <td>Africa</td>
        <td>102</td>
    </tr>
    <tr>
        <td>4</td>
        <td>Asia</td>
        <td>95</td>
    </tr>
    <tr>
        <td>5</td>
        <td>Europe</td>
        <td>88</td>
    </tr>
</table>



### 4. How many days on average are customers reallocated to a different node?


```python
from datetime import datetime

# dates in string format
str_d1 = '2020/01/04'
str_d2 = '2020/01/14'

# convert string to date object
d1 = datetime.strptime(str_d1, "%Y/%m/%d")
d2 = datetime.strptime(str_d2, "%Y/%m/%d")

# difference between dates in timedelta
delta = d2 - d1
print(f'Difference is {delta.days} days')
```

    Difference is 10 days



```sql
%%sql
WITH node_cte AS(
    SELECT customer_id,node_id, end_date - start_date AS diff
    FROM customer_nodes
    WHERE end_date != '9999-12-31'
    GROUP BY customer_id,node_id ,end_date,start_date
), diff_cte AS(
    SELECT 
        customer_id,node_id,SUM(diff) AS sum_diff
    FROM node_cte
    GROUP BY 1 ,2
)

SELECT ROUND(AVG(sum_diff),0) AS avg_reallocation_days
FROM diff_cte
```

     * postgresql://postgres:***@localhost/databank
    1 rows affected.





<table>
    <tr>
        <th>avg_reallocation_days</th>
    </tr>
    <tr>
        <td>24</td>
    </tr>
</table>



### 5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?


```sql
%%sql

WITH region_cte AS(
    SELECT customer_id,region_id, end_date - start_date AS diff
    FROM customer_nodes
    WHERE end_date != '9999-12-31'
    GROUP BY customer_id,region_id ,end_date,start_date
)

SELECT region_name, percentile_disc(0.5) WITHIN GROUP (ORDER BY diff) AS median,
percentile_disc(0.8) WITHIN GROUP (ORDER BY diff) AS "80th percentile",
percentile_disc(0.95) WITHIN GROUP (ORDER BY diff) AS "95th percentile"
FROM  region_cte
JOIN regions
ON region_cte.region_id = regions.region_id
GROUP BY region_name

```

     * postgresql://postgres:***@localhost/databank
    5 rows affected.





<table>
    <tr>
        <th>region_name</th>
        <th>median</th>
        <th>80th percentile</th>
        <th>95th percentile</th>
    </tr>
    <tr>
        <td>Africa</td>
        <td>15</td>
        <td>24</td>
        <td>28</td>
    </tr>
    <tr>
        <td>America</td>
        <td>15</td>
        <td>23</td>
        <td>28</td>
    </tr>
    <tr>
        <td>Asia</td>
        <td>15</td>
        <td>23</td>
        <td>28</td>
    </tr>
    <tr>
        <td>Australia</td>
        <td>15</td>
        <td>23</td>
        <td>28</td>
    </tr>
    <tr>
        <td>Europe</td>
        <td>15</td>
        <td>24</td>
        <td>28</td>
    </tr>
</table>



### <font color='green'>Customer Transactions</font>


    What is the unique count and total amount for each transaction type?
    What is the average total historical deposit counts and amounts for all customers?
    For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
    What is the closing balance for each customer at the end of the month?
    What is the percentage of customers who increase their closing balance by more than 5%?


### 1. What is the unique count and total amount for each transaction type?


```sql
%%sql
SELECT 
    DISTINCT txn_type AS "Transaction type",
    COUNT(customer_id) AS "Count",
    SUM(txn_amount) AS "Total Amount"
FROM customer_transactions
GROUP BY 1

```

     * postgresql://postgres:***@localhost/databank
    3 rows affected.





<table>
    <tr>
        <th>Transaction type</th>
        <th>Count</th>
        <th>Total Amount</th>
    </tr>
    <tr>
        <td>deposit</td>
        <td>2671</td>
        <td>1359168</td>
    </tr>
    <tr>
        <td>purchase</td>
        <td>1617</td>
        <td>806537</td>
    </tr>
    <tr>
        <td>withdrawal</td>
        <td>1580</td>
        <td>793003</td>
    </tr>
</table>



### 2. What is the average total historical deposit counts and amounts for all customers?


```sql
%%sql
WITH deposite_cte AS(
    SELECT customer_id,
        COUNT(*) AS deposite_count,
        SUM(txn_amount) AS deposite_amount
    FROM customer_transactions
    WHERE txn_type = 'deposit'
    GROUP BY 1
    LIMIT 5
)
SELECT
    ROUND(AVG(deposite_count),2) AS avg_deposite_count,
    ROUND(AVG(deposite_amount),2) AS avg_deposite_amount
FROM deposite_cte

```

     * postgresql://postgres:***@localhost/databank
    1 rows affected.





<table>
    <tr>
        <th>avg_deposite_count</th>
        <th>avg_deposite_amount</th>
    </tr>
    <tr>
        <td>4.80</td>
        <td>2553.20</td>
    </tr>
</table>



### 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?


```sql
%%sql
WITH month_cte AS(
    SELECT
        TO_CHAR(txn_date,'Month')AS month,
        customer_id,
        SUM(CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END) AS deposit_count,
        SUM(CASE WHEN txn_type = 'purchase' THEN 1 ELSE 0 END) AS purchase_count,
        SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS drawal_count
    FROM customer_transactions
    GROUP BY 1,2
)
SELECT
    month,
    COUNT(customer_id) AS customer_count
FROM month_cte
WHERE (deposit_count > 1) AND (purchase_count >=1 OR drawal_count>=1)
GROUP BY month
ORDER BY TO_DATE(month,'Month')
```

     * postgresql://postgres:***@localhost/databank
    4 rows affected.





<table>
    <tr>
        <th>month</th>
        <th>customer_count</th>
    </tr>
    <tr>
        <td>January  </td>
        <td>168</td>
    </tr>
    <tr>
        <td>February </td>
        <td>181</td>
    </tr>
    <tr>
        <td>March    </td>
        <td>192</td>
    </tr>
    <tr>
        <td>April    </td>
        <td>70</td>
    </tr>
</table>



### 4. What is the closing balance for each customer at the end of the month?


```sql
%%sql
WITH customer_cte AS(
    SELECT customer_id,
    TO_CHAR(txn_date,'Month') AS month,
    txn_type,
        CASE
        WHEN txn_type = 'withdrawal' OR txn_type = 'purchase'  THEN txn_amount* -1
        ELSE  txn_amount END AS txn_amount
    FROM customer_transactions
), transaction_cte AS(
    SELECT 
        customer_id,
        month,
        SUM(txn_amount) AS trans_amount
    FROM customer_cte
    GROUP BY 1,2
    ORDER BY customer_id,to_date(month,'Month')
)
SELECT
    customer_id,
    month,
    trans_amount,
    SUM(trans_amount) OVER(PARTITION BY customer_id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW ) AS closing_balance
FROM transaction_cte
LIMIT 20
```

     * postgresql://postgres:***@localhost/databank
    20 rows affected.





<table>
    <tr>
        <th>customer_id</th>
        <th>month</th>
        <th>trans_amount</th>
        <th>closing_balance</th>
    </tr>
    <tr>
        <td>1</td>
        <td>January  </td>
        <td>312</td>
        <td>312</td>
    </tr>
    <tr>
        <td>1</td>
        <td>March    </td>
        <td>-952</td>
        <td>-640</td>
    </tr>
    <tr>
        <td>2</td>
        <td>January  </td>
        <td>549</td>
        <td>549</td>
    </tr>
    <tr>
        <td>2</td>
        <td>March    </td>
        <td>61</td>
        <td>610</td>
    </tr>
    <tr>
        <td>3</td>
        <td>January  </td>
        <td>144</td>
        <td>144</td>
    </tr>
    <tr>
        <td>3</td>
        <td>February </td>
        <td>-965</td>
        <td>-821</td>
    </tr>
    <tr>
        <td>3</td>
        <td>March    </td>
        <td>-401</td>
        <td>-1222</td>
    </tr>
    <tr>
        <td>3</td>
        <td>April    </td>
        <td>493</td>
        <td>-729</td>
    </tr>
    <tr>
        <td>4</td>
        <td>January  </td>
        <td>848</td>
        <td>848</td>
    </tr>
    <tr>
        <td>4</td>
        <td>March    </td>
        <td>-193</td>
        <td>655</td>
    </tr>
    <tr>
        <td>5</td>
        <td>January  </td>
        <td>954</td>
        <td>954</td>
    </tr>
    <tr>
        <td>5</td>
        <td>March    </td>
        <td>-2877</td>
        <td>-1923</td>
    </tr>
    <tr>
        <td>5</td>
        <td>April    </td>
        <td>-490</td>
        <td>-2413</td>
    </tr>
    <tr>
        <td>6</td>
        <td>January  </td>
        <td>733</td>
        <td>733</td>
    </tr>
    <tr>
        <td>6</td>
        <td>February </td>
        <td>-785</td>
        <td>-52</td>
    </tr>
    <tr>
        <td>6</td>
        <td>March    </td>
        <td>392</td>
        <td>340</td>
    </tr>
    <tr>
        <td>7</td>
        <td>January  </td>
        <td>964</td>
        <td>964</td>
    </tr>
    <tr>
        <td>7</td>
        <td>February </td>
        <td>2209</td>
        <td>3173</td>
    </tr>
    <tr>
        <td>7</td>
        <td>March    </td>
        <td>-640</td>
        <td>2533</td>
    </tr>
    <tr>
        <td>7</td>
        <td>April    </td>
        <td>90</td>
        <td>2623</td>
    </tr>
</table>



### 5. What is the percentage of customers who increase their closing balance by more than 5%?

We will calculate customer whose closing balance is more than 5% of their opening balance


```sql
%%sql
WITH customer_cte AS(
    SELECT customer_id,
    txn_date,
    txn_type,
        CASE
        WHEN txn_type = 'withdrawal' OR txn_type = 'purchase'  THEN txn_amount* -1
        ELSE  txn_amount END AS txn_amount
    FROM customer_transactions
), transaction_cte AS(
    SELECT 
        customer_id,
        EXTRACT(MONTH FROM txn_date) AS month,
        SUM(txn_amount) AS trans_amount
    FROM customer_cte
    GROUP BY 1,2
    ORDER BY 1,2
),closing_balance_cte AS(
    SELECT
        customer_id,
        month,
        trans_amount,
        SUM(trans_amount) OVER(PARTITION BY customer_id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW ) AS closing_balance
    FROM transaction_cte
    ORDER BY 1,2
),customer_opening_closing AS(
    SELECT
        DISTINCT customer_id,
        opening_balance,
        closing_balance
    FROM(
        SELECT customer_id,
            FIRST_VALUE(closing_balance) OVER (PARTITION BY customer_id) opening_balance,
            LAST_VALUE(closing_balance) OVER (PARTITION BY customer_id) closing_balance
        FROM closing_balance_cte
        
    ) AS sub1
)
SELECT ROUND(COUNT(DISTINCT customer_id)*100.0/(
    SELECT COUNT(DISTINCT customer_id)
    FROM customer_transactions
),2) AS "% customer",
    COUNT(DISTINCT customer_id) AS "customer count"
FROM customer_opening_closing
WHERE closing_balance >= (opening_balance*0.5 + opening_balance)
LIMIT 10
```

     * postgresql://postgres:***@localhost/databank
    1 rows affected.





<table>
    <tr>
        <th>% customer</th>
        <th>customer count</th>
    </tr>
    <tr>
        <td>30.80</td>
        <td>154</td>
    </tr>
</table>



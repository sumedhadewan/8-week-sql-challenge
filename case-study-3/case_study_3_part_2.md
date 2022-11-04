### Case Study # 3 Part 2
## Foodie-Fi

source : <a href='https://8weeksqlchallenge.com/case-study-3/'> 8 Week SQL challenges </a>

<a id='top'></a>

### <font color='green' >A. Customer Journey</font>

Based off the 5 sample customers provided in the sample from the `subscriptions` table, write a brief description about each customer’s onboarding journey.




```python
%load_ext sql
```


```python
%sql postgresql://postgres:dewan@localhost/foodie
```

There are 1000 Distinct customer_id in `subscription`


```sql
%%sql
SELECT COUNT(DISTINCT customer_id)
FROM subscriptions

```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>count</th>
    </tr>
    <tr>
        <td>1000</td>
    </tr>
</table>



#### Generating random customer id


```python
import random
rand_list = []
random.sample(range(1,1000),5)
```




    [459, 565, 697, 143, 637]



#### Customer id  459

- First started 7 days free trial on 7th Dec 2020
- automatically continued with pro monthly plan after 7 days free trial ends
- 3 months later upgraded to pro annual plan


```sql
%%sql
SELECT *
FROM subscriptions
JOIN plans USING (plan_id)
WHERE customer_id = 459
ORDER BY start_date
```

     * postgresql://postgres:***@localhost/foodie
    3 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>customer_id</th>
        <th>start_date</th>
        <th>plan_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>0</td>
        <td>459</td>
        <td>2020-12-07</td>
        <td>trial</td>
        <td>0.00</td>
    </tr>
    <tr>
        <td>2</td>
        <td>459</td>
        <td>2020-12-14</td>
        <td>pro monthly</td>
        <td>19.90</td>
    </tr>
    <tr>
        <td>3</td>
        <td>459</td>
        <td>2021-03-14</td>
        <td>pro annual</td>
        <td>199.00</td>
    </tr>
</table>



#### Customer Id 565

- First started 7 days free trial on 2nd Jan 2020
- automatically continued with pro monthly plan after 7 days free trial ends


```sql
%%sql
SELECT *
FROM subscriptions
JOIN plans USING (plan_id)
WHERE customer_id = 565
ORDER BY start_date
```

     * postgresql://postgres:***@localhost/foodie
    2 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>customer_id</th>
        <th>start_date</th>
        <th>plan_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>0</td>
        <td>565</td>
        <td>2020-01-02</td>
        <td>trial</td>
        <td>0.00</td>
    </tr>
    <tr>
        <td>2</td>
        <td>565</td>
        <td>2020-01-09</td>
        <td>pro monthly</td>
        <td>19.90</td>
    </tr>
</table>



#### Customer Id 697

- First started 7 days free trial on 23 Jan 2020
- downgraded to basic monthly plan after trial ended.


```sql
%%sql
SELECT *
FROM subscriptions
JOIN plans USING (plan_id)
WHERE customer_id = 697
ORDER BY start_date
```

     * postgresql://postgres:***@localhost/foodie
    2 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>customer_id</th>
        <th>start_date</th>
        <th>plan_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>0</td>
        <td>697</td>
        <td>2020-01-23</td>
        <td>trial</td>
        <td>0.00</td>
    </tr>
    <tr>
        <td>1</td>
        <td>697</td>
        <td>2020-01-30</td>
        <td>basic monthly</td>
        <td>9.90</td>
    </tr>
</table>



#### Customer Id 697

- First started 7 days free trial on 20 Dec 2020
- downgraded to basic monthly plan after trial ended.
- cancelled Foodie-Fi service in less than 3 months.


```sql
%%sql
SELECT *
FROM subscriptions
JOIN plans USING (plan_id)
WHERE customer_id = 143
ORDER BY start_date
```

     * postgresql://postgres:***@localhost/foodie
    3 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>customer_id</th>
        <th>start_date</th>
        <th>plan_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>0</td>
        <td>143</td>
        <td>2020-12-20</td>
        <td>trial</td>
        <td>0.00</td>
    </tr>
    <tr>
        <td>1</td>
        <td>143</td>
        <td>2020-12-27</td>
        <td>basic monthly</td>
        <td>9.90</td>
    </tr>
    <tr>
        <td>4</td>
        <td>143</td>
        <td>2021-03-03</td>
        <td>churn</td>
        <td>None</td>
    </tr>
</table>



#### Customer Id 637

- First started 7 days free trial on 20 sept 2020
- downgraded to basic monthly plan after trial ended.


```sql
%%sql
SELECT *
FROM subscriptions
JOIN plans USING (plan_id)
WHERE customer_id = 637
ORDER BY start_date
```

     * postgresql://postgres:***@localhost/foodie
    2 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>customer_id</th>
        <th>start_date</th>
        <th>plan_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>0</td>
        <td>637</td>
        <td>2020-09-20</td>
        <td>trial</td>
        <td>0.00</td>
    </tr>
    <tr>
        <td>1</td>
        <td>637</td>
        <td>2020-09-27</td>
        <td>basic monthly</td>
        <td>9.90</td>
    </tr>
</table>



<a id="analysis"></a>
### <font color='green' > B. Data Analysis Questions </font>

### How many customers has Foodie-Fi ever had?


```sql
%%sql
SELECT COUNT(DISTINCT customer_id) AS unique_customer_id
FROM subscriptions
```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>unique_customer_id</th>
    </tr>
    <tr>
        <td>1000</td>
    </tr>
</table>



### What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value


```sql
%%sql

SELECT 
    EXTRACT(MONTH FROM start_date) AS month ,
    COUNT(start_date) AS count_started
FROM subscriptions
WHERE plan_id = 0
GROUP BY 1
ORDER BY 2 DESC

```

     * postgresql://postgres:***@localhost/foodie
    12 rows affected.
    




<table>
    <tr>
        <th>month</th>
        <th>count_started</th>
    </tr>
    <tr>
        <td>3</td>
        <td>94</td>
    </tr>
    <tr>
        <td>7</td>
        <td>89</td>
    </tr>
    <tr>
        <td>5</td>
        <td>88</td>
    </tr>
    <tr>
        <td>1</td>
        <td>88</td>
    </tr>
    <tr>
        <td>8</td>
        <td>88</td>
    </tr>
    <tr>
        <td>9</td>
        <td>87</td>
    </tr>
    <tr>
        <td>12</td>
        <td>84</td>
    </tr>
    <tr>
        <td>4</td>
        <td>81</td>
    </tr>
    <tr>
        <td>6</td>
        <td>79</td>
    </tr>
    <tr>
        <td>10</td>
        <td>79</td>
    </tr>
    <tr>
        <td>11</td>
        <td>75</td>
    </tr>
    <tr>
        <td>2</td>
        <td>68</td>
    </tr>
</table>



### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name


```sql
%%sql
SELECT 
    plan_id,
    plan_name,
    COUNT(start_date) AS count_of_events
FROM subscriptions
JOIN plans USING(plan_id)
WHERE EXTRACT(YEAR FROM start_date) > 2020
GROUP BY 1,2
ORDER BY 3 DESC
```

     * postgresql://postgres:***@localhost/foodie
    4 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>plan_name</th>
        <th>count_of_events</th>
    </tr>
    <tr>
        <td>4</td>
        <td>churn</td>
        <td>71</td>
    </tr>
    <tr>
        <td>3</td>
        <td>pro annual</td>
        <td>63</td>
    </tr>
    <tr>
        <td>2</td>
        <td>pro monthly</td>
        <td>60</td>
    </tr>
    <tr>
        <td>1</td>
        <td>basic monthly</td>
        <td>8</td>
    </tr>
</table>



### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?


```sql
%%sql
SELECT 
    COUNT(DISTINCT customer_id) AS churned_customers,
    ROUND(COUNT(DISTINCT customer_id)*1.0/(
        SELECT COUNT(DISTINCT customer_id)
        FROM subscriptions
    )*100,2) AS churn_perc
FROM subscriptions
WHERE plan_id = 4
```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>churned_customers</th>
        <th>churn_perc</th>
    </tr>
    <tr>
        <td>307</td>
        <td>30.70</td>
    </tr>
</table>



### 5.  How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?


```sql
%%sql
CREATE TEMP TABLE next_plan_table AS

SELECT 
    customer_id,
    plan_id,
    start_date,
    LEAD(plan_id,1) OVER(PARTITION BY customer_id ORDER BY start_date) AS next_plan
FROM subscriptions
ORDER BY customer_id
```

     * postgresql://postgres:***@localhost/foodie
    2650 rows affected.
    




    []




```sql
%%sql
WITH churned_list AS(
    SELECT *
    FROM next_plan_table
    WHERE plan_id = 0 AND next_plan = 4
)

SELECT
    COUNT(DISTINCT customer_id) AS churned_after_trial,
    ROUND(COUNT(DISTINCT customer_id) * 100.0/ (
        SELECT COUNT(DISTINCT customer_id)
        FROM subscriptions
    ),2) AS churned_perc_after_trial
FROM churned_list
```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>churned_after_trial</th>
        <th>churned_perc_after_trial</th>
    </tr>
    <tr>
        <td>92</td>
        <td>9.20</td>
    </tr>
</table>



### 6. What is the number and percentage of customer plans after their initial free trial?


```sql
%%sql
SELECT *
FROM next_plan_table
LIMIT 5
```

     * postgresql://postgres:***@localhost/foodie
    5 rows affected.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>plan_id</th>
        <th>start_date</th>
        <th>next_plan</th>
    </tr>
    <tr>
        <td>1</td>
        <td>0</td>
        <td>2020-08-01</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>2020-08-08</td>
        <td>None</td>
    </tr>
    <tr>
        <td>2</td>
        <td>0</td>
        <td>2020-09-20</td>
        <td>3</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>2020-09-27</td>
        <td>None</td>
    </tr>
    <tr>
        <td>3</td>
        <td>0</td>
        <td>2020-01-13</td>
        <td>1</td>
    </tr>
</table>




```sql
%%sql
SELECT 
    next_plan,
    (
        SELECT plan_name
        FROM plans
        WHERE plans.plan_id = next_plan_table.next_plan
    ) AS next_plan_name,
    COUNT(DISTINCT customer_id) AS count_customer_after_trial,
    ROUND(COUNT(DISTINCT customer_id) * 100.0/ (
        SELECT COUNT(DISTINCT customer_id)
        FROM subscriptions
    ),2) AS perc_customer_after_trial
FROM next_plan_table
WHERE plan_id = 0 AND next_plan IS NOT NULL
GROUP BY 1,2
```

     * postgresql://postgres:***@localhost/foodie
    4 rows affected.
    




<table>
    <tr>
        <th>next_plan</th>
        <th>next_plan_name</th>
        <th>count_customer_after_trial</th>
        <th>perc_customer_after_trial</th>
    </tr>
    <tr>
        <td>1</td>
        <td>basic monthly</td>
        <td>546</td>
        <td>54.60</td>
    </tr>
    <tr>
        <td>2</td>
        <td>pro monthly</td>
        <td>325</td>
        <td>32.50</td>
    </tr>
    <tr>
        <td>3</td>
        <td>pro annual</td>
        <td>37</td>
        <td>3.70</td>
    </tr>
    <tr>
        <td>4</td>
        <td>churn</td>
        <td>92</td>
        <td>9.20</td>
    </tr>
</table>



### 7.  What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?


```sql
%%sql
WITH temp AS(
    SELECT *,
        row_number() OVER(PARTITION BY customer_id ORDER BY start_date DESC) As latest_plan 
    FROM subscriptions
    WHERE start_date <= '2020-12-31'
)
SELECT
    plan_id,
    plan_name,
    COUNT(DISTINCT customer_id) AS customer_count,
    ROUND(COUNT(DISTINCT customer_id)*100.0/(
        SELECT COUNT(DISTINCT customer_id)
        FROM subscriptions
    ),2) AS perc_breakdown
    
FROM temp
JOIN plans USING(plan_id)
WHERE latest_plan = 1
GROUP BY 1,2
ORDER BY 4 DESC
```

     * postgresql://postgres:***@localhost/foodie
    5 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>plan_name</th>
        <th>customer_count</th>
        <th>perc_breakdown</th>
    </tr>
    <tr>
        <td>2</td>
        <td>pro monthly</td>
        <td>326</td>
        <td>32.60</td>
    </tr>
    <tr>
        <td>4</td>
        <td>churn</td>
        <td>236</td>
        <td>23.60</td>
    </tr>
    <tr>
        <td>1</td>
        <td>basic monthly</td>
        <td>224</td>
        <td>22.40</td>
    </tr>
    <tr>
        <td>3</td>
        <td>pro annual</td>
        <td>195</td>
        <td>19.50</td>
    </tr>
    <tr>
        <td>0</td>
        <td>trial</td>
        <td>19</td>
        <td>1.90</td>
    </tr>
</table>



### 8. How many customers have upgraded to an annual plan in 2020?


```sql
%%sql
SELECT COUNT(customer_id)
FROM  subscriptions
WHERE plan_id = 3 AND EXTRACT(YEAR FROM start_date) = '2020'
```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>count</th>
    </tr>
    <tr>
        <td>195</td>
    </tr>
</table>



### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?


```sql
%%sql
DROP TABLE IF EXISTS upgrade_table; 
CREATE TEMP TABLE upgrade_table AS
SELECT 
    *,
    LAST_VALUE(start_date) OVER(PARTITION BY customer_id) AS lastest_date,
    LAST_VALUE(plan_id) OVER(PARTITION BY customer_id) AS latest_plan
FROM subscriptions
```

     * postgresql://postgres:***@localhost/foodie
    Done.
    2650 rows affected.
    




    []




```sql
%%sql

SELECT
    ROUND(AVG(diff)) AS avg_day_to_upgrade
FROM
(
    SELECT *,
        lastest_date - start_date AS diff
    FROM upgrade_table
    WHERE plan_id = 0 AND latest_plan = 3
) AS sub
```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>avg_day_to_upgrade</th>
    </tr>
    <tr>
        <td>106</td>
    </tr>
</table>



### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)


```sql
%%sql
WITH temp_diff_table AS(
SELECT *,
    lastest_date - start_date AS diff
FROM upgrade_table
WHERE plan_id = 0 AND latest_plan = 3
)
SELECT
    min(diff),
    max(diff)
FROM temp_diff_table
    
```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>min</th>
        <th>max</th>
    </tr>
    <tr>
        <td>7</td>
        <td>346</td>
    </tr>
</table>




```sql
%%sql
WITH temp_diff_table AS(
SELECT *,
    lastest_date - start_date AS diff
FROM upgrade_table
WHERE plan_id = 0 AND latest_plan = 3
),
bins AS(
    SELECT 
        generate_series(0,330,30) AS lower,
        generate_series(30,360,30) AS upper
)
SELECT 
    lower AS lower_limit_days,
    upper AS upper_limit_days,
    count(customer_id)
FROM bins
LEFT JOIN temp_diff_table
ON diff> lower and diff <= upper
GROUP BY 1,2
ORDER BY 1
```

     * postgresql://postgres:***@localhost/foodie
    12 rows affected.
    




<table>
    <tr>
        <th>lower_limit_days</th>
        <th>upper_limit_days</th>
        <th>count</th>
    </tr>
    <tr>
        <td>0</td>
        <td>30</td>
        <td>47</td>
    </tr>
    <tr>
        <td>30</td>
        <td>60</td>
        <td>22</td>
    </tr>
    <tr>
        <td>60</td>
        <td>90</td>
        <td>33</td>
    </tr>
    <tr>
        <td>90</td>
        <td>120</td>
        <td>34</td>
    </tr>
    <tr>
        <td>120</td>
        <td>150</td>
        <td>42</td>
    </tr>
    <tr>
        <td>150</td>
        <td>180</td>
        <td>36</td>
    </tr>
    <tr>
        <td>180</td>
        <td>210</td>
        <td>26</td>
    </tr>
    <tr>
        <td>210</td>
        <td>240</td>
        <td>4</td>
    </tr>
    <tr>
        <td>240</td>
        <td>270</td>
        <td>5</td>
    </tr>
    <tr>
        <td>270</td>
        <td>300</td>
        <td>1</td>
    </tr>
    <tr>
        <td>300</td>
        <td>330</td>
        <td>1</td>
    </tr>
    <tr>
        <td>330</td>
        <td>360</td>
        <td>1</td>
    </tr>
</table>




```sql
%%sql
SELECT *
FROM generate_series(0,30,10)
```

     * postgresql://postgres:***@localhost/foodie
    4 rows affected.
    




<table>
    <tr>
        <th>generate_series</th>
    </tr>
    <tr>
        <td>0</td>
    </tr>
    <tr>
        <td>10</td>
    </tr>
    <tr>
        <td>20</td>
    </tr>
    <tr>
        <td>30</td>
    </tr>
</table>



### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?


```sql
%%sql
SELECT COUNT(customer_id) AS downgraded
FROM next_plan_table
WHERE plan_id = 2 AND next_plan = 1 AND EXTRACT(YEAR FROM start_date) = 2020

```

     * postgresql://postgres:***@localhost/foodie
    1 rows affected.
    




<table>
    <tr>
        <th>downgraded</th>
    </tr>
    <tr>
        <td>0</td>
    </tr>
</table>




#### Back to : [Top](#top) ▲


#### Prev :  <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/case_study_3_part_1.md'> Foodie-Fi PART 1 </a>

#### Back to : <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/'>This Project Table of Content</a>

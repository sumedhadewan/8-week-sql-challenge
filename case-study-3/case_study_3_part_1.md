### Case Study # 3 Part 1
## Foodie-Fi

source : <a href='https://8weeksqlchallenge.com/case-study-3/'> 8 Week SQL challenges </a>

<img src = 'images/foodie.png' style='width:450px'>

<a id='top'></a>

### Table of Contents :
* [Datasets](#dataset)
* [Entity Relation Diagram](#diagram)
* [Case Questions](#question)
    1. <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/case_study_3_part_2.md'>Customer Journey</a>
    2. <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/case_study_3_part_2.md#analysis'>Data analysis questions</a>

#### Loading sql extension and connecting to `foodie` database with postgresql


```python
%load_ext sql

```


```python
%sql postgresql://postgres:dewan@localhost/foodie
```

#### Checking tables inside foodie database


```sql
%%sql
SELECT *
FROM information_schema.tables
WHERE table_schema = 'public'
```

     * postgresql://postgres:***@localhost/foodie
    2 rows affected.
    




<table>
    <tr>
        <th>table_catalog</th>
        <th>table_schema</th>
        <th>table_name</th>
        <th>table_type</th>
        <th>self_referencing_column_name</th>
        <th>reference_generation</th>
        <th>user_defined_type_catalog</th>
        <th>user_defined_type_schema</th>
        <th>user_defined_type_name</th>
        <th>is_insertable_into</th>
        <th>is_typed</th>
        <th>commit_action</th>
    </tr>
    <tr>
        <td>foodie</td>
        <td>public</td>
        <td>plans</td>
        <td>BASE TABLE</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>YES</td>
        <td>NO</td>
        <td>None</td>
    </tr>
    <tr>
        <td>foodie</td>
        <td>public</td>
        <td>subscriptions</td>
        <td>BASE TABLE</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>YES</td>
        <td>NO</td>
        <td>None</td>
    </tr>
</table>



<a id = 'dataset'></a>
### Datasets

There are two tables

1. `plans`

Customers can choose which plans to join Foodie-Fi when they first sign up.

 * Basic plan : customers have limited access and can only stream their videos and is only available monthly at $9.90

 * Pro plan : customers have no watch time limits and are able to download videos for offline viewing. Pro plans start at $19.90 a month or $199 for an annual subscription.

 * Trial : Customers can sign up to an initial 7 day free trial will automatically continue with the pro monthly subscription plan unless they cancel, downgrade to basic or upgrade to an annual pro plan at any point during the trial.

 * cancel Foodie-Fi service - customer will have a churn plan record with a null price but their plan will continue until the end of the billing period.

2. `subscriptions`

 * Customer subscriptions show the exact date where their specific plan_id starts.

 * If customers **downgrade** from a pro plan or **cancel** their subscription - the higher plan will remain in place until the period is over - the start_date in the subscriptions table will reflect the date that the actual plan changes.

 * When customers **upgrade** their account from a basic plan to a pro or annual pro plan - the higher plan will take effect straightaway.

 * When customers **churn** - they will keep their access until the end of their current billing period but the start_date will be technically the day they decided to cancel their service


```sql
%%sql
SELECT *
FROM plans
```

     * postgresql://postgres:***@localhost/foodie
    5 rows affected.
    




<table>
    <tr>
        <th>plan_id</th>
        <th>plan_name</th>
        <th>price</th>
    </tr>
    <tr>
        <td>0</td>
        <td>trial</td>
        <td>0.00</td>
    </tr>
    <tr>
        <td>1</td>
        <td>basic monthly</td>
        <td>9.90</td>
    </tr>
    <tr>
        <td>2</td>
        <td>pro monthly</td>
        <td>19.90</td>
    </tr>
    <tr>
        <td>3</td>
        <td>pro annual</td>
        <td>199.00</td>
    </tr>
    <tr>
        <td>4</td>
        <td>churn</td>
        <td>None</td>
    </tr>
</table>




```sql
%%sql
SELECT *
FROM subscriptions
LIMIT 5;
```

     * postgresql://postgres:***@localhost/foodie
    5 rows affected.
    




<table>
    <tr>
        <th>customer_id</th>
        <th>plan_id</th>
        <th>start_date</th>
    </tr>
    <tr>
        <td>1</td>
        <td>0</td>
        <td>2020-08-01</td>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>2020-08-08</td>
    </tr>
    <tr>
        <td>2</td>
        <td>0</td>
        <td>2020-09-20</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>2020-09-27</td>
    </tr>
    <tr>
        <td>3</td>
        <td>0</td>
        <td>2020-01-13</td>
    </tr>
</table>



#### Checking Datatypes for both tables


```sql
%%sql
SELECT column_name,data_type
FROM information_schema.columns
WHERE table_schema = 'public'  AND table_name = 'plans'
```

     * postgresql://postgres:***@localhost/foodie
    3 rows affected.
    




<table>
    <tr>
        <th>column_name</th>
        <th>data_type</th>
    </tr>
    <tr>
        <td>plan_id</td>
        <td>integer</td>
    </tr>
    <tr>
        <td>plan_name</td>
        <td>character varying</td>
    </tr>
    <tr>
        <td>price</td>
        <td>numeric</td>
    </tr>
</table>




```sql
%%sql
SELECT column_name,data_type
FROM information_schema.columns
WHERE table_schema = 'public'  AND table_name = 'subscriptions'
```

     * postgresql://postgres:***@localhost/foodie
    3 rows affected.
    




<table>
    <tr>
        <th>column_name</th>
        <th>data_type</th>
    </tr>
    <tr>
        <td>customer_id</td>
        <td>integer</td>
    </tr>
    <tr>
        <td>plan_id</td>
        <td>integer</td>
    </tr>
    <tr>
        <td>start_date</td>
        <td>date</td>
    </tr>
</table>



<a id='diagram'></a>
### Entity Relationship Diagram

<img src ='images/erd3.png'>

<a id='question'></a>
### Case Study questions

This case study is split into an initial data understanding question before diving straight into data analysis questions before finishing with 1 single extension challenge.

1. <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/case_study_3_part_2.md'>Customer Journey</a>
2. <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/case_study_3_part_2.md#analysis'>Data analysis questions</a>

#### Back to : [Top](#top) ▲

#### Next : [Case Study 3 Part 2 ](#https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/case_study_3_part_2.md) 


#### Back to : <a href='https://github.com/sumedhadewan/8-week-sql-challenge/blob/main/case-study-3/'>This Project Table of Content</a>


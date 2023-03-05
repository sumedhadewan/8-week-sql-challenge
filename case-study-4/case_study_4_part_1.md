### Case Study # 4 Part 1
## Data Bank

source : <a href='https://8weeksqlchallenge.com/case-study-4/'> 8 Week SQL challenges </a>
<img src='images/databank.png' style='width:450px'>

<a id='table-of-content'></a>

### Table of Content
* [Introduction](#introduction)
* [Creating table inside database](#table)
* [Datasets](#datasets)
* [Entity Relationship Diagram](#diagram)
* Case Study Questions

    1. Customer Node Exploration
    2. Customer Transactions
    3. Data Allocation Challenge
    4. Extra challenge

<a id='introduction'></a>

### Introduction

There is a new innovation in the financial industry called Neo-Banks: new aged digital only banks without physical branches.

Danny thought that there should be some sort of intersection between these new age banks, cryptocurrency and the data world…so he decides to launch a new initiative - Data Bank!

Data Bank runs just like any other digital bank - but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts. There are a few interesting caveats that go with this business model, and this is where the Data Bank team need your help!

The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

This case study is all about calculating metrics, growth and helping the business analyse their data in a smart way to better forecast and plan for their future developments!


<a id = 'table'></a>

### Creating tables inside `databank` database


```python
%load_ext sql
```


```python
%sql postgresql://postgres:dewan@localhost/databank
```

There is no tables in this database at the moment.


```sql
%%sql
SELECT *
FROM information_schema.tables
WHERE table_schema = 'public'
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    0 rows affected.





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
</table>



### Table 1 : `regions`


```sql
%%sql
DROP TABLE IF EXISTS regions;
CREATE TABLE regions (
    region_id INTEGER,
    region_name VARCHAR(9)
)
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    Done.
    Done.





    []




```sql
%%sql
INSERT INTO regions
    (region_id, region_name)
VALUES
    ('1','Australia'),
    ('2','America'),
    ('3','Africa'),
    ('4','Asia'),
    ('5','Europe');
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    5 rows affected.





    []



### Table 2 : `customer_nodes`


```sql
%%sql
DROP TABLE IF EXISTS customer_nodes;
CREATE TABLE customer_nodes(
    customer_id INTEGER,
    region_id INTEGER,
    node_id INTEGER,
    start_date DATE,
    end_date DATE
);
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    Done.
    Done.





    []



Inserting 3500 rows into `customer_nodes` using pgAdmin


```sql
%%sql
SELECT COUNT(*)
FROM customer_nodes

```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    1 rows affected.





<table>
    <tr>
        <th>count</th>
    </tr>
    <tr>
        <td>3500</td>
    </tr>
</table>



### Table 3 : `customer_transactions`


```sql
%%sql
DROP TABLE IF EXISTS customer_transactions;
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    Done.





    []




```sql
%%sql
DROP TABLE IF EXISTS customer_transactions;
CREATE TABLE customer_transactions(
    customer_id INTEGER,
    txn_date DATE,
    txn_type VARCHAR(10),
    txn_amount INTEGER
);
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    Done.
    Done.





    []



Inserted 5868 rows into `customer_transactions` table using pgAdmin


```sql
%%sql
SELECT COUNT(*)
FROM customer_transactions
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    1 rows affected.





<table>
    <tr>
        <th>count</th>
    </tr>
    <tr>
        <td>5868</td>
    </tr>
</table>



<a id='datasets'></a>

## Datasets

We created three tables `regions` , `customer_nodes` , `customer_transactions` inside `databank` database


```sql
%%sql
SELECT *
FROM information_schema.tables
WHERE table_schema ='public'
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    3 rows affected.





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
        <td>databank</td>
        <td>public</td>
        <td>regions</td>
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
        <td>databank</td>
        <td>public</td>
        <td>customer_nodes</td>
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
        <td>databank</td>
        <td>public</td>
        <td>customer_transactions</td>
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



**`regions`**

Just like popular cryptocurrency platforms - Data Bank is also run off a network of nodes where both money and data is stored across the globe. In a traditional banking sense - you can think of these nodes as bank branches or stores that exist around the world.

This regions table contains the region_id and their respective region_name values


```sql
%%sql
SELECT *
FROM regions
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    5 rows affected.





<table>
    <tr>
        <th>region_id</th>
        <th>region_name</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Australia</td>
    </tr>
    <tr>
        <td>2</td>
        <td>America</td>
    </tr>
    <tr>
        <td>3</td>
        <td>Africa</td>
    </tr>
    <tr>
        <td>4</td>
        <td>Asia</td>
    </tr>
    <tr>
        <td>5</td>
        <td>Europe</td>
    </tr>
</table>



**`customer_nodes`**

Customers are randomly distributed across the nodes according to their region - this also specifies exactly which node contains both their cash and data.

This random distribution changes frequently to reduce the risk of hackers getting into Data Bank’s system and stealing customer’s money and data!


```sql
%%sql
SELECT *
FROM customer_nodes
LIMIT 5
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    5 rows affected.





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
    <tr>
        <td>4</td>
        <td>5</td>
        <td>4</td>
        <td>2020-01-07</td>
        <td>2020-01-19</td>
    </tr>
    <tr>
        <td>5</td>
        <td>3</td>
        <td>3</td>
        <td>2020-01-15</td>
        <td>2020-01-23</td>
    </tr>
</table>



**`customer_transactions`**

This table stores all customer deposits, withdrawals and purchases made using their Data Bank debit card.



```sql
%%sql
SELECT *
FROM customer_transactions
LIMIT 5
```

     * postgresql://postgres:***@localhost/databank
       postgresql://postgres:***@localhost/foodie
    5 rows affected.





<table>
    <tr>
        <th>customer_id</th>
        <th>txn_date</th>
        <th>txn_type</th>
        <th>txn_amount</th>
    </tr>
    <tr>
        <td>429</td>
        <td>2020-01-21</td>
        <td>deposit</td>
        <td>82</td>
    </tr>
    <tr>
        <td>155</td>
        <td>2020-01-10</td>
        <td>deposit</td>
        <td>712</td>
    </tr>
    <tr>
        <td>398</td>
        <td>2020-01-01</td>
        <td>deposit</td>
        <td>196</td>
    </tr>
    <tr>
        <td>255</td>
        <td>2020-01-14</td>
        <td>deposit</td>
        <td>563</td>
    </tr>
    <tr>
        <td>185</td>
        <td>2020-01-29</td>
        <td>deposit</td>
        <td>626</td>
    </tr>
</table>



<a id='diagram'></a>

## Entity Relationship Diagram

<img src='images/erd4.png'>

[Top](#table-of-content) ▲

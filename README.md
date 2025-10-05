# Snowflake-SQL-Banking-Insights
Insights into banking details using Snowflake

``` SQL
## Show all active customers

Select count(*) as total_active_customers
FROM customer
where status = 'Active';

```
| TOTAL_ACTIVE_CUSTOMERS |
|---|
| 25 |

``` SQL
## customers from branch = Auckland

Select Name 
From CUSTOMER as c
join branch as b on c.branch_id = b.branch_id
where city = 'Auckland';
```
| Name |
|---|
| Terrence Gregory |
| Steven Banks |
| Mark Harris |
| Kristen Gibson |
| Andrew Foster |
| Brandon Wilkerson |
| Melissa Knight |
| Richard Parker |
| Dr. Donna Riddle |
| Dean Black |
| Anthony Boyd |
| Olivia Anderson |
| Daniel Mccarthy |
| Travis Case |

``` SQL
#All transactions greater than 50000

select c.name, SUM(t.AMOUNT) as total_transaction 
FROM CUSTOMER as c 
join ACCOUNT as a on c.customer_id = a.customer_id
join TRANSACTION as t on a.account_id = t.ACCOUNT_ID
Group by c.name
HAVING ABS(total_transaction) > 50000
Order by total_transaction DESC;
```
| NAME | TOTAL_TRANSACTION |
|---|---|
| Julian Jackson | 53926.00 |
| Brandi Li | 52908.20 |
| Tyler Evans | -53650.76 |
| Peter Moore | -60334.99 |
| James Morrow | -61303.24 |
| Charles Mckay | -81166.72 |

``` SQL
# top 10 withdrawals by largest amount

select c.name, SUM(t.AMOUNT) as total_transaction 
FROM CUSTOMER as c 
join ACCOUNT as a on c.customer_id = a.customer_id
join TRANSACTION as t on a.account_id = t.ACCOUNT_ID
Where t.transaction_type = 'Debit'
group by c.name
order by ABS(SUM(t.amount)) DESC
limit 10;
```
| NAME | TOTAL_TRANSACTION |
|---|---|
| Steven Banks | -414970.31 |
| Julian Jackson | -256544.03 |
| James Morrow | -247741.93 |
| Brandon Wilkerson | -246139.81 |
| Andrew Foster | -238317.35 |
| Kirsten Turner | -205018.84 |
| Charles Mckay | -199306.25 |
| Erica Small | -192266.61 |
| Brandi Li | -191289.47 |
| Diana Leon | -185959.83 |

``` SQL 
#total deposits by branch

Select b.branch_name, SUM(t.AMOUNT) as total_deposits
From branch as b 
join CUSTOMER as c on b.branch_id = c.branch_id
join account as a on c.customer_id = a.customer_id
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
group by b.branch_name
order by total_deposits DESC;
```
| BRANCH_NAME | TOTAL_DEPOSITS |
|---|---|
| Auckland CBD | 1770492.41 |
| Wellington Central | 1361483.55 |
| Christchurch South | 1304433.70 |
| Hamilton Central | 911250.11 |

``` SQL
#average balance by account type

select account_type, ROUND(AVG(BALANCE), 2) as Avg_bal
from Account
Group by account_type
order by Avg_bal DESC;
```
| ACCOUNT_TYPE | AVG_BAL |
|---|---|
| Checking | 54770.86 |
| Savings | 44122.52 |

``` SQL
#get min and max balance 

select account_type, min(BALANCE) as min_balance, max(balance) as max_balance
FROM ACCOUNT 
Group by account_type;
```
| ACCOUNT_TYPE | MIN_BALANCE | MAX_BALANCE |
|---|---|---|
| Savings | 1995.49 | 97293.81 |
| Checking | 629.66 | 97656.84 |

``` SQL
#count active vs inactive customers

select Status, COUNT(*) as Current_stat
FROM CUSTOMER 
Group by STATUS
Order by Status;
```
| STATUS | CURRENT_STAT |
|---|---|
| Active | 25 |
| Inactive | 25 |

```SQL
#Active vs Inactive customers in each branch

SELECT Branch_name,
    sum(Case when c.status = 'Active' THEN 1 ELSE 0 End) as Active,
    SUM(Case When c.status = 'Inactive' Then 1 Else 0 End) As inactive
FROM CUSTOMER as c 
Join BRANCH as b 
ON c.branch_id = b.branch_id
Group by b.branch_name;
```
| BRANCH_NAME | ACTIVE | INACTIVE |
|---|---|---|
| Auckland CBD | 4 | 10 |
| Christchurch South | 9 | 2 |
| Hamilton Central | 4 | 6 |
| Wellington Central | 8 | 7 |
 
``` SQL
#list of customers alonh with their account types and balance

SELECT c.name, a.account_type, Sum(a.balance) as balance
from customer as c 
join account as a on c.customer_id = a.CUSTOMER_ID
group by c.name, a.ACCOUNT_TYPE
Order by c.name, a.account_type, balance;

```
| NAME | ACCOUNT_TYPE | BALANCE |
|---|---|---|
| Alexandra Esparza | Checking | 112508.32 |
| Andrew Foster | Checking | 167426.04 |
| Andrew Foster | Savings | 203565.48 |
| Anna Jenkins | Checking | 192746.44 |
| Anne Johnson | Checking | 106479.45 |
| Anthony Boyd | Checking | 93528.97 |
| Anthony Boyd | Savings | 34458.31 |
| Anthony Rice | Savings | 11273.94 |
| Brandi Li | Checking | 57936.89 |
| Brandi Li | Savings | 132959.75 |
| Brandon Wilkerson | Checking | 33020.19 |
| Brandon Wilkerson | Savings | 128131.04 |
| Carla Stewart | Savings | 101600.86 |
| Charles Mckay | Checking | 148865.13 |
| Courtney Parker | Checking | 629.66 |
| Cynthia Conner | Checking | 63243.38 |
| Cynthia Conner | Savings | 43425.57 |
| Daniel Mccarthy | Checking | 143580.93 |
| David Choi | Checking | 62291.64 |
| Dean Black | Savings | 100353.50 |
| Deborah Myers | Checking | 90048.55 |
| Diana Leon | Checking | 48688.69 |
| Diana Leon | Savings | 96474.45 |
| Dr. Donna Riddle | Savings | 2374.15 |
| Erica Small | Checking | 88872.86 |
| Erica Small | Savings | 85268.07 |
| Haley Nelson | Savings | 78607.74 |
| James Morrow | Checking | 115220.66 |
| James Morrow | Savings | 116811.14 |
| Janice Poole | Savings | 57749.10 |
| Joseph Garcia | Checking | 59074.66 |
| Julian Jackson | Checking | 103993.97 |
| Julian Jackson | Savings | 64866.43 |
| Karen Dunn | Checking | 132977.93 |
| Karen Dunn | Savings | 40008.73 |
| Katie Bowman | Checking | 50192.40 |
| Kirsten Turner | Checking | 10614.58 |
| Kirsten Turner | Savings | 72268.36 |
| Kristen Gibson | Checking | 78719.40 |
| Matthew Schroeder | Savings | 61236.04 |
| Melissa Knight | Checking | 85499.43 |
| Melissa Knight | Savings | 89354.82 |
| Michelle Fitzgerald | Checking | 142607.82 |
| Michelle Fitzgerald | Savings | 53992.73 |
| Miss Alexandra Bailey | Checking | 56634.31 |
| Olivia Anderson | Checking | 81897.33 |
| Peter Moore | Checking | 127947.49 |
| Peter Moore | Savings | 68343.00 |
| Rebecca Franco | Savings | 97651.73 |
| Richard Parker | Checking | 57285.16 |
| Rose Hughes | Checking | 56052.84 |
| Sherry Williams | Checking | 39689.29 |
| Sherry Williams | Savings | 77242.96 |
| Sonia Hartman | Savings | 8261.36 |
| Stephanie Camacho | Checking | 101998.42 |
| Stephanie Camacho | Savings | 4551.88 |
| Steven Banks | Checking | 136980.46 |
| Steven Banks | Savings | 174039.90 |
| Terrence Gregory | Savings | 65673.55 |
| Tyler Evans | Checking | 55602.49 |
| Tyler Evans | Savings | 3213.76 |

``` SQL 
#list of total deposits per customer

Select c.name, Sum(t.amount) as total_deposits
from customer as c 
join account as a on c.customer_id = a.customer_id
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
Group by c.name
order by total_deposits DESC;
```
| NAME | TOTAL_DEPOSITS |
|---|---|
| Steven Banks | 453411.28 |
| Julian Jackson | 310470.03 |
| Brandi Li | 244197.67 |
| Brandon Wilkerson | 239040.99 |
| Andrew Foster | 237300.92 |
| Karen Dunn | 218440.72 |
| James Morrow | 186438.69 |
| Erica Small | 183988.77 |
| Kirsten Turner | 179880.78 |
| Diana Leon | 168287.28 |
| Katie Bowman | 167121.28 |
| Michelle Fitzgerald | 145733.49 |
| Anthony Boyd | 140050.61 |
| Kristen Gibson | 130894.66 |
| Alexandra Esparza | 123018.48 |
| Anna Jenkins | 121681.25 |
| Sherry Williams | 121469.65 |
| Charles Mckay | 118139.53 |
| Anne Johnson | 115742.79 |
| Daniel Mccarthy | 115214.29 |
| Carla Stewart | 114679.52 |
| Peter Moore | 112526.14 |
| Dean Black | 111121.80 |
| Stephanie Camacho | 101706.32 |
| Cynthia Conner | 94467.33 |
| Terrence Gregory | 93132.48 |
| Richard Parker | 86760.89 |
| Anthony Rice | 86709.62 |
| Courtney Parker | 75610.66 |
| Dr. Donna Riddle | 75078.53 |
| Deborah Myers | 71145.60 |
| Miss Alexandra Bailey | 71106.59 |
| Janice Poole | 67853.68 |
| Joseph Garcia | 67219.50 |
| David Choi | 64025.55 |
| Matthew Schroeder | 62843.78 |
| Melissa Knight | 62254.96 |
| Rebecca Franco | 55659.03 |
| Tyler Evans | 48148.23 |
| Rose Hughes | 33081.87 |
| Olivia Anderson | 26231.00 |
| Haley Nelson | 23099.17 |
| Sonia Hartman | 22674.36 |

``` SQL 
##Avg deposite per account

SELECT a.account_id, Round(AVG(t.amount), 2) as Avg_deposite
from account as a
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
group by a.account_id
order by a.Account_id ASC;
```
| ACCOUNT_ID | AVG_DEPOSIT |
|---|---|
| 201 | 2674.65 |
| 202 | 2412.82 |
| 203 | 2551.64 |
| 204 | 2283.28 |
| 205 | 2570.92 |
| 206 | 1780.26 |
| 207 | 2270.49 |
| 208 | 2669.37 |
| 209 | 2585.68 |
| 210 | 2527.37 |
| 211 | 2991.75 |
| 212 | 2341.99 |
| 213 | 2047.02 |
| 214 | 2185.92 |
| 215 | 1985.76 |
| 216 | 2550.48 |
| 217 | 2292.94 |
| 218 | 2260.46 |
| 219 | 2801.73 |
| 220 | 2386.01 |
| 221 | 2300.05 |
| 222 | 2448.70 |
| 223 | 2377.33 |
| 224 | 2585.25 |
| 225 | 3010.64 |
| 226 | 2309.82 |
| 227 | 2795.73 |
| 228 | 2845.37 |
| 229 | 2146.76 |
| 230 | 1853.16 |
| 231 | 2210.17 |
| 232 | 2417.07 |
| 233 | 1989.70 |
| 234 | 2360.33 |
| 235 | 2324.84 |
| 236 | 1924.72 |
| 237 | 2666.39 |
| 238 | 3005.95 |
| 239 | 2118.50 |
| 240 | 2050.90 |
| 241 | 2494.35 |
| 242 | 2765.80 |
| 243 | 2827.27 |
| 244 | 2322.94 |
| 245 | 2922.59 |
| 246 | 2583.55 |
| 247 | 2488.36 |
| 248 | 2774.64 |
| 249 | 2170.53 |
| 250 | 2207.78 |


``` SQL
#active vs inactive customers

select b.branch_name,
 SUM(case when c.status = 'Active' THEN 1 else 0 END) as Active_Cx, 
 SUM(case when c.status = 'Inactive' THEN 1 else 0 END) as Inactive_Cx
from customer as c
join branch as b on c.branch_id = b.branch_id
group by b.branch_name;
```

| BRANCH_NAME | ACTIVE_CX | INACTIVE_CX |
|---|---|---|
| Auckland CBD | 4 | 10 |
| Christchurch South | 9 | 2 |
| Hamilton Central | 4 | 6 |
| Wellington Central | 8 | 7 |

``` SQL 
#total deposits per customer

select c.name, Round(SUM(t.amount), 2) as total_depo
from Customer as c
join account as a on c.customer_id = a.customer_id
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
group by c.name
order by total_depo DESC;

```

| NAME | TOTAL_DEPOSIT |
|---|---|
| Steven Banks | 453411.28 |
| Julian Jackson | 310470.03 |
| Brandi Li | 244197.67 |
| Brandon Wilkerson | 239040.99 |
| Andrew Foster | 237300.92 |
| Karen Dunn | 218440.72 |
| James Morrow | 186438.69 |
| Erica Small | 183988.77 |
| Kirsten Turner | 179880.78 |
| Diana Leon | 168287.28 |
| Katie Bowman | 167121.28 |
| Michelle Fitzgerald | 145733.49 |
| Anthony Boyd | 140050.61 |
| Kristen Gibson | 130894.66 |
| Alexandra Esparza | 123018.48 |
| Anna Jenkins | 121681.25 |
| Sherry Williams | 121469.65 |
| Charles Mckay | 118139.53 |
| Anne Johnson | 115742.79 |
| Daniel Mccarthy | 115214.29 |
| Carla Stewart | 114679.52 |
| Peter Moore | 112526.14 |
| Dean Black | 111121.80 |
| Stephanie Camacho | 101706.32 |
| Cynthia Conner | 94467.33 |
| Terrence Gregory | 93132.48 |
| Richard Parker | 86760.89 |
| Anthony Rice | 86709.62 |
| Courtney Parker | 75610.66 |
| Dr. Donna Riddle | 75078.53 |
| Deborah Myers | 71145.60 |
| Miss Alexandra Bailey | 71106.59 |
| Janice Poole | 67853.68 |
| Joseph Garcia | 67219.50 |
| David Choi | 64025.55 |
| Matthew Schroeder | 62843.78 |
| Melissa Knight | 62254.96 |
| Rebecca Franco | 55659.03 |
| Tyler Evans | 48148.23 |
| Rose Hughes | 33081.87 |
| Olivia Anderson | 26231.00 |
| Haley Nelson | 23099.17 |
| Sonia Hartman | 22674.36 |


################CASE WHEN###########################
``` SQL 
#categorize transactions

select amount, 
  CASE 
    when amount > 5000 Then 'high'
    when amount between 1000 and 5000 THEN 'medium'
    when amount < 1000 THEN 'low'
    END as transaction_category
from transaction
```
| AMOUNT | TRANSACTION_CATEGORY |
|---|---|
| -1841.64 | low |
| 4503.63 | medium |
| -4330.12 | low |
| -3600.52 | low |
| -4659.21 | low |
| -333.11 | low |
| 4008.78 | medium |
| -3586.34 | low |
| 2141.82 | medium |
| -1330.13 | low |
| 3821.91 | medium |
| -597.16 | low |
| -1234.76 | low |
| 2807.28 | medium |
| 1945.90 | medium |
| 681.73 | low |
| 2361.13 | medium |
| -4201.60 | low |
| -1147.67 | low |
| 1201.60 | medium |
| -1680.05 | low |
| -1221.93 | low |
| 4988.09 | medium |
| 2420.93 | medium |

``` SQL 
#customer value segmentation

select balance,
    Case 
        when balance > 50000 THEN 'Platinum'
        when balance between 1000 AND 50000 Then 'Gold'
        when balance < 1000 Then 'Silver'
    END as account_tier
from Account
```
| BALANCE | ACCOUNT_TIER |
|---|---|
| 73601.30 | Platinum |
| 41907.45 | Gold |
| 57936.89 | Platinum |
| 93337.48 | Platinum |
| 96694.90 | Platinum |
| 10764.08 | Gold |
| 57206.18 | Platinum |
| 40008.73 | Gold |
| 71803.34 | Platinum |
| 4551.88 | Gold |
| 57285.16 | Platinum |
| 33020.19 | Gold |
| 3213.76 | Gold |
| 81897.33 | Platinum |
| 55602.49 | Platinum |
| 18579.64 | Gold |
| 48901.44 | Gold |
| 3658.60 | Gold |
| 88872.86 | Platinum |
| 4307.05 | Gold |
| 97293.81 | Platinum |
| 34929.73 | Gold |
| 63243.38 | Platinum |
| 70821.69 | Platinum |
| 64837.68 | Platinum |
| 86145.02 | Platinum |
| 57803.23 | Platinum |
| 34458.31 | Gold |


``` SQL
# salaries above average

##Return all customers and their account balances, including customers who don’t have an account yet.

select c.name, a.account_id, a.balance
from customer as c 
left join account as a on c.customer_id = a.customer_id
```

| NAME | ACCOUNT_ID | BALANCE |
|---|---|---|
| Andrew Foster | 201 | 73601.30 |
| Kirsten Turner | 202 | 41907.45 |
| Brandi Li | 203 | 57936.89 |
| Anna Jenkins | 204 | 93337.48 |
| Dean Black | 205 | 96694.90 |
| Rebecca Franco | 206 | 10764.08 |
| Julian Jackson | 207 | 57206.18 |
| Karen Dunn | 208 | 40008.73 |
| Steven Banks | 209 | 71803.34 |
| Stephanie Camacho | 210 | 4551.88 |
| Richard Parker | 211 | 57285.16 |
| Brandon Wilkerson | 212 | 33020.19 |
| Tyler Evans | 213 | 3213.76 |
| Olivia Anderson | 214 | 81897.33 |
| Tyler Evans | 215 | 55602.49 |
| Diana Leon | 216 | 18579.64 |
| Andrew Foster | 217 | 48901.44 |
| Dean Black | 218 | 3658.60 |
| Erica Small | 219 | 88872.86 |
| Carla Stewart | 220 | 4307.05 |
| Carla Stewart | 221 | 97293.81 |
| Steven Banks | 222 | 34929.73 |
| Cynthia Conner | 223 | 63243.38 |
| Michelle Fitzgerald | 224 | 70821.69 |
| Stephanie Camacho | 225 | 64837.68 |
| Brandi Li | 226 | 86145.02 |
| Charles Mckay | 227 | 57803.23 |
| Anthony Boyd | 228 | 34458.31 |
| Rebecca Franco | 229 | 86887.65 |
| Charles Mckay | 230 | 91061.90 |
| Brandon Wilkerson | 231 | 5472.18 |
| Matthew Schroeder | 232 | 61236.04 |
| Peter Moore | 233 | 97656.84 |
| Michelle Fitzgerald | 234 | 71786.13 |


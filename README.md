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

#get min and max balance 

select account_type, min(BALANCE) as min_balance, max(balance) as max_balance
FROM ACCOUNT 
Group by account_type;

#count active vs inactive customers

select Status, COUNT(*) as Current_stat
FROM CUSTOMER 
Group by STATUS
Order by Status;

#Active vs Inactive customers in each branch

SELECT Branch_name,
    sum(Case when c.status = 'Active' THEN 1 ELSE 0 End) as Active,
    SUM(Case When c.status = 'Inactive' Then 1 Else 0 End) As inactive
FROM CUSTOMER as c 
Join BRANCH as b 
ON c.branch_id = b.branch_id
Group by b.branch_name;

#find all accounts with negative balances

Select account_id, Sum(balance)
from account
group by account_id 
having sum(balance) < 0; 

#list of customers alonh with their account types and balance

SELECT c.name, a.account_type, Sum(a.balance) as balance
from customer as c 
join account as a on c.customer_id = a.CUSTOMER_ID
group by c.name, a.ACCOUNT_TYPE
Order by c.name, a.account_type, balance;

#list of total deposits per customer

Select c.name, Sum(t.amount) as total_deposits
from customer as c 
join account as a on c.customer_id = a.customer_id
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
Group by c.name
order by total_deposits DESC;


##Avg deposite per account

SELECT a.account_id, Round(AVG(t.amount), 2) as Avg_deposite
from account as a
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
group by a.account_id
order by a.Account_id ASC;

#active vs inactive customers

select b.branch_name,
 SUM(case when c.status = 'Active' THEN 1 else 0 END) as Active_Cx, 
 SUM(case when c.status = 'Inactive' THEN 1 else 0 END) as Inactive_Cx
from customer as c
join branch as b on c.branch_id = b.branch_id
group by b.branch_name;

#total deposits per customer

select c.name, Round(SUM(t.amount), 2) as total_depo
from Customer as c
join account as a on c.customer_id = a.customer_id
join transaction as t on a.account_id = t.account_id
where t.transaction_type = 'Credit'
group by c.name
order by total_depo DESC; 


#Customer with no transactions

Select c.name
from CUSTOMER as c 
left join account as a on c.customer_id = a.customer_id
left join transaction as t on a.account_id = t.ACCOUNT_ID
where t.transaction_id is null
Group by c.name
order by c.name ASC; 

################CASE WHEN###########################

#categorize transactions

select amount, 
  CASE 
    when amount > 5000 Then 'high'
    when amount between 1000 and 5000 THEN 'medium'
    when amount < 1000 THEN 'low'
    END as transaction_category
from transaction


#customer value segmentation

select balance,
    Case 
        when balance > 50000 THEN 'Platinum'
        when balance between 1000 AND 50000 Then 'Gold'
        when balance < 1000 Then 'Silver'
    END as account_tier
from Account 

###############Sub-Queries###################

# salaries above average

##Return all customers and their account balances, including customers who don’t have an account yet.

select c.name, a.account_id, a.balance
from customer as c 
left join account as a on c.customer_id = a.customer_id


###list all customers who do not have checking account 

select c.name
from customer as c 
where customer_id not in (select customer_id from account where account_type = 'Checking' );


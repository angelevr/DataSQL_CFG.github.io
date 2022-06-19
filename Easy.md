Table: World

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | int     |
+-------------+---------+
name is the primary key column for this table.
Each row of this table gives information about the name of a country, the continent to which it belongs, its area, the population, and its GDP value.
 

A country is big if:

it has an area of at least three million (i.e., 3000000 km2), or
it has a population of at least twenty-five million (i.e., 25000000).
Write an SQL query to report the name, population, and area of the big countries.

Return the result table in any order.

#### Solution
SELECT
    name,
    population,
    area
FROM
    world
WHERE area >= 3000000 OR population >= 250000000

----------------------------------------------------------------------------------------------

Table: Products

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| low_fats    | enum    |
| recyclable  | enum    |
+-------------+---------+
product_id is the primary key for this table.
low_fats is an ENUM of type ('Y', 'N') where 'Y' means this product is low fat and 'N' means it is not.
recyclable is an ENUM of types ('Y', 'N') where 'Y' means this product is recyclable and 'N' means it is not.
 

Write an SQL query to find the ids of products that are both low fat and recyclable.

Return the result table in any order.

#### Solution
SELECT
    product_id
FROM
    Products
WHERE low_fats = "Y" AND recyclable = "Y"

----------------------------------------------------------------------------------------------
Table: Customer

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| referee_id  | int     |
+-------------+---------+
id is the primary key column for this table.
Each row of this table indicates the id of a customer, their name, and the id of the customer who referred them.
 

Write an SQL query to report the names of the customer that are not referred by the customer with id = 2.

Return the result table in any order.

#### Solution
SELECT
    name
FROM
    Customer
WHERE referee_id != 2 OR referee_id IS NULL;

--------------------------------------------------------------------------------------------

Table: Customers

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
id is the primary key column for this table.
Each row of this table indicates the ID and name of a customer.
 

Table: Orders

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| customerId  | int  |
+-------------+------+
id is the primary key column for this table.
customerId is a foreign key of the ID from the Customers table.
Each row of this table indicates the ID of an order and the ID of the customer who ordered it.
 

Write an SQL query to report all customers who never order anything.

Return the result table in any order.

#### Solution
SELECT
    name
AS
    Customers
FROM
    Customers
LEFT JOIN
    Orders
ON
    Customers.id = Orders.customerId
WHERE
    Orders.id IS NULL

--------------------------------------------------------------------------------------------
Table: Person

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
id is the primary key column for this table.
Each row of this table contains an email. The emails will not contain uppercase letters.
 

Write an SQL query to delete all the duplicate emails, keeping only one unique email with the smallest id. Note that you are supposed to write a DELETE statement and not a SELECT one.

After running your script, the answer shown is the Person table. The driver will first compile and run your piece of code and then show the Person table. The final order of the Person table does not matter.

#### Solution
delete p2  -- we are deleting p2, cause p1 table match with 1,john and p2 match with 3,john
           -- hence, we need to delete higher id, according to question
from person as p1
inner join person as p2
where p1.id < p2.id and p1.email = p2.email
    
---------------------------------------------------------------------------------------------
Table: Salary

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| name        | varchar  |
| sex         | ENUM     |
| salary      | int      |
+-------------+----------+
id is the primary key for this table.
The sex column is ENUM value of type ('m', 'f').
The table contains information about an employee.
 

Write an SQL query to swap all 'f' and 'm' values (i.e., change all 'f' values to 'm' and vice versa) with a single update statement and no intermediate temporary tables.

Note that you must write a single update statement, do not write any select statement for this problem.

#### Solution
UPDATE Salary
SET sex =
CASE
WHEN sex = 'm' THEN 'f'
WHEN sex = 'f' THEN 'm'
END

---------------------------------------------------------------------------------------------
Table: Employees

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
| salary      | int     |
+-------------+---------+
employee_id is the primary key for this table.
Each row of this table indicates the employee ID, employee name, and salary.
 

Write an SQL query to calculate the bonus of each employee. The bonus of an employee is 100% of their salary if the ID of the employee is an odd number and the employee name does not start with the character 'M'. The bonus of an employee is 0 otherwise.

Return the result table ordered by employee_id.

#### Solution
SELECT
    employee_id,
IF
    (employee_id % 2!=0 AND name NOT LIKE 'M%', salary, 0) AS bonus
FROM
    Employees
ORDER BY  
    employee_id
 
------------------------------------------------------------------------------------------
Table: Users

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| name           | varchar |
+----------------+---------+
user_id is the primary key for this table.
This table contains the ID and the name of the user. The name consists of only lowercase and uppercase characters.
 

Write an SQL query to fix the names so that only the first character is uppercase and the rest are lowercase.

Return the result table ordered by user_id.

#### Solution
- Firstly we need to fetch  the first letter and conert it to upperCase
- Secondly we need to fetch the remaining string to lowerCase and then concat them
Concat() function adds two or more strings together. it takes a variable number of string arguments and concatenates the into a single string.
- We will be using four functions here that are present in SQL
 - UPPER(A) where A is string
 - LOWER(A) where A is string
 - SUBSTR(A,index,length) where A is string index (1 index instead of 0 index) and optional
  - So to get first letter we can use SUBSTR(name,1,1)
  - To get the remaining string we can use SUBSTR(name,2) // length is not required here
 - CONCAT(A,B) where we concat two strings A+B

Now with the above information we can create our query as following:
SELECT Users.users_id, CONCAT(UPPER(SUBSTR(Users.name,1,1)), LOWER(SUBSTR(Users.name,2)))
AS name
FROM Users
ORDER BY Users.user_id ASC

--------------------------------------------------------------------------------------------
Table Activities:

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| sell_date   | date    |
| product     | varchar |
+-------------+---------+
There is no primary key for this table, it may contain duplicates.
Each row of this table contains the product name and the date it was sold in a market.
 

Write an SQL query to find for each date the number of different products sold and their names.

The sold products names for each date should be sorted lexicographically.

Return the result table ordered by sell_date.

#### Solution

GROUPCONCAT() is used to concatenat data from multiple rows into one field - it works better than other functions as it can use DISTINCT to filter out repeated values.
Step 1: we will count distinct products and name the column as num_sold (to avoid duplicates)
Step 2: we use group concat to get the distinct products and to display them in a column with a seperator(,) and order by products and name the column as products
Step 3: group them by sell_date

SELECT sell_date,
		COUNT(DISTINCT(product)) AS num_sold, 
		GROUP_CONCAT(DISTINCT product ORDER BY product ASC SEPARATOR ',') AS products
FROM Activities
GROUP BY sell_date
ORDER BY sell_date ASC
 
OR without the separator (groupconcat() separates them for you)

select sell_date,
count(distinct(product)) as num_sold,#counting the number of products sold that day sepretly 
group_concat(distinct (product))  as products# to group the product that sold in the same day
from activities
group by sell_date
order by sell_date

-----------------------------------------------------------------------------------------------
Table: Patients

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |
+--------------+---------+
patient_id is the primary key for this table.
'conditions' contains 0 or more code separated by spaces. 
This table contains information of the patients in the hospital.
 

Write an SQL query to report the patient_id, patient_name all conditions of patients who have Type I Diabetes. Type I Diabetes always starts with DIAB1 prefix

Return the result table in any order.

#### Solution
SELECT * FROM PATIENTS WHERE
CONDITIONS LIKE '% DIAB1%' OR
CONDITIONS LIKE 'DIAB1%';

-------------------------------------------------------------------------------------------





















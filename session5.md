-- Most popular functions: SUM, AVG, COUNT, MIN, MAX - these are all numerical functions
-- Functions can be string based, numerical based, date based (taking month or year from a date)
-- see more at https://www.w3schools.com/SQL/sql_ref_mysql.asp

USE fruits;

SELECT * 
FROM Table1_fruit_basket;


SELECT * 
FROM Table2_fruit_basket;

-- COUNT
SELECT COUNT(Fruit)
FROM Table2_fruit_basket
WHERE Fruit = 'pear';

-- CHAR_LENGTH
SELECT Fruit, CHAR_LENGTH(Fruit)
FROM Table2_fruit_basket
WHERE CHAR_LENGTH(Fruit) = 4;

-- CASE
SELECT Fruit,
	CASE
		WHEN Fruit = "PEAR" THEN "IS_PEAR"
        ELSE "NOT_PEAR"
	END
FROM Table2_fruit_basket;

USE Parts;
SELECT *
FROM Supply;

-- How many projects required more than 700 parts in total?
SELECT COUNT(*)
FROM
(SELECT SUM(QUANTITY) AS total_parts_supplied
FROM Supply
GROUP BY J_ID
HAVING total_parts_supplied > 700) projects_high_requirements;


-- alternative solution using CASE, much more complexed and not needed for this example but here to show how CASE is used
SELECT SUM(total_parts_supplied_more_than_700)
FROM
(SELECT J_ID, total_parts_supplied,
	CASE 
		WHEN total_parts_supplied > 700 THEN 1
        ELSE 0
	END total_parts_supplied_more_than_700
FROM
(SELECT J_ID, SUM(QUANTITY) AS total_parts_supplied
FROM Supply
GROUP BY J_ID) projects_requirements
)projects_requirements;

## Transactions
CREATE DATABASE bank;

USE bank;

CREATE TABLE accounts (
	customer_id INT,
	customer_name VARCHAR(50),
    money_amount INT
);

INSERT INTO accounts
VALUES 
(1, 'Jack', 50),
(2, 'Jill', 100);

SELECT *
FROM accounts;

-- Jack has given Jill 50 
-- cannot undo changes here 
UPDATE accounts
SET money_amount = money_amount - 50
WHERE customer_name = 'Jack';

UPDATE accounts
SET money_amount = money_amount + 50
WHERE customer_name = 'Jillo';

-- ability to undo changes
START TRANSACTION;

UPDATE accounts
SET money_amount = money_amount - 50
WHERE customer_name = 'Jack';

UPDATE accounts
SET money_amount = money_amount + 50
WHERE customer_name = 'Jillo';

SELECT *
FROM accounts;

ROLLBACK;

SELECT *
FROM accounts;

UPDATE accounts
SET money_amount = money_amount - 50
WHERE customer_name = 'Jack';

UPDATE accounts
SET money_amount = money_amount + 50
WHERE customer_name = 'Jill';

SELECT *
FROM accounts;

-- commit my changes
COMMIT;

-- rollback my changes
ROLLBACK;

SELECT *
FROM accounts;

-- other option
SET autocommit=0; 

## Parts, from session4 homework
USE Parts;
SELECT * FROM PART;
SELECT * FROM PROJECT;
SELECT * FROM SUPPLIER;
SELECT * FROM SUPPLY;
/*
S_ID = supplier id
J_ID = project id
P_ID = part id
*/

-- Find the name and status of each supplier who supplies project J2
SELECT s.SNAME, s.STATUS
FROM SUPPLIER s
WHERE s.S_ID IN
(
	SELECT S_ID
	FROM SUPPLY
	WHERE J_ID = 'J2'
);

-- subquery: should run on its own
SELECT S_ID
FROM SUPPLY
WHERE J_ID = 'J2'; # S2, S3, S5

-- main query 
SELECT s.SNAME, s.STATUS
FROM SUPPLIER s
WHERE s.S_ID IN ('S2', 'S3', 'S5');

-- Find the name and city of each project supplied by a London-based supplier
-- 1. Find suppliers in London 
		-- comes from SUPPLIER
-- 2. Find projects that is supplied by 1 -
		-- comes from SUPPLY 
        -- we have S_IDs from step 1
        -- we can use S_IDs to filter out J_ID that correspond to S_ID in step 1
-- 3. Find name and city of each project in 2 
		-- comes from PROJECT 
        -- we have J_IDs from step 2 
        -- we can use the J_IDS to get corresponding name, city of project

-- step 1:
SELECT S_ID
FROM SUPPLIER
WHERE CITY = 'London'; # S1, S4

-- step 2:
SELECT J_ID
FROM SUPPLY
WHERE S_ID IN (
	SELECT S_ID
	FROM SUPPLIER
	WHERE CITY = 'London'
); # J1, J3, J4, J7

-- step 3:
SELECT J_ID, JNAME, CITY
FROM PROJECT
WHERE J_ID IN 
(
SELECT J_ID
FROM SUPPLY
WHERE S_ID IN (
	SELECT S_ID
	FROM SUPPLIER
	WHERE CITY = 'London'
)
);

-- Find the name and city of each project not supplied by a London-based supplier
-- 1. Find suppliers not in London 
		-- comes from SUPPLIER
-- 2. Find projects that is supplied by 1 -
		-- comes from SUPPLY 
        -- we have S_IDs from step 1
        -- we can use S_IDs to filter out J_ID that correspond to S_ID in step 1
-- 3. Find name and city of each project in 2 
		-- comes from PROJECT 
        -- we have J_IDs from step 2 
        -- we can use the J_IDS to get corresponding name, city of project

-- step 1:
SELECT S_ID
FROM SUPPLIER
WHERE CITY != 'London'; # S2, S3, S5

-- step 2:
SELECT DISTINCT J_ID
FROM SUPPLY
WHERE S_ID IN (
	SELECT S_ID
	FROM SUPPLIER
	WHERE CITY != 'London'
); # J1, J2, J3, J4, J5, J6, J7

-- step 3:
SELECT J_ID, JNAME, CITY
FROM PROJECT
WHERE J_ID IN 
(
SELECT DISTINCT J_ID
FROM SUPPLY
WHERE S_ID IN (
	SELECT S_ID
	FROM SUPPLIER
	WHERE CITY != 'London'
)
);

/*
JOINS
1. How am I joining it? LEFT, RIGHT, INNER
2. What am I joining it ON? e.g. name, customer_id 
​
SELECT *
FROM table_a
LEFT JOIN table_b
ON table_a.name = table_b.name
*/

/*
Find the supplier name, project name and part name for each case where a 
supplier supplies a project with a part, but also the 
supplier city, project city and part city are the same.
*/

SELECT *
FROM SUPPLY
LEFT JOIN SUPPLIER
ON SUPPLY.S_ID = SUPPLIER.S_ID
LEFT JOIN PROJECT
ON SUPPLY.J_ID = PROJECT.J_ID
LEFT JOIN PART
ON SUPPLY.P_ID = PART.P_ID
WHERE (SUPPLIER.CITY = PROJECT.CITY) AND (SUPPLIER.CITY = PART.CITY)

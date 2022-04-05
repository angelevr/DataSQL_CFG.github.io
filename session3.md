CREATE DATABASE Bakery;

USE Bakery;

CREATE TABLE Sweet (
    id INT NOT NULL,
    item_name VARCHAR(50) NOT NULL,
    price FLOAT(2)
);

CREATE TABLE Savoury (
    id INT NOT NULL,
    item_name VARCHAR(50) NOT NULL,
    price FLOAT(2),
    main_ingredient VARCHAR(50)
);

-- Populate the Sweet table
INSERT INTO Sweet
(id, item_name, price)
VALUES
(1, 'doughnut', 0.50),
(2, 'croissant', 0.75),
(3, 'painauchocolat', 0.55),
(4, 'cinnamon twirl', 0.45),
(5, 'cannoli', 0.88),
(6, 'apple tart', 1.12);

INSERT INTO Savoury
(id, item_name, price, main_ingredient)
VALUES
(1, 'meat pie', 1.25, 'pork'),
(2, 'sausage roll', 1.00, null),
(3, 'pasty', 2.45, 'beef');

-- Use select * statements to check tables

SELECT * FROM savoury;
SELECT * FROM savoury 
WHERE main_ingredient = "PORK"
OR main_ingredient = "BEEF";

SELECT * FROM savoury
WHERE main_ingredient is not null;

SELECT * FROM sweet
WHERE price <= 0.50;

SELECT item_name AS desserts FROM sweet
WHERE price <=0.50;

SELECT * FROM sweet
WHERE item_name != "cannoli";

##2nd practice
SELECT * FROM sweet
WHERE item_name LIKE "C%";

SELECT * FROM sweet
WHERE item_name LIKE "C%I";

SELECT * FROM savoury
WHERE price BETWEEN 1.01 AND 2.45;

SELECT price, item_name as dessert FROM sweet
WHERE item_name LIKE "A%";

##Sort by
SELECT item_name,price FROM sweet
ORDER BY price;

SELECT item_name,price FROM sweet
ORDER BY price DESC;

#sum
SELECT SUM(price) FROM sweet;

SELECT AVG(price) FROM Sweet;

SELECT COUNT(item_name) FROM sweet;

#groupby
SELECT COUNT(item_name), item_name FROM sweet GROUP BY item_name;

##having
SELECT COUNT(item_name), item_name FROM sweet GROUP BY item_name
HAVING COUNT(item_name) <=1.5;


CREATE DATABASE shop;
use shop;

CREATE TABLE SALES1 (
    Store VARCHAR(20) NOT NULL,
    Week VARCHAR(20) NOT NULL,
    Day VARCHAR(20) NOT NULL,
    SalesPerson VARCHAR(20) NOT NULL,
    SalesAmount DECIMAL(6 , 2 ) NOT NULL,
    Month VARCHAR(20) NULL
); 

INSERT INTO  SALES1
(Store, Week, Day, SalesPerson, SalesAmount, Month) 
VALUES 
('London', '2', 'Monday', 'Frank', 56.25 , 'May'),
('London', '5', 'Tuesday', 'Frank', 74.32 , 'Sep'),
('London', '5', 'Monday', 'Bill', 98.42 , 'Sep'),
('London', '5', 'Saturday', 'Bill', 73.90 , 'Dec'),
('London', '1', 'Tuesday', 'Josie', 44.27 , 'Sep'),
('Dusseldorf', '4', 'Monday', 'Manfred', 77.00 , 'Jul'),
('Dusseldorf', '3', 'Tuesday', 'Inga', 9.99, 'Jun'),
('Dusseldorf', '4', 'Wednesday', 'Manfred', 86.81 , 'Jul'),
('London', '6', 'Friday', 'Josie', 74.02, 'Oct'),
('Dusseldorf', '1', 'Saturday', 'Manfred', 43.11, 'Apr');

##homework
## Find all sales records 
## (and all columns) that took place in the London store, 
## not in December, but sales concluded by Bill or Frank for 
## the amount higher than £50
SELECT * FROM SALES1
WHERE Store = "London"
AND Month != "Dec"
AND SalesPerson IN ("Bill", "Frank")
AND SalesAmount > 50;

##find out how many sales took place each week
SELECT COUNT(*) FROM SALES1;

SELECT WEEK, COUNT(*) AS weekly_sales_number
FROM SALES1
GROUP BY Week;

## Find out how many sales took place each week (and present 
## data by week in descending
## and then in ascending order)
SELECT Week, COUNT(*) AS weekly_sales_number
FROM SALES1
GROUP BY Week
ORDER BY Week DESC;

## Find out how many sales were recorded each week
## on different days of the week
SELECT Week, Day, COUNT(*) AS weekly_daily_sales_number
FROM SALES1
GROUP BY Week, Day
ORDER BY WEEK, day;

## We need to change salesperson's name Inga to Annette
UPDATE SALES1
SET SalesPerson = "Annette"
WHERE SalesPerson = "Inga";

## find out how many sales did Annette do
SELECT SalesPerson, SalesAmount
FROM SALES1
WHERE SalesPerson = "Annette";

##OR

SELECT SUM(SalesAmount) as total_annette_sales
FROM SALES1
WHERE SalesPerson = "Annette";

##OR

SELECT COUNT(*)
FROM SALES1
WHERE SalesPerson = "Annette";

## Find the total sales amount by each person by day
SELECT SalesPerson, Day, SUM(SalesAmount)
FROM SALES1
GROUP BY SalesPerson, Day;

## How much (sum) each person sold for the given period
SELECT SalesPerson, SUM(SalesAmount) AS total_sales_by_person
FROM SALES1
GROUP BY SalesPerson;

## How much (sum) each person sold for the given period, 
## including the number of sales per
##person, their average, lowest and highest sale amounts
SELECT SalesPerson, 
	SUM(SalesAmount) AS total_sales_by_person,
	COUNT(SalesAmount) AS number_sales_by_person,
	AVG(SalesAmount) AS avg_sales_by_person,
	MIN(SalesAmount) AS min_sales_by_person,
	MAX(SalesAmount) AS max_sales_by_person
FROM SALES1
GROUP BY SalesPerson;

## Find the total monetary sales amount achieved by each store
SELECT Store, SUM(SalesAmount) AS store_sales
FROM SALES1
GROUP BY Store;

## Find the number of sales by each person if 
## they did less than 3 sales for the past period
SELECT SalesPerson, COUNT(*) AS number_sales_by_person
FROM SALES1
GROUP BY SalesPerson
HAVING number_sales_by_person <3;

## Find the total amount of sales by month 
## where combined total is less than £100
SELECT Month, SUM(SalesAmount) AS monthly_sales
FROM SALES1
GROUP BY Month
HAVING monthly_sales < 100;

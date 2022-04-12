### Fruits
CREATE DATABASE fruits;
USE fruits;

-- Create table 1
CREATE TABLE Table1_fruit_basket
(ID INT, Fruit VARCHAR(50));
INSERT INTO Table1_fruit_basket 
(ID, Fruit)
VALUES
(1, 'pear'),
(2, 'apple'),
(3, 'kiwi'),
(4, 'orange'),
(5, 'banana');

-- Create table 2
CREATE TABLE Table2_fruit_basket
(ID INT, Fruit VARCHAR(50));
INSERT INTO Table2_fruit_basket 
(ID, Fruit)
VALUES
(1, 'pear'),
(2, 'apple'),
(3, 'kiwi'),
(6, 'melon'),
(7, 'peach'),
(8, 'plum');

SELECT *
FROM Table1_fruit_basket;
SELECT *
FROM Table2_fruit_basket;

### Practice 
-- inner join example
SELECT 
	t1.ID AS t1_ID, 
    t2.ID AS t2_ID, 
    t1.Fruit AS t1_fruit, 
    t2.Fruit AS t2_fruit
FROM Table1_fruit_basket t1
INNER JOIN
Table2_fruit_basket t2
ON t1.ID = t2.ID;

-- left join example
SELECT 
	t1.ID AS t1_ID, 
    t2.ID AS t2_ID, 
    t1.Fruit AS t1_fruit, 
    t2.Fruit AS t2_fruit
FROM Table1_fruit_basket t1
LEFT JOIN
Table2_fruit_basket t2
ON t1.ID = t2.ID;

-- right join example
SELECT 
	t1.ID AS t1_ID, 
    t2.ID AS t2_ID, 
    t1.Fruit AS t1_fruit, 
    t2.Fruit AS t2_fruit
FROM Table1_fruit_basket t1
RIGHT JOIN
Table2_fruit_basket t2
ON t1.ID = t2.ID;

-- union example
SELECT ID, Fruit
FROM Table1_fruit_basket t1
UNION
SELECT ID, Fruit
FROM Table2_fruit_basket t2;

-- union all example
SELECT ID, Fruit
FROM Table1_fruit_basket t1
UNION ALL
SELECT ID, Fruit
FROM Table2_fruit_basket t2;

-- subquery example
-- all fruits in Table 2 that are in table 1 with letter a in the fruit name
SELECT *
FROM Table2_fruit_basket t2
WHERE t2.Fruit IN
(
	SELECT Fruit 
	FROM Table1_fruit_basket
    WHERE Fruit LIKE '%a%'
    
    );


### Parts
CREATE DATABASE Parts;
USE Parts;

CREATE TABLE SUPPLIER (
	S_ID VARCHAR(2),
	SNAME VARCHAR(10),
	STATUS INT,
	CITY VARCHAR(10)
);

CREATE TABLE PART
(
P_ID CHAR(2),
PNAME CHAR(10),
COLOUR CHAR(10),
WEIGHT INT,
CITY CHAR(10)
);


CREATE TABLE PROJECT
(
J_ID CHAR(2),
JNAME CHAR(10),
CITY CHAR(10)
);

CREATE TABLE SUPPLY
(
S_ID CHAR(2),
P_ID CHAR(2),
J_ID CHAR(2),
QUANTITY INT
);

INSERT INTO PROJECT 
(J_ID,JNAME, CITY)
VALUES 
('J1', 'SORTER', 'PARIS'),
('J2', 'DISPLAY', 'ROME'),
('J3', 'OCR', 'ATHENS'),
('J4', 'CONSOLE', 'ATHENS'),
('J5', 'RAID', 'LONDON'),
('J6', 'EDS', 'OSLO'),
('J7', 'TAPE', 'LONDON');

SELECT * from project;

INSERT INTO PART 
(P_ID,PNAME, COLOUR, WEIGHT, CITY)
VALUES 
('P1', 'NUT', 'RED', 12, 'LONDON'),
('P2', 'BOLT', 'GREEN', 17, 'PARIS'),
('P3', 'SCREW', 'BLUE', 17, 'ROME'),
('P4', 'SCREW', 'RED', 14, 'LONDON'),
('P5', 'CAM', 'BLUE', 12, 'PARIS'),
('P6', 'COG', 'RED', 19, 'LONDON');

INSERT INTO SUPPLIER (S_ID,SNAME,STATUS,CITY)
SELECT 'S1','SMITH',20,'LONDON'
UNION ALL
SELECT 'S2','JONES',10,'PARIS'
UNION ALL
SELECT 'S3','BLAKE',30,'PARIS'
UNION ALL
SELECT 'S4','CLARK',20,'LONDON'
UNION ALL
SELECT 'S5', 'ADAMS',30,'ATHENS';

SELECT * from supplier;

INSERT INTO SUPPLY
SELECT 'S1','P1','J1',200
UNION ALL
SELECT 'S1','P1','J4',700
UNION ALL
SELECT 'S2','P3','J1',400
UNION ALL
SELECT 'S2','P3','J2',200
UNION ALL
SELECT 'S2','P3','J3',200
UNION ALL
SELECT 'S2','P3','J4',500
UNION ALL
SELECT 'S2','P3','J5',600
UNION ALL
SELECT 'S2','P3','J6',400
UNION ALL
SELECT 'S2','P3','J7',800
UNION ALL
SELECT 'S2','P5','J2',100
UNION ALL
SELECT 'S3','P3','J1',200
UNION ALL
SELECT 'S3','P4','J2',500
UNION ALL
SELECT 'S4','P6','J3',300
UNION ALL
SELECT 'S4','P6','J7',300
UNION ALL
SELECT 'S5','P2','J2',200
UNION ALL
SELECT 'S5','P2','J4',100
UNION ALL
SELECT 'S5','P5','J5',500
UNION ALL
SELECT 'S5','P5','J7',100
UNION ALL
SELECT 'S5','P6','J2',200
UNION ALL
SELECT 'S5','P1','J4',100
UNION ALL
SELECT 'S5','P3','J4',200
UNION ALL
SELECT 'S5','P4','J4',800
UNION ALL
SELECT 'S5','P5','J4',400
UNION ALL
SELECT 'S5','P6','J4',500;

SELECT * from supply;

### Customers
CREATE DATABASE customers;
USE customers;

CREATE TABLE customer (
    customer_id INTEGER NOT NULL,
    first_name VARCHAR(55) NOT NULL,
    last_name VARCHAR(55) NULL,
    CONSTRAINT PK_customer PRIMARY KEY (customer_id)
);

CREATE TABLE address (
    address_id INTEGER NOT NULL,
    building_number VARCHAR(55) NOT NULL,
    street VARCHAR(55) NOT NULL,
    city VARCHAR(55),
    post_code VARCHAR(55) NOT NULL,
    country VARCHAR(55),
    CONSTRAINT PK_Address PRIMARY KEY (address_id)
);

CREATE TABLE email_address (
    email_address_id INTEGER NOT NULL,
    email_address_customer_id INTEGER,
    email_address VARCHAR(55) NOT NULL,
    CONSTRAINT PK_email_address PRIMARY KEY (email_address_id)
);

CREATE TABLE phone_number (
    phone_number_id INTEGER NOT NULL,
    phone_number_customer_id INTEGER NOT NULL,
    phone_number VARCHAR(55) NOT NULL,
    CONSTRAINT PK_phone_number PRIMARY KEY (phone_number_id)
);

INSERT INTO address 
(address_id, building_number, street, city, post_code, country) 
VALUES 
(1, '20', 'Birch Alley', 'London', 'SE24 0AB', 'UK'),
(2, '15', 'Oak Street', NULL, 'SE25 0XY', NULL),
(3, '10', 'Streatham Street', 'London', 'SE21 1BF', 'UK'),
(4, '18', 'Victorai Street', 'London', 'SW4 3AL', 'UK');

INSERT INTO customer 
(customer_id, first_name, last_name) 
VALUES 
(1, 'Jon', 'Flanders'),
(2, 'Sam', 'Smith'),
(3, 'Sara', 'Peters'),
(4, 'Alex', 'Wanson'),
(5, 'Phil', 'Maxil'),
(6, 'Lily', 'Cathrie');

INSERT INTO email_address 
(email_address_id, email_address_customer_id, email_address)
VALUES 
(1, 2, 'ssmith@mail.com'),
(2, 1, 'sample1@mail.com'),
(3, 1, 'sample2@mail.com'),
(4, 3, 'sample3@mail.com'),
(5, 6, 'sample4@mail.com');
 
 
INSERT INTO phone_number 
(phone_number_id, phone_number_customer_id, phone_number) 
VALUES 
(1, 1, '555-1212'),
(2, 2, '555-3344'),
(3, 4, '555-2133'),
(4, 6, '555-6789'),
(5, 3, '555-0934');

ALTER TABLE email_address  
ADD  CONSTRAINT 
FK_email_address_customer 
FOREIGN KEY(email_address_customer_id)
REFERENCES customer (customer_id);

ALTER TABLE phone_number   
ADD  CONSTRAINT FK_phone_number_customer 
FOREIGN KEY(phone_number_customer_id)
REFERENCES customer (customer_id);

USE customers;

SELECT * FROM address;
SELECT * FROM customer;
SELECT * FROM email_address;
SELECT * FROM phone_number;

-- find all customers with phone numbers in the database
SELECT c.customer_id, c.first_name, c.last_name, p.phone_number
FROM customer c
RIGHT JOIN
phone_number p
ON
c.customer_id = p.phone_number_customer_id;

-- find all customers(with names) with both email address and phone number
SELECT c.first_name, c.last_name, email.email_address, p.phone_number
FROM email_address email
INNER JOIN
phone_number p
ON 
email.email_address_customer_id = p.phone_number_customer_id
LEFT JOIN
customer c
ON 
c.customer_id = email.email_address_customer_id
;

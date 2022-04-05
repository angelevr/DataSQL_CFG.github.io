# Using the table 'parts', return all unique part names.
USE Parts;
select * from parts;
SELECT DISTINCT PNAME FROM PARTs;
SELECT DISTINCT P.P_ID, P.PNAME
FROM  PARTs P;

# Refer to the table 'projects' and return all projects that are run in London
USE Project;
SELECT * FROM PROJECTs;
SELECT * FROM PROJECTs
WHERE CITY = 'LONDON';

select * from projects;

# Today we are going to be pizza makers, bakers and small restaurant owners!
# We accept orders online or by telephone and deliver pizza to our customers.
# We need to create a database to hold information about our customers, so we can keep records of their names, addresses, phone numbers, email addresses and any other useful information like placed orders.
# Design and create a relational normalised database called customers
# set reasonable primary keys to the tables
# set NOT NULL constraints on the columns that you think must have values

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
CREATE TABLE orders (
    order_id INTEGER NOT NULL,
    orders_customer_id INTEGER NOT NULL,
    order_date DATE NOT NULL,
    CONSTRAINT PK_order_id PRIMARY KEY (order_id)
);

INSERT INTO address
(address_id, building_number, street, city, post_code, country)
VALUES
(1, 20, "Birch Alley", "London", "SE24 0AB", "UK"),
(2, 17, "Oak Street", "London", "SE25 0XY", NULL);
INSERT INTO customer
(customer_id, first_name, last_name)
VALUES
(1, "Jon" "Flanders"),
(2, "Sam", "Smith");
INSERT INTO email_address
(email_address_id, email_address_customer_id, email_address)
 VALUES
 (1, 2, "ssmith@mail.com"),
 (2, 1, "on@mail.com");
 INSERT INTO phone_number
 (phone_number_id, phone_number_customer_id, phone_number)
 VALUES
 (1, 1, ‘555-1212’),
 (2, 2, ‘555-3344’);
 INSERT INTO orders
 (order_id, orders_customer_id, order_date)
 VALUES
 (1, 1, ‘2019-08-20’),
 (2, 2, ‘2019-03-15’);

# let's also modify some tables in the database, so we add Foreign Keys to tables and define relationships between our tables.
# add some data to the tables in the customers database
# alter tables email_address and phone_number in the customers database by adding Foreign keys that reference Primary keys from relevant tables.
# remove the table called orders from our database

ALTER TABLE email_address
ADD  CONSTRAINT
FK_email_address_customer
FOREIGN KEY(email_address_customer_id)
REFERENCES customer (customer_id);
ALTER TABLE phone_number
ADD  CONSTRAINT FK_phone_number_customer
FOREIGN KEY(phone_number_customer_id)
REFERENCES customer (customer_id);

## Homework
# Usa parts DB to write the following queries
# find the name and weight of each red part
# find all unique supplier(s) name from London
# create a new database called SHOP
# add a new table called SALES1.

USE parts;
select * from parts;
SELECT DISTINCT  PNAME, WEIGHT FROM PARTS
WHERE COLOUR = "RED";

SELECT * FROM PARTS
WHERE CITY = "LONDON";

CREATE DATABASE SHOP;
USE SHOP;
CREATE TABLE SALES1(
Store VARCHAR(50),
Week INT NOT NULL,
Day VARCHAR(50),
SalesPerson VARCHAR(50),
SalesAmount INT NOT NULL,
Month VARCHAR(50);

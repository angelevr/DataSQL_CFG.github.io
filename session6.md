-- Turn ON Event Scheduler 
SET GLOBAL event_scheduler = ON;
USE practice;

-- EXAMPLE 1 --> one time event

CREATE TABLE monitoring_events
(ID INT NOT NULL AUTO_INCREMENT, 
Last_Update TIMESTAMP,
PRIMARY KEY (ID));
​
​
-- We are creating an event that will be scheduled for us
-- Change Delimiter
DELIMITER //

CREATE EVENT one_time_event
ON SCHEDULE AT NOW() + INTERVAL 1 MINUTE
DO BEGIN
	INSERT INTO monitoring_events(Last_Update)
	VALUES (NOW());
END//
​
-- Change Delimiter
DELIMITER ;

-- Select Data to see that our table is empty
-- Then Select data again in approx 1 min to see what happened. 
SELECT *
FROM monitoring_events;

-- Clean up (optional)
-- DROP TABLE monitoring_events;
-- DROP EVENT one_time_event;

-- EXAMPLE 2 --> reccuring event

CREATE TABLE monitoring_events_version2
(ID INT NOT NULL AUTO_INCREMENT, 
Last_Update TIMESTAMP,
PRIMARY KEY (ID));
​
-- Change Delimiter
DELIMITER //

CREATE EVENT recurring_time_event
ON SCHEDULE EVERY 2 SECOND
STARTS NOW()
DO BEGIN
	INSERT INTO monitoring_events_version2(Last_Update)
	VALUES (NOW());
END//
​
-- Change Delimiter
DELIMITER ;

-- Select Data
SELECT *
FROM monitoring_events_version2
ORDER BY ID DESC;


-- Clean up - this is necessary, otherwise your table will keep on being populated by the event
-- DROP TABLE monitoring_events_version2;
-- DROP EVENT recurring_time_event;

# Trigger
SELECT *
FROM bakery.sweet;
​
-- BEFORE Trigger Example - this one ensures font consistency for inserted items
-- Change Delimiter
DELIMITER //

CREATE TRIGGER sweetItem_Before_Insert
BEFORE INSERT on sweet
FOR EACH ROW
BEGIN
	SET NEW.item_name = CONCAT(UPPER(SUBSTRING(NEW.item_name,1,1)),
						LOWER(SUBSTRING(NEW.item_name FROM 2)));
END//
​
-- Change Delimiter
DELIMITER ;

-- Insert Data
INSERT INTO sweet (id, item_name, price)
VALUES (123, 'apple_pie', 1.2);

INSERT INTO sweet (id, item_name, price)
VALUES (456, 'caramel slice', 0.9);

INSERT INTO sweet (id, item_name, price)
VALUES (789, 'YUM YUM', 0.65);
​
SELECT *
FROM bakery.sweet;

# View example
USE bank;

SELECT *
FROM accounts;

CREATE VIEW account_without_money_info
AS
	SELECT customer_id, customer_name
    FROM accounts;

SELECT *
FROM account_without_money_info;

INSERT INTO account_without_money_info
VALUES (3, "Name");

SELECT *
FROM account_without_money_info;

SELECT *
FROM accounts;

CREATE VIEW total_bank_money
AS
	SELECT SUM(money_amount)
    FROM accounts;
    
SELECT *
FROM total_bank_money;

CREATE VIEW accounts_with_name_starting_j
AS
	SELECT *
    FROM accounts
    WHERE customer_name LIKE 'J%'
    WITH CHECK OPTION;

SELECT *
FROM accounts_with_name_starting_j;

-- this will fail because Name does not start with J (because of WITH CHECK OPTION)
INSERT INTO accounts_with_name_starting_j
VALUES (3, "Name", 100);

INSERT INTO accounts_with_name_starting_j
VALUES (3, "Jame", 100);

# Employee Example
CREATE DATABASE employees;

USE employees;

CREATE TABLE employee(
	employee_id INT NOT NULL,
	first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    date_of_birth DATE NOT NULL,
    salary INT NOT NULL,
    start_date DATE NOT NULL,
    PRIMARY KEY (employee_id)
);

CREATE TABLE dept(
	dept_id INT NOT NULL,
    dept_name VARCHAR(50) NOT NULL,
    PRIMARY KEY (dept_id)
);

CREATE TABLE employee_dept(
	employee_id INT NOT NULL,
    dept_id INT NOT NULL,
    FOREIGN KEY (employee_id) REFERENCES employee(employee_id),
    FOREIGN KEY (dept_id) REFERENCES dept(dept_id)
);

INSERT INTO employee
VALUES
(1, "Jack", "Miller", "1990-01-01", 5000, "2020-02-01"),
(2, "Jenni", "Xi", "1992-01-01", 6000, "2022-02-01"),
(3, "Alan", "Clark", "1990-03-01", 2000, "2010-03-01"),
(4, "Billie", "Mark", "1980-01-03", 3000, "2019-05-01");

INSERT INTO dept
VALUES
(1, 'Accounting'),
(2, 'Product');

INSERT INTO employee_dept
VALUES
(1, 1),
(1, 2),
(2, 2),
(3, 1),
(4, 2);

SELECT * 
FROM employee;

CREATE VIEW employee_without_confidential_info
AS
	SELECT employee_id, first_name, last_name, start_date
    FROM employee;

SELECT *
FROM employee_without_confidential_info;

ALTER TABLE employee
MODIFY COLUMN salary INT NULL Default 1000,
MODIFY COLUMN date_of_birth DATE NULL Default "1999-03-04";

INSERT INTO employee_without_confidential_info
VALUES
(5, "Nate", "Lam", current_date());

SELECT *
FROM employee_without_confidential_info;

SELECT * 
FROM employee;

-- WITH CHECK OPTION

CREATE VIEW employees_high_earners
AS
	SELECT *
    FROM employee
    WHERE salary > 4000
    WITH CHECK OPTION;

SELECT *
FROM employees_high_earners;

-- this will fail because of check option
INSERT INTO employees_high_earners
VALUES 
(7, "E", "X", "1995-01-05", 1000, current_date());

-- this will not fail
INSERT INTO employees_high_earners
VALUES 
(7, "E", "X", "1995-01-05", 5000, current_date());

/* STORED PROCEDURES 

create a procedure to easily insert values into table with some pre processing
*/

DELIMITER //
CREATE PROCEDURE easyinsert(
	IN employee_id INT,
    IN first_name VARCHAR(50),
    IN last_name VARCHAR(50),
    IN dob DATE,
    IN salary INT
)
BEGIN
	INSERT INTO employee
    VALUES
    (employee_id, CONCAT(UPPER(SUBSTRING(first_name, 1, 1)), LOWER(SUBSTRING(first_name FROM 2))), 
    last_name, dob, salary, current_date());
END //
DELIMITER ;

CALL easyinsert(8, "first", "last", "1990-01-01", 1000);

SELECT * 
FROM employee;

/* STORED FUNCTIONS 

create function to flag anyone who has worked in the company for more than 10 years

CREATE FUNCTION [function name] (function parameters)
*/

DELIMITER //
CREATE FUNCTION has_long_tenure(start_date DATE)
RETURNS VARCHAR(50)
DETERMINISTIC
BEGIN
	DECLARE tenure_type VARCHAR(50);
    IF (YEAR(current_date()) - YEAR(start_date)) >= 10 THEN
		SET tenure_type = 'long_tenure';
	ELSE
		SET tenure_type = 'short_tenure';
	END IF;
	RETURN (tenure_type);
END//

SELECT first_name, last_name, start_date, has_long_tenure(start_date)
FROM employee;

# Project: Students Information Database
# This DB has Student's information and personal records.

# 1. Create a DB
CREATE DATABASE Students;

# 2. Make at least 5 tables and fill with relevant data
USE Students;
CREATE TABLE Personal_Info(
Student_ID INT NOT NULL,
Firstname VARCHAR(50),
Surname VARCHAR(50),
Address VARCHAR(50),
Gender VARCHAR(50) NOT NULL,
DOB VARCHAR(50),
Mobile INT,
Email VARCHAR(50),
CONSTRAINT PK_Personal_info PRIMARY KEY (Student_ID));

INSERT Personal_info
(Student_ID, Firstname, Surname, Address, Gender, DOB, Mobile, Email)
VALUES
(1, "John", "Smith", "23 Frognoal Close", "Male", "15/10/2000", 098437583, "john,smith@gmail.com"),
(2, "Nick", "Jose", "45 Biddle Road", "Male", "3/2/2000", 093758392, "nickjose@hotmail.com"),
(3, "Christina", "King", "1 Manor Clone", "Female", "22/9/2001", 038174947, "kingc@gmail.com"),
(4, "John", "Lory", "24 Queen's Road", "Male", "12/9/2001", 048672910, "john.lory@hotmail.com"),
(5, "Rachel", "Petrou", "32 Buckingham Street", "Female", "30/5/2000", 08528194, "petrouR@gmail.com"),
(6, "Jen", "Elliot", "19 Lara Close", "Female", "26/3/2001", 0957361844, "Jen.elliot@gmail.com");

CREATE TABLE Parent_info(
Parent_ID_Student_ID INT NOT NULL,
ParentType VARCHAR(50) NOT NULL,
Firstname VARCHAR(50),
Surname VARCHAR(50),
Address VARCHAR(50),
Gender VARCHAR(50),
Mobile INT NOT NULL,
Email VARCHAR(50),
CONSTRAINT PK_Parent_info PRIMARY KEY (Parent_ID_Student_ID));

INSERT Parent_Info
(Parent_ID_Student_ID, ParentType, Firstname, Surname, Address, Gender, Mobile, Email)
VALUES
("1", "Father", "Callum", "Smith", "23 Frognoal Close", "Male", 09472812, "callum.smith@gmail.com"),
("2", "Father", "Andrew", "Jose", "45 Biddle Road", "Male", 03137594, "andrewjose@hotmail.com"),
("3", "Mother", "Georgia", "King", "1 Manor Clone", "Female", 04738573, "kingg@gmail.com"),
("4", "Mother", "Penelope", "Lory", "24 Queen's Road", "Female", 04738294, "penel.lory@hotmail.com"),
("5", "FosterParent", "Rachel", "Nogueras", "32 Buckingham Street", "Female", 04827194, "racheln@gmail.com"),
("6", "FosterParent", "Jen", "Malpesa", "19 Lara Close", "Female", 04738924, "Jen.malpesa@gmail.com");

CREATE TABLE Outreach_Session(
DOS VARCHAR(50),
Location VARCHAR(50) NOT NULL,
Numberofstudents INT,
Activities VARCHAR(50),
SafeguardingIssues VARCHAR(50) NOT NULL);

INSERT Outreach_Session
(DOS, Location, Numberofstudents, Activities, SafeguardingISsues)
VALUES
("1/1/2022", "Exeter", "3", "Rope jumping", "no"),
("7/2/2022", "Barnstaple", "4", "Toasties", "yes"),
("14/3/2022", "Exeter", "1", "discussion", "no"),
("13/4/2022", "Tiverton", "2", "future me workshop", "no"),
("23/4/2022", "Tiverton", "3", "toasties", "no"),
("30/4/2022", "Exeter", "4", "music workshop", "yes");

CREATE TABLE OpenAccess_session(
DOS VARCHAR(50),
Location VARCHAR(50),
NameofYouthWorker VARCHAR(50),
Numberofstudents INT,
Activities VARCHAR(50),
DiscussionTopic VARCHAR(50));

INSERT OpenAccess_session
(DOS, Location, NameofYouthWorker, Numberofstudents, Activities, DiscussionTopic)
VALUES
("3/1/2022", "Exeter", "Chris", "12", "rhythm learning", "music help"),
("3/2/2022", "Tiverton", "John", "20", "football", "teamwork"),
("20/2/2022", "Barnstaple", "Jenna", "15", "gym session", "fitness"),
("12/3/2022", "Exeter", "Jim", "9", "VR game", "teamwork"),
("25/3/2022", "Tiverton", "Chris", "25", "gaming", "teamwork"),
("30/3/2022", "Exeter", "John", "14", "painting", "creativity");

CREATE TABLE FieldTrip(
Firstname VARCHAR(50),
ConsentGranted VARCHAR(50),
Disability VARCHAR(50),
TeamName VARCHAR(50));

INSERT FieldTrip
(FirstName, ConsentGranted, Disability, TeamName)
VALUES
("John", "yes", "no", "Bulls"),
("Chris", "yes", "no", "Bulls"),
("Jen", "yes", "no", "Amazing"),
("Christina", "no", "no", "Amazing"),
("Rachel", "yes", "no", "Bulls");

# 3. Set primary and foreign key constraints to create relations between the tables
ALTER TABLE parent_info
ADD  CONSTRAINT
FK_parent_info_personal_info
FOREIGN KEY(parent_ID_Student_ID)
REFERENCES Personal_info (student_id);

# 4. Using any type of the joins create a view that combines multiple tables in a logical way
# --> joining the surnames of both parents and students with their corresponding names
SELECT
s.Surname AS s_Surname,
s.Firstname as s_FirstName,
p.Firstname AS p_Firstname
FROM Personal_info s
INNER JOIN
parent_info p
ON s.Surname = p.Surname;

# 5. In your DB, create a stored function that can be applied to a query in your DB
-- 1. COUNT how many consents where granted
SELECT COUNT(ConsentGranted)
FROM fieldtrip
WHERE ConsentGranted = 'yes';

-- 2. CASE: did we have enough sessions around developing teamwork skills?
SELECT DiscussionTopic,
	CASE
		WHEN DiscussionTopic = "teamwork" THEN "valuable_skills_learned"
        ELSE "need_more_sessions"
	END
FROM openaccess_session;

-- 3. How many sessions had more than 15 students?
SELECT COUNT(*)
FROM
(SELECT SUM(Numberofstudents) AS total_students
FROM openaccess_session
GROUP BY Location
HAVING total_students >= 15) adequate_number_attendance;

-- 4. Christina also has consent now
UPDATE fieldtrip
SET consentgranted = "yes"
WHERE Firstname = 'Christina';

-- 5. ability to undo changes
# John's father decided to withdraw his consent
START TRANSACTION;

UPDATE fieldtrip
SET consentgranted = "no"
WHERE Firstname = 'John';

SELECT *
FROM fieldtrip;

ROLLBACK;

SELECT *
FROM fieldtrip;

-- 6. commit my changes
COMMIT;

-- Turn ON Event Scheduler 
SET GLOBAL event_scheduler = ON;
USE students;

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

# Trigger
SELECT *
FROM openaccess_session;
​
-- BEFORE Trigger Example - this one ensures font consistency for inserted items
-- Change Delimiter
DELIMITER //

CREATE TRIGGER Location_Before_Insert
BEFORE INSERT on openaccess_session
FOR EACH ROW
BEGIN
	SET NEW.Activities = CONCAT(UPPER(SUBSTRING(NEW.Activities,1,1)),
						LOWER(SUBSTRING(NEW.Activities FROM 2)));
END//
​
-- Change Delimiter
DELIMITER ;

-- Insert Data
INSERT INTO openaccess_session (DOS, Location, NameofYouthWorker, Numberofstudents, Activities, DiscussionTopic)
VALUES ("3/5/2022", 'Tiverton', "Chris", 20, "rope jumpting", "teamwork"),
("5/5/2022", "Exeter", "Chris", 13, "music session", "creativity");

SELECT *
FROM openaccess_session;

# View example
USE students;

SELECT *
FROM outreach_session;

CREATE VIEW account_without_SafeguardingIssues
AS
	SELECT DOS, Location, Numberofstudents, Activities
    FROM outreach_session;

SELECT *
FROM account_without_SafeguardingIssues;

INSERT INTO account_without_SafeguardingIssues
VALUES ("5/6/2022", "Exmouth", 5, "discussion");

SELECT *
FROM account_without_SafeguardingIssues;

# back up
use mock_project_db;

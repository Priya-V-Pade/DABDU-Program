Practical=1
Design and Develop SQL DDL statements which demonstrate the use of SQL objects such as Table, View, Index, Sequence, Synonym, different constraints etc. 

-- Create Department Table
CREATE TABLE Department (
  DeptID INT PRIMARY KEY,
  DeptName VARCHAR(50)
);

-- Create Employee Table with Constraints
CREATE TABLE Employee (
  EmpID INT PRIMARY KEY,
  EmpName VARCHAR(50) NOT NULL,
  Salary DECIMAL(10,2),
  DeptID INT,
  FOREIGN KEY (DeptID) REFERENCES Department(DeptID)
);

-- Insert Sample Data
INSERT INTO Department VALUES (1, 'HR');
INSERT INTO Department VALUES (2, 'IT');

INSERT INTO Employee VALUES (101, 'Priya', 40000, 2);
INSERT INTO Employee VALUES (102, 'Amit', 30000, 1);

-- Create a View
CREATE VIEW Emp_View AS
SELECT EmpName, Salary, DeptName
FROM Employee
JOIN Department ON Employee.DeptID = Department.DeptID;

-- Create an Index on Salary
CREATE INDEX idx_salary ON Employee(Salary);

-- Create a Sequence for Employee IDs
CREATE SEQUENCE emp_seq
START WITH 103
INCREMENT BY 1;

-- Use the Sequence to Insert a New Employee
INSERT INTO Employee (EmpID, EmpName, Salary, DeptID)
VALUES (emp_seq.NEXTVAL, 'Riya', 35000, 2);

-- Create a Synonym for Employee Table
CREATE SYNONYM Emp FOR Employee;

-- Select Data using Synonym
SELECT * FROM Emp;
_____________________________________________________________________________________________________________________________________________
Practical = 2

Design and develop SQL Queries – all types of joins, sub-queries and views.
-- Create Tables
CREATE TABLE Department (
  DeptID INT PRIMARY KEY,
  DeptName VARCHAR(50)
);

CREATE TABLE Employee (
  EmpID INT PRIMARY KEY,
  EmpName VARCHAR(50),
  DeptID INT,
  Salary DECIMAL(10,2),
  FOREIGN KEY (DeptID) REFERENCES Department(DeptID)
);

-- Insert Data
INSERT INTO Department VALUES
(1, 'HR'),
(2, 'IT');

INSERT INTO Employee VALUES
(101, 'Amit', 1, 40000),
(102, 'Neha', 2, 60000),
(103, 'Ravi', 2, 50000),
(104, 'Simran', NULL, 30000);

---------------------------------------------------
-- 1️⃣ INNER JOIN
---------------------------------------------------
SELECT EmpName, DeptName
FROM Employee
INNER JOIN Department ON Employee.DeptID = Department.DeptID;

---------------------------------------------------
-- 2️⃣ LEFT JOIN
---------------------------------------------------
SELECT EmpName, DeptName
FROM Employee
LEFT JOIN Department ON Employee.DeptID = Department.DeptID;

---------------------------------------------------
-- 3️⃣ RIGHT JOIN
---------------------------------------------------
SELECT EmpName, DeptName
FROM Employee
RIGHT JOIN Department ON Employee.DeptID = Department.DeptID;

---------------------------------------------------
-- 4️⃣ CROSS JOIN
---------------------------------------------------
SELECT EmpName, DeptName
FROM Employee
CROSS JOIN Department;

---------------------------------------------------
-- 5️⃣ SUBQUERY (Find employees with salary > Amit)
---------------------------------------------------
SELECT EmpName, Salary
FROM Employee
WHERE Salary > (SELECT Salary FROM Employee WHERE EmpName = 'Amit');

---------------------------------------------------
-- 6️⃣ CREATE VIEW
---------------------------------------------------
CREATE VIEW Emp_View AS
SELECT EmpName, DeptName, Salary
FROM Employee
LEFT JOIN Department ON Employee.DeptID = Department.DeptID;

-- View Data
SELECT * FROM Emp_View;

_____________________________________________________________________________________________________________________________________________

Practical = 3
Design and develop SQL queries for any suitable database application using SQL DML statements.

-- Step 1: Create a Database
CREATE DATABASE InstituteDB;

-- Step 2: Use the Database
USE InstituteDB;

-- Step 3: Create a Table
CREATE TABLE StudentRecord (
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50),
    Course VARCHAR(50),
    Marks INT
);

-- Step 4: Insert Records (INSERT Command)
INSERT INTO StudentRecord (Student_ID, Name, Course, Marks)
VALUES 
(1, 'Amit', 'Computer Science', 85),
(2, 'Priya', 'Information Tech', 78),
(3, 'Rahul', 'Computer Science', 90),
(4, 'Harshada', 'Electronics', 88);

-- Step 5: Display All Records (SELECT Command)
SELECT * FROM StudentRecord;

-- Step 6: Update Record (Change Marks)
UPDATE StudentRecord
SET Marks = 95
WHERE Student_ID = 3;

-- Step 7: Change Name (UPDATE Command)
UPDATE StudentRecord
SET Name = 'Rohit'
WHERE Student_ID = 1;

-- Step 8: Delete a Record (DELETE Command)
DELETE FROM StudentRecord
WHERE Student_ID = 2;

-- Step 9: Display Final Records
SELECT * FROM StudentRecord;

-- Step 10: Display Students with Marks greater than 70
SELECT * FROM StudentRecord WHERE Marks > 70;

-- Step 11: Sort students by Marks in descending order (highest first)
SELECT * FROM StudentRecord
ORDER BY Marks DESC;

-- Step 12: Sort only student names by Marks in descending order
SELECT Name FROM StudentRecord
ORDER BY Marks DESC;
_____________________________________________________________________________________________________________________________________________

Practocal = 4
Design and Develop Unnamed PL/SQL code block: Use of Control structure and Exception handling is mandatory.

CREATE DATABASE LibraryDB;
USE LibraryDB;

CREATE TABLE Borrower (
    RollNo INT,
    Name VARCHAR(50),
    BookName VARCHAR(50),
    DateOfIssue DATE,
    Status VARCHAR(5)
);

INSERT INTO Borrower VALUES
(1, 'Harshada', 'Operating System', '2022-09-19', 'I'),
(2, 'Vaishnavi', 'OOP', '2022-07-24', 'I'),
(3, 'Mohan', 'Microprocessor', '2022-06-12', 'I'),
(4, 'Om', 'Mechanics', '2022-04-19', 'I');

CREATE TABLE Fine (
    RollNo INT,
    FineDate DATE,
    Amount INT
);

-- ============================================
-- Procedure 1: Calculate Fine with Exception Handling
-- ============================================
DELIMITER //
CREATE PROCEDURE CalcFine(IN r INT, IN b VARCHAR(50))
BEGIN
    DECLARE d DATE;
    DECLARE diff INT;
    DECLARE CONTINUE HANDLER FOR NOT FOUND 
        BEGIN
            SET d = NULL;
            SELECT 'No record found for given RollNo and Book!' AS Message;
        END;

    BEGIN
        SELECT DateOfIssue INTO d 
        FROM Borrower 
        WHERE RollNo = r AND BookName = b;

        IF d IS NOT NULL THEN
            SET diff = DATEDIFF(CURDATE(), d);

            IF diff BETWEEN 15 AND 30 THEN
                INSERT INTO Fine VALUES(r, CURDATE(), diff * 5);
            ELSEIF diff > 30 THEN
                INSERT INTO Fine VALUES(r, CURDATE(), diff * 50);
            ELSE
                SELECT 'No fine — Book returned on time.' AS Message;
            END IF;
        END IF;
    EXCEPTION
        WHEN SQLEXCEPTION THEN
            SELECT 'SQL Error occurred while calculating fine!' AS Message;
        WHEN SQLWARNING THEN
            SELECT 'Warning occurred while calculating fine!' AS Message;
    END;
END //
DELIMITER ;

-- ============================================
-- Procedure 2: Submit Book with Exception Handling
-- ============================================
DELIMITER //
CREATE PROCEDURE SubmitBook(IN r INT)
BEGIN
    BEGIN
        UPDATE Borrower SET Status='R' WHERE RollNo=r;
        DELETE FROM Fine WHERE RollNo=r;
        SELECT 'Book returned successfully!' AS Message;
    EXCEPTION
        WHEN SQLEXCEPTION THEN
            SELECT 'Error while submitting the book!' AS Message;
    END;
END //
DELIMITER ;

-- ============================================
-- Call Procedures
-- ============================================
CALL CalcFine(1,'Operating System');
CALL CalcFine(2,'OOP');
CALL CalcFine(3,'Microprocessor');
CALL CalcFine(4,'Mechanics');

SELECT * FROM Fine;

CALL SubmitBook(1);
CALL SubmitBook(2);
CALL SubmitBook(3);
CALL SubmitBook(4);

SELECT * FROM Borrower;
___________________________________________________________________________________________________________________________________________________________________
Practical = 5
Design and Develop Named PL/SQL Block: PL/SQL Stored Procedure and Stored Function. 
Write a Stored Procedure namely pro_Graded for the categorization of student. If marks scored by students in examination is <=1500 and marks>=990 then student will be placed in distinction category if marks scored are between 989 and900 category is first class, if marks899and 825 category is Higher Second Class. 

create database Score;
use Score;

create table stud_marks(name varchar(20),total_marks int(5));

create table Result(roll_no int(3) primary key,name varchar(20),class varchar(20));

insert into stud_marks values('Vaishnavi',995);
insert into stud_marks values('Harshada',865);
insert into stud_marks values('Samart',920);
insert into stud_marks values('Mohan',1000);
insert into stud_marks values('Soham',745);
select * from stud_marks;
insert into Result(roll_no,Name) values(1,'Vaishnavi');
insert into Result(roll_no,Name) values(2,'Harshada');
insert into Result(roll_no,Name) values(3,'Samart');
insert into Result(roll_no,Name) values(4,'Mohan');
insert into Result(roll_no,Name) values(5,'Soham');
select * from Result;


delimiter //

create function func_Grade(r int)
returns varchar(25)
deterministic
begin
    declare m int;
    declare grade varchar(25);

    -- get total marks for given roll_no
    select total_marks into m 
    from stud_marks 
    where name = (select name from Result where roll_no = r);

    -- determine grade
    if m >= 990 then
        set grade = 'Distinction';
    elseif m >= 900 then
        set grade = 'FirstClass';
    elseif m >= 825 then
        set grade = 'SecondClass';
    else
        set grade = '--';
    end if;

    return grade;
end //

delimiter ;


DELIMITER //
create function func_Grade(r int(2))
returns varchar(25)
deterministic
begin
declare grade varchar(25);
call proc_Grade(r,grade);
return grade;
end //
DELIMITER ;


select func_Grade(1); 
select func_Grade(2); 
select func_Grade(3); 
select func_Grade(4); 
select func_Grade(5); 
select * from Result;

___________________________________________________________________________________________________________________________________________________________________
Practical = 6
Write a PL/SQL block of code using parameterized Cursor that will merge the data available in the newly created table N_Roll Call with the data available in the table O-Roll Call. If the data in the first table already exist in the second table, then that data should be skipped.

CREATE DATABASE class1;
USE class1;

CREATE TABLE O_RollCall (
  roll_no INT(3),
  name VARCHAR(20)
);

CREATE TABLE N_RollCall (
  roll_no INT(3),
  name VARCHAR(20)
);

INSERT INTO O_RollCall VALUES 
(1,'Harshada'),
(2,'Vaishnavi'),
(3,'Soham'),
(5,'Vaibhav'),
(6,'Shubham'),
(9,'Sanket'),
(11,'Harish');


SELECT * FROM O_RollCall;
SELECT * FROM N_RollCall;

DELIMITER //
CREATE PROCEDURE cursor_proc_p1()
BEGIN
  DECLARE fin INT DEFAULT 0;
  DECLARE old_roll INT(3);
  DECLARE old_name VARCHAR(20);
  DECLARE new_roll INT(3);

  DECLARE old_csr CURSOR FOR SELECT roll_no, name FROM O_RollCall;
  DECLARE new_csr CURSOR FOR SELECT roll_no FROM N_RollCall;

  DECLARE CONTINUE HANDLER FOR NOT FOUND SET fin = 1;

  OPEN old_csr;
  OPEN new_csr;

  loop_label: LOOP
    FETCH old_csr INTO old_roll, old_name;
    FETCH new_csr INTO new_roll;
    
    IF fin = 1 THEN
      LEAVE loop_label;
    END IF;

    IF old_roll <> new_roll THEN
      INSERT INTO N_RollCall VALUES (old_roll, old_name);
    END IF;
  END LOOP;

  CLOSE old_csr;
  CLOSE new_csr;
END //
DELIMITER ;

DELIMITER //
CREATE PROCEDURE cursor_proc_p2(IN r1 INT)
BEGIN
  DECLARE r2 INT;
  DECLARE exit_loop BOOLEAN DEFAULT FALSE;

  DECLARE c1 CURSOR FOR 
    SELECT roll_no FROM O_RollCall WHERE roll_no > r1;

  DECLARE CONTINUE HANDLER FOR NOT FOUND SET exit_loop = TRUE;

  OPEN c1;

  loop_e: LOOP
    FETCH c1 INTO r2;

    IF exit_loop THEN
      LEAVE loop_e;
    END IF;

    IF NOT EXISTS (SELECT * FROM N_RollCall WHERE roll_no = r2) THEN
      INSERT INTO N_RollCall 
      SELECT * FROM O_RollCall WHERE roll_no = r2;
    END IF;
  END LOOP;

  CLOSE c1;
END //
DELIMITER ;


CALL cursor_proc_p2(5);
SELECT * FROM N_RollCall;

CALL cursor_proc_p2(3);
CALL cursor_proc_p1();

SELECT * FROM O_RollCall;
SELECT * FROM N_RollCall;
___________________________________________________________________________________________________________________________________________________________________

Practical = 7
Write a database trigger on Library table. The System should keep track of the records that are being updated or deleted. The old value of updated or deleted records should be added in Library Audit table. 

USE CollegeDB;

-- Step 1: Create Library table
CREATE TABLE Library (
    book_id INT PRIMARY KEY,
    book_name VARCHAR(100),
    author_name VARCHAR(100),
    price INT
);

-- Step 2: Create Library_Audit table
CREATE TABLE Library_Audit (
    audit_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    book_name VARCHAR(100),
    author_name VARCHAR(100),
    price INT,
    operation_type VARCHAR(20),
    operation_date DATETIME
);

-- Step 3: Trigger for UPDATE
DELIMITER $$
CREATE TRIGGER trg_library_update
AFTER UPDATE
ON Library
FOR EACH ROW
BEGIN
    INSERT INTO Library_Audit (book_id, book_name, author_name, price, operation_type, operation_date)
    VALUES (OLD.book_id, OLD.book_name, OLD.author_name, OLD.price, 'UPDATE', NOW());
END$$
DELIMITER ;

-- Step 4: Trigger for DELETE
DELIMITER $$
CREATE TRIGGER trg_library_delete
AFTER DELETE
ON Library
FOR EACH ROW
BEGIN
    INSERT INTO Library_Audit (book_id, book_name, author_name, price, operation_type, operation_date)
    VALUES (OLD.book_id, OLD.book_name, OLD.author_name, OLD.price, 'DELETE', NOW());
END$$
DELIMITER ;

-- Step 5: Insert records into Library
INSERT INTO Library VALUES 
(1, 'DBMS Concepts', 'Shreya', 550),
(2, 'Operating Systems', 'Shruti', 650),
(3, 'Computer Network', 'Riya', 400);

-- Step 6: View Library table
SELECT * FROM Library;

-- Step 7: Perform UPDATE and DELETE operations
UPDATE Library SET price = 450 WHERE book_id = 3;
DELETE FROM Library WHERE book_id = 2;

-- Step 8: View Library_Audit (trigger results)
SELECT * FROM Library_Audit;

-- Step 9: View final Library table
SELECT * FROM Library;
___________________________________________________________________________________________________________________________________________________________________

Practical = 8
Design and develop a PL/SQL function named calculate -Bonus that accepts the employee as an input parameter. The function should calculate & return the bonus amount based on the employee’s department (sales, HR, IT, others). Implement proper exception handling.



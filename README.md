# SQL-Joins-Practice

-- Create database
CREATE DATABASE IF NOT EXISTS Volume1;
USE Volume1;

-- =========================
-- Tables
-- =========================
-- Departments
DROP TABLE IF EXISTS Projects;
DROP TABLE IF EXISTS Employees;
DROP TABLE IF EXISTS Departments;

CREATE TABLE Departments (
    DeptID INT PRIMARY KEY,
    DeptName VARCHAR(50)
);

-- Employees
CREATE TABLE Employees (
    EmpID INT PRIMARY KEY,
    EmpName VARCHAR(50),
    DeptID INT,
    Salary DECIMAL(10,2),
    FOREIGN KEY (DeptID) REFERENCES Departments(DeptID)
);

-- Projects
CREATE TABLE Projects (
    ProjectID INT PRIMARY KEY,
    ProjectName VARCHAR(50),
    DeptID INT,
    LeadEmpID INT,
    FOREIGN KEY (DeptID) REFERENCES Departments(DeptID),
    FOREIGN KEY (LeadEmpID) REFERENCES Employees(EmpID)
);

-- =========================
-- Sample Data
-- =========================
INSERT INTO Departments (DeptID, DeptName) VALUES
(1, 'Engineering'),
(2, 'Marketing'),
(3, 'HR'),
(4, 'Sales');

INSERT INTO Employees (EmpID, EmpName, DeptID, Salary) VALUES
(101, 'Alice',   1, 70000),
(102, 'Bob',     1, 65000),
(103, 'Charlie', 2, 50000),
(104, 'Diana',   2, 52000),
(105, 'Eve',     3, 45000),
(106, 'Sujal',   4, 45000);

INSERT INTO Projects (ProjectID, ProjectName, DeptID, LeadEmpID) VALUES
(201, 'Website Revamp',     1, 101),
(202, 'Product Launch',     2, 103),
(203, 'Recruitment Drive',  3, 105),
(204, 'Data Migration',     1, 102);

-- =========================
-- Practice Queries
-- =========================

-- 1) List all employees along with their department names.
SELECT e.EmpName, d.DeptName
FROM Employees e
JOIN Departments d ON d.DeptID = e.DeptID;

-- 2) Find all projects and the names of their lead employees.
SELECT p.ProjectName, e.EmpName
FROM Projects p
JOIN Employees e ON e.EmpID = p.LeadEmpID;

-- 3) Show employees and the project names they lead (if any).
SELECT e.EmpName, p.ProjectName
FROM Employees e
LEFT JOIN Projects p ON p.LeadEmpID = e.EmpID;

-- 4) Display departments that do not have any projects assigned.
SELECT d.DeptName
FROM Departments d
LEFT JOIN Projects p ON d.DeptID = p.DeptID
WHERE p.ProjectID IS NULL;

-- 5) Find the average salary of employees in each department.
SELECT d.DeptName, AVG(e.Salary) AS Average_Salary
FROM Employees e
JOIN Departments d ON d.DeptID = e.DeptID
GROUP BY d.DeptName;

-- 6) List all employees who work in the same department as Bob (excluding Bob).
SELECT e.EmpName
FROM Employees e
WHERE e.DeptID = (SELECT DeptID FROM Employees WHERE EmpName = 'Bob')
  AND e.EmpName <> 'Bob';

-- 7) Show the department name and the number of employees working in each.
SELECT d.DeptName, COUNT(e.EmpID) AS Num_Employees
FROM Departments d
LEFT JOIN Employees e ON e.DeptID = d.DeptID
GROUP BY d.DeptName;

-- 8) Retrieve the names of employees who are project leads along with their department name.
SELECT e.EmpName, p.ProjectName, d.DeptName
FROM Employees e
JOIN Projects p   ON p.LeadEmpID = e.EmpID
JOIN Departments d ON d.DeptID = e.DeptID;

-- 9) Find all employees who are not leading any projects.
SELECT e.EmpName, d.DeptName
FROM Employees e
JOIN Departments d ON d.DeptID = e.DeptID
LEFT JOIN Projects p ON p.LeadEmpID = e.EmpID
WHERE p.ProjectID IS NULL;

-- 10) Show all employees with salary greater than the average salary of their department.
SELECT e.EmpName AS Employee, d.DeptName AS Department, e.Salary
FROM Employees e
JOIN Departments d ON d.DeptID = e.DeptID
WHERE e.Salary > (
    SELECT AVG(e2.Salary)
    FROM Employees e2
    WHERE e2.DeptID = e.DeptID
);

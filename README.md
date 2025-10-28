# Emp_Performance
Designed and executed complex SQL queries to analyze employee performance, departmental efficiency, and regional sales trends for an organization. The project focused on mastering SQL joins, subqueries, window functions, and aggregations — similar to real-world business intelligence tasks.

![Screenshot 2025-10-28 004629](https://github.com/user-attachments/assets/f1d823bf-1a8b-4ff8-8ee8-51ed02214d56)


CREATE DATABASE employees;

-- Check

SELECT * FROM departments;
SELECT * FROM employee_details;
SELECT * FROM sales;

-- Create three tables

-- 1 . Departments

CREATE TABLE departments (
	department_id INT PRIMARY KEY,
    department_name VARCHAR(20) NOT NULL,
    location VARCHAR(20)
    );
    
-- 2. employee

CREATE TABLE employee_details (
	employee_id INT PRIMARY KEY,
    first_name VARCHAR(10),
    last_name VARCHAR(10),
    email VARCHAR (20),
    hire_date VARCHAR(20),
    salary DECIMAL(20,2),
    department_id INT,
    manager_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id),
    FOREIGN KEY (manager_id) REFERENCES employee_details(employee_id)
    );
	
-- 3. sales

CREATE TABLE sales (
    sale_id INT PRIMARY KEY,
    employee_id INT,
    sale_date DATE,
    amount DECIMAL(10,2),
    region VARCHAR(50),
    FOREIGN KEY (employee_id) REFERENCES employee_details(employee_id)
);

-- Inserting values

INSERT INTO departments VALUES
(1, 'Sales', 'New York'),
(2, 'HR', 'Boston'),
(3, 'IT', 'San Francisco'),
(4, 'Finance', 'Chicago'),
(5, 'Marketing', 'Seattle');

INSERT INTO employee_details VALUES
(101, 'John', 'Doe', 'john.doe@corp.com', '2018-03-15', 70000, 1, NULL),
(102, 'Jane', 'Smith', 'jane.smith@corp.com', '2019-07-01', 65000, 1, 101),
(103, 'Emily', 'Davis', 'emy.davis@corp.com', '2020-01-10', 60000, 1, 101),
(104, 'Michael', 'Brown', 'michaeln@corp.com', '2017-10-21', 80000, 3, NULL),
(105, 'Sarah', 'Wilson', 'sarah.son@corp.com', '2021-06-30', 55000, 3, 104),
(106, 'David', 'Lee', 'david.le@corp.com', '2016-12-05', 90000, 4, NULL),
(107, 'Chris', 'Taylor', 'chris.tr@corp.com', '2022-01-01', 50000, 5, NULL),
(108, 'Anna', 'Martin', 'anna.martin@corp.com', '2018-09-12', 62000, 2, NULL);

INSERT INTO sales VALUES
(1, 102, '2023-01-15', 5000.00, 'East'),
(2, 102, '2023-02-10', 7000.00, 'East'),
(3, 103, '2023-03-05', 4500.00, 'West'),
(4, 103, '2023-04-20', 6200.00, 'West'),
(5, 101, '2023-01-10', 9000.00, 'Central'),
(6, 101, '2023-02-18', 8500.00, 'Central'),
(7, 107, '2023-03-15', 3000.00, 'North'),
(8, 107, '2023-05-10', 2800.00, 'North'),
(9, 105, '2023-04-01', 4100.00, 'West'),
(10, 105, '2023-05-25', 3900.00, 'West');


-- Querries

-- 1 . join name and their department

SELECT CONCAT(E.first_name,E.last_name) AS Emp_name , D.department_name, E.department_id
FROM employee_details E 
	JOIN departments D ON  D.department_id = E.department_id;

-- 2 . group by

SELECT MIN(E.salary) AS Min_S , E.department_id
FROM employee_details E 
GROUP BY department_id;

-- #. . find top performer

SELECT CONCAT(E.first_name,E.last_name) AS Emp_name , E.employee_id , E.department_id, S.amount AS Total_sales
	FROM employee_details E 
		JOIN sales S ON
			E.employee_id = S.employee_id
            ORDER BY Total_sales DESC
            LIMIT 1 ;

-- OR OPTION 2

SELECT CONCAT(E.first_name,E.last_name) AS Emp_name 
FROM employee_details E 
WHERE employee_id = (SELECT employee_id
FROM sales
GROUP BY employee_id
order by Sum(amount) DESC
LIMIT 1);

-- #. Rank

SELECT CONCAT(E.first_name,E.last_name) AS Emp_name ,Sum(S.amount) AS Total_Sales,
	RANK() OVER (ORDER BY Sum(S.amount) DESC) AS Sales_Rank
FROM employee_details E
	JOIN sales S ON
		E.employee_id = S.employee_id
	GROUP BY E.employee_id;
    
--#. List all employees hired after 2020.

SELECT CONCAT(E.first_name,E.last_name) AS Emp_name , E.hire_date AS DOJ
FROM employee_details E
	WHERE E.hire_date > '2019-12-30';
    
-- Find employees whose salary is greater than $60,000.

SELECT CONCAT(E.first_name,E.last_name) AS Emp_name , E.salary 
FROM employee_details E
	WHERE E.salary >'60000';
    
--#. Display employee full name and email, sorted by last name.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , E.email 
	FROM employee_details E 
    order by E.last_name;

--#. Show all departments located in “New York.”

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , E.department_id , D.department_name , D.location
FROM employee_details E 
    JOIN departments D ON
		E.department_id = D.department_id
	WHERE D.location = "New York";
    
--#. List distinct regions where sales happened.

SELECT DISTINCT region
FROM sales;

--#. Find employees whose first name starts with ‘J’.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name 
FROM employee_details E 
WHERE E.first_name LIKE '%i%';

--#. Show top 5 highest-paid employees.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , E.salary
	FROM employee_details E 
    order by E.salary DESC
    LIMIT 5;

--#. Display the total number of employees in each department.

SELECT D.department_name , COUNT(E.employee_id) AS No_of_Employees
	FROM departments D 
    JOIN employee_details E ON
		D.department_id = E.department_id
	GROUP BY D.department_name;
    
--#. Count how many sales each employee made.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , SUM(S.amount) AS Total_Sales
	FROM employee_details E 
    JOIN sales S ON
		S.employee_id = E.employee_id
	GROUP BY Emp_name;

--#. Get each employee’s department name.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , D.department_name
	FROM employee_details E 
    JOIN departments D ON
		E.department_id = E.department_id;
        
-- #. List employees and their managers’ names (self join)

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , CONCAT(M.first_name,' ' ,M.last_name) AS Mng_name
	 FROM employee_details E 
     LEFT JOIN employee_details M ON
		E.manager_id = M.employee_id;
        
-- #. Show all sales with employee names and their departments.


SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , SUM(S.amount) AS Total_Sales, D.department_name
	FROM employee_details E 
		JOIN sales S ON 
        E.employee_id = S.employee_id
        JOIN departments D ON
        E.department_id = D.department_id
        group by E.employee_id;

-- #. Find departments that have no employees.

SELECT d.department_id, d.department_name, d.location
FROM departments d
LEFT JOIN employee_details e 
    ON d.department_id = e.department_id
WHERE e.department_id IS NULL;

INSERT INTO departments VALUES (6, 'Legal', 'Houston');

-- #. Find employees who have not made any sales.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , E.employee_id, S.amount
	FROM employee_details E 
    LEFT JOIN sales S ON
		E.employee_id = S.employee_id
	where S.employee_id IS NULL;
    
-- #. Show total sales per department (joining all three tables).

SELECT D.department_name , SUM(S.amount) AS Total_Sales
	FROM departments D 
		JOIN employee_details E ON
			D.department_id = E.department_id
		JOIN sales S ON
			E.employee_id = S.employee_id
	GROUP BY D.department_name;
    
-- #. Get employees working in departments located in “San Francisco.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , D.department_name , D.location
	FROM employee_details E 
		JOIN departments D ON
			E.department_id = D.department_id
		WHERE location = 'San Francisco';
        
-- #. Find the employee with the highest sale in each region.

SELECT 
    region,
    CONCAT(first_name, ' ', last_name) AS Emp_name,
    amount AS highest_sale, rnk
FROM (
    SELECT 
        S.region,
        E.first_name, E.last_name,
        S.amount,
        RANK() OVER (PARTITION BY S.region ORDER BY S.amount DESC) AS rnk
    FROM sales S
    JOIN employee_details E 
        ON S.employee_id = E.employee_id
) ranked 
WHERE rnk = 1;

-- #. Show average salary per location.

SELECT round(Avg(E.salary),2) AS Avg_Salary , D.location
	FROM employee_details E 
		JOIN departments D ON
			E.department_id = D.department_id
	GROUP BY D.location;
    
--#. List managers and how many people report to them.

SELECT 
    CONCAT(M.first_name, ' ', M.last_name) AS Manager_Name,
    COUNT(E.employee_id) AS Num_of_Reports
FROM employee_details E
JOIN employee_details M 
    ON E.manager_id = M.employee_id
GROUP BY M.employee_id, M.first_name, M.last_name;

-- #. Find each employee’s cumulative salary — that is, show a running total of salaries in the order of hire date.

SELECT 
    employee_id,
    CONCAT(first_name, ' ', last_name) AS Emp_Name,
    hire_date,
    salary,
    SUM(salary) OVER (ORDER BY hire_date) AS Cumulative_Salary
FROM employee_details
ORDER BY hire_date;


--#. Find total sales amount for each employee.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , SUM(S.amount)
	FROM employee_details E 
		LEFT JOIN sales S ON
			E.employee_id = S.employee_id
		GROUP BY Emp_name;

-- #..	Calculate average sale amount per region.

SELECT S.region , ROUND(avg(S.amount),1) AS Average_Sale_Region
	FROM sales S 
    GROUP BY S.region;

-- #..	Find the department with the highest total salary cost.

WITH DTS AS (
		SELECT D.department_name , SUM(E.salary) AS HTS 
		FROM departments D 
		JOIN employee_details E ON
			D.department_id = E.department_id
		GROUP BY D.department_name
        ),
        ranked AS (
				SELECT department_name, HTS , RANK() OVER ( ORDER BY HTS DESC) AS rnk
                FROM DTS
                )
		SELECT * FROM ranked 
			WHERE rnk = 1;
        
-- #..	Calculate monthly total sales (using DATE_TRUNC or MONTH() depending on DB).

SELECT 
	YEAR(sale_date) AS sale_year,
    MONTH(sale_date) AS sale_month,
    SUM(amount) AS total_sales
FROM sales
GROUP BY sale_year, sale_month
ORDER BY sale_month, sale_month;

SELECT 
    DATE_FORMAT(sale_date, '%Y-%m') AS sale_month,
    SUM(amount) AS total_sales
FROM sales
GROUP BY DATE_FORMAT(sale_date, '%Y-%m')
ORDER BY sale_month;

-- #..	Show employees earning more than their department average.

SELECT CONCAT(E.first_name,' ' ,E.last_name) AS Emp_name , E.salary , D.department_name
	FROM employee_details E 
		JOIN departments D ON
			E.department_id = D.department_id
		WHERE E.salary > (
							SELECT AVG(E2.salary)
                            FROM employee_details E2
                            WHERE E2.department_id = E.department_id
						)
					ORDER BY E.department_id, E.salary DESC;
                    
-- #..	Find how many employees were hired each year.

SELECT COUNT(employee_id) AS Total_hires , YEAR(hire_date) AS Year
	FROM employee_details
    GROUP BY YEAR(hire_date);
    
-- #..	Find the max, min, and avg salary across all departments.

SELECT Max(E.salary) , Min(E.salary) , AVG(E.salary) , D.department_name
	FROM employee_details E 
		JOIN departments D ON
			E.department_id = D.department_id
		GROUP BY E.department_id;
        
-- #..	Find regions where total sales exceeded $10,000.

SELECT S.region , Sum(S.amount) 
	FROM sales S 
		GROUP BY S.region
        HAVING Sum(S.amount) > 10000;
        
-- #.	Get employees who made at least 3 sales.

SELECT E.employee_id , Count(S.sale_id)
	FROM employee_details E 
		JOIN sales S ON
			E.employee_id = S.employee_id
		GROUP BY E.employee_id
        HAVING Count(S.sale_id) >= 3;
        
-- #.	Count how many departments each location has.

SELECT location , COUNT(department_id)
	FROM departments
    GROUP BY location;

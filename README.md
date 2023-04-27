# sql-challenge

Task:
To do a research project about people whom the company employed during the 1980s and 1990s. All that remains of the employee database from that period are six CSV files.

Three steps:
1. Data Modeling
2. Data Engineering
3. Data Analysis

#DATA MODELING

departments
-
dept_no PK VARCHAR 
dept_name VARCHAR

employees
-
emp_no PK INT
birth_date DATE 
first_name VARCHAR
last_name VARCHAR
sex VARCHAR
hire_date DATE 

dept_emp
-
emp_no INT FK >- employees.emp_no 
dept_no VARCHAR FK >- departments.dept_no


dept_managers
-
dept_no VARCHAR FK >- departments.dept_no
emp_no INT FK >- employees.emp_no



salaries
-
emp_no INT FK >- employees.emp_no
salary BIGINT


titles
-
emp_no INT FK >- employees.emp_no
title VARCHAR

## DATA ENGINEERING

CREATE TABLE "departments" (
    "dept_no" VARCHAR(45)   NOT NULL,
    "dept_name" VARCHAR(45)   NOT NULL,
    CONSTRAINT "pk_departments" PRIMARY KEY (
        "dept_no"
     )
);

CREATE TABLE "dept_emp" (
    "emp_no" VARCHAR(10)   NOT NULL,
    "dept_no" VARCHAR(10)   NOT NULL
);

CREATE TABLE "dept_manager" (
    "dept_no" VARCHAR(10)   NOT NULL,
    "emp_no" VARCHAR(10)   NOT NULL
);

CREATE TABLE "employees" (
    "emp_no" VARCHAR(10)   NOT NULL,
    "emp_title_id" VARCHAR(5)   NOT NULL,
    "birth_date" VARCHAR(10)  NOT NULL,
    "first_name" VARCHAR(45)   NOT NULL,
    "last_name" VARCHAR(45)   NOT NULL,
    "sex" VARCHAR(1)   NOT NULL,
    "hire_date" VARCHAR(10)  NOT NULL,
    CONSTRAINT "pk_employees" PRIMARY KEY (
        "emp_no"
     )
);



CREATE TABLE "salaries" (
    "emp_no" VARCHAR(10)   NOT NULL,
    "salary" BIGINT   NOT NULL
);

CREATE TABLE "titles" (
    "title_id" VARCHAR(5)   NOT NULL,
    "title" VARCHAR(45)   NOT NULL,
    CONSTRAINT "pk_titles" PRIMARY KEY (
        "title_id"
     )
);

ALTER TABLE "dept_emp" ADD CONSTRAINT "fk_dept_emp_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");

ALTER TABLE "dept_emp" ADD CONSTRAINT "fk_dept_emp_dept_no" FOREIGN KEY("dept_no")
REFERENCES "departments" ("dept_no");

ALTER TABLE "dept_manager" ADD CONSTRAINT "fk_dept_manager_dept_no" FOREIGN KEY("dept_no")
REFERENCES "departments" ("dept_no");

ALTER TABLE "dept_manager" ADD CONSTRAINT "fk_dept_manager_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");

ALTER TABLE "employees" ADD CONSTRAINT "fk_employees_emp_title_id" FOREIGN KEY("emp_title_id")
REFERENCES "titles" ("title_id");

ALTER TABLE "salaries" ADD CONSTRAINT "fk_salaries_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");


### DATA ANALYSIS

-- List the employee number, last name, first name, sex, and salary of each employee

SELECT e.emp_no, e.last_name, e.first_name, e.sex, s.salary
FROM employees e
JOIN salaries s
ON e.emp_no = s.emp_no;

--List the first name, last name, and hire date for the employees who were hired in 1986.

SELECT first_name, last_name, hire_date 
FROM employees
WHERE hire_date BETWEEN '1/1/1986' and '12/31/1986'
ORDER BY hire_date ASC;


--List the manager of each department along with their department number, department name, employee number, last name, and first name.

SELECT dm.dept_no,d.dept_name, dm.emp_no, e.last_name, e.first_name
FROM dept_manager dm
JOIN employees e
ON dm.emp_no = e.emp_no
JOIN departments d
ON dm.dept_no = d.dept_no
ORDER BY d.dept_name ASC;

--List the department number for each employee along with that employee’s employee number, last name, first name, and department name.

SELECT d.dept_no, e.emp_no, e.last_name, e.first_name, d.dept_name
FROM employees e
JOIN dept_emp de 
ON e.emp_no = de.emp_no
JOIN departments d
ON d.dept_no = de.dept_no
ORDER BY d.dept_name ASC;

--List first name, last name, and sex of each employee whose first name is Hercules and whose last name begins with the letter B.

SELECT first_name, last_name, sex
FROM employees 
WHERE first_name = 'Hercules' AND last_name LIKE 'B%'
ORDER BY last_name ASC;

--List each employee in the Sales department, including their employee number, last name, and first name.

SELECT e.emp_no, e.last_name, e.first_name, d.dept_name
FROM employees e
JOIN dept_emp de 
ON e.emp_no = de.emp_no
JOIN departments d
ON d.dept_no = de.dept_no
WHERE d.dept_name = 'Sales';

--List each employee in the Sales and Development departments, including their employee number, last name, first name, and department name.

SELECT e.emp_no, e.last_name, e.first_name, d.dept_name
FROM employees e
JOIN dept_emp de 
ON e.emp_no = de.emp_no
JOIN departments d
ON d.dept_no = de.dept_no
WHERE d.dept_name = 'Sales' OR d.dept_name = 'Development'
ORDER BY d.dept_name ASC;

--List the frequency counts, in descending order, of all the employee last names (that is, how many employees share each last name).

SELECT last_name, count(emp_no) as num_employees_with_same_last_name
FROM employees
GROUP BY last_name
ORDER BY num_employees_with_same_last_name DESC;
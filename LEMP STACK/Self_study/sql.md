# Structured Query Language (SQL) Documentation

# Introduction

__SQL (Structured Query Language) is a powerful and standardized language designed for interacting with relational databases. It empowers users to create, retrieve, update, and delete data within these databases. This documentation provides a foundational understanding of SQL syntax and explores the most frequently used commands.__

## Basic Syntax

SQL statements follow a specific structure that defines the operation to be performed. Here's a breakdown of some core statements:

- __SELECT Statement:__ Used to retrieve data from one or more tables.

```
SELECT column1, column2, ... FROM table_name;
```

- __INSERT Statement:__ Used to add new records to a table.

```
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);
```
- __UPDATE Statement:__ Used to modify existing records within a table.

```
UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition;
```
- __DELETE Statement:__ Used to remove records from a table.

```
DELETE FROM table_name WHERE condition;
```

- __CREATE TABLE Statement:__ Used to define and create a new table within the database.

```
CREATE TABLE table_name (
  column1 datatype,
  column2 datatype,
  ...
);
```
- __ALTER TABLE Statement:__ Used to modify the structure of an existing table by adding, removing, or changing columns.
```
ALTER TABLE table_name ADD column_name datatype;
```

- __DROP TABLE Statement:__ Used to permanently remove a table from the database.

```
DROP TABLE table_name;
```

## Commonly Used Commands

Beyond the basic structure, SQL offers a rich set of commands for data manipulation and retrieval. Here are some of the most essential ones:

- __SELECT:__ This fundamental command retrieves data from tables. You can specify which columns to retrieve and filter the results using a WHERE clause.

```
SELECT * FROM employees;  -- Select all columns from the 'employees' table.
```
- __WHERE:__ This clause allows you to filter the retrieved data based on a specific condition.

```
SELECT * FROM employees WHERE department = 'HR';  -- Select employees from the 'HR' department.
```

- __ORDER BY:__ This command sorts the result set in ascending or descending order based on a specified column.

```
SELECT * FROM employees ORDER BY salary DESC;  -- Order employees by salary (descending).
```
- __GROUP BY:__ This command groups rows with matching values in a particular column, enabling summary calculations.

```
SELECT department, COUNT(*) FROM employees GROUP BY department;  -- Count employees by department.
```
- __JOIN:__ This powerful command combines data from two or more tables based on a shared column, allowing for relational queries.

```
SELECT employees.name, departments.name FROM employees JOIN departments ON employees.department_id = departments.id;  -- Join employee names with department names.
```

- __COUNT:__ This aggregate function calculates the total number of rows in a result set.

```
SELECT COUNT(*) FROM employees;  -- Count the number of employees.
```
- __SUM, AVG, MIN, MAX:__ These aggregate functions perform calculations on a set of values, enabling operations like calculating total salary (SUM), average salary (AVG), minimum salary (MIN), and maximum salary (MAX).

```
SELECT SUM(salary) FROM employees;  -- Calculate total employee salary.
```

### Conclusion

This documentation provides a solid foundation for understanding the fundamentals of SQL syntax and commonly used commands. By delving deeper and practicing with more complex queries, one'll gain a comprehensive grasp of SQL's capabilities and unlock its full potential for data management and analysis.
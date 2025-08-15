# SQL Documentation: Basic SQL Commands

This documentation provides an overview of basic SQL commands, enabling users to perform essential database operations such as displaying, creating, deleting, selecting, and inserting data. Each command is explained with syntax and examples for clarity.

## 1. SHOW

### Purpose

The ```SHOW``` command is used to display database information such as tables, databases, or the columns within a table.

### Syntax

```bash
SHOW {DATABASES | TABLES | COLUMNS FROM table_name};
```
Examples:

- Show all databases:
```bash
SHOW DATABASES;
```
- Show all tables in the current database:
```bash
SHOW TABLES;
```
- Show columns in a specific table:
```bash
SHOW COLUMNS FROM table_name;
```

## 2. CREATE

### Purpose

The CREATE command is used to create a new database or table.

### Syntax

- Create a database:
```bash
CREATE DATABASE database_name;
```

- Create a table:
```bash
CREATE TABLE table_name (
    column1_name data_type constraints,
    column2_name data_type constraints,
    ...
);
```

Examples:

- Create a new database:
```bash
CREATE DATABASE my_database;
```
- Create a new table:
```bash
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    position VARCHAR(50),
    salary DECIMAL(10, 2)
);
```

## 3. DROP

### Purpose

The ```DROP``` command is used to delete a database or table permanently.

### Syntax

- Drop a database:
```bash
DROP DATABASE database_name;
```
- Drop a table:
```bash
DROP TABLE table_name;
```

Examples:

- Drop a database:
```bash
DROP DATABASE my_database;
```
- Drop a table:
```bash
DROP TABLE employees;
```

## 4. SELECT

## Purpose

The ```SELECT``` command is used to retrieve data from a database.

### Syntax

```bash
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

Exampls:

- Select all columns from a table:
```bash
SELECT * FROM employees;
```
- Select specific columns from a table:
```bash
SELECT name, position FROM employees;
```
- Select with a condition:
```bash
SELECT name, salary FROM employees WHERE position = 'Manager';
```

## 5. INSERT

### Purpose

The ```INSERT``` command is used to add new rows of data to a table.

### Syntax

```bash
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

Examples:

- Insert a single row into a table:
```bash
INSERT INTO employees (id, name, position, salary)
VALUES (1, 'John Doe', 'Manager', 75000.00);
```

- Insert multiple rows into a table:
```bash
INSERT INTO employees (id, name, position, salary)
VALUES
(2, 'Jane Smith', 'Developer', 65000.00),
(3, 'Emily Johnson', 'Designer', 60000.00);
```

### Conclusion

These basic SQL commands form the foundation for interacting with a relational database. Mastery of SHOW, CREATE, DROP, SELECT, and INSERT commands is essential for database management and manipulation.
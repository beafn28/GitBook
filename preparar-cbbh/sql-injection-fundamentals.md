# SQL Injection Fundamentals

{% file src="../.gitbook/assets/Sql_Injection_Fundamentals_Module_Cheat_Sheet.pdf" %}

## MySQL

### Intro to MySQL

#### Preguntas

**Connect to the database using the MySQL client from the command line. Use the 'show databases;' command to list databases in the DBMS. What is the name of the first database?**

```
mysql --skip-ssl -h 94.237.50.221 -P 33445 -u root -p
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### SQL Statements

**What is the department number for the 'Development' department?**

```
show DATABASES;
use employees;
show TABLES;
select * from departments;
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Query Results

**What is the last name of the employee whose first name starts with "Bar" AND who was hired on 1990-01-01?**

```
 SELECT last_name FROM employees WHERE first_name LIKE 'Bar%' AND hire_date = '1990-01-01';
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### SQL Operators

**In the 'titles' table, what is the number of records WHERE the employee number is greater than 10000 OR their title does NOT contain 'engineer'?**

```
SELECT COUNT(*) AS num_records FROM titles WHERE emp_no > 10000 OR title NOT LIKE '%engineer%';
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## SQL Injections


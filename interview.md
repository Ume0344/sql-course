# Interview Prep

### SQL vs NoSQL
- SQL is relational, formed as tables. We build multiple tables to store data which can have relation among them. ie, MySQL, PostgresSQL, SQLite
[Good example on relational database](https://www.boot.dev/assignments/3971b767-64fa-4403-a94e-a17c445f4d16).
- NoSQL is non-relational, mostly written in key-value or document store. i.e, DyanmoDB, MongoDB etc. NoSQL nest the data.
[Good example on relational vs non-relational](https://www.boot.dev/assignments/25d0e40b-2ce7-4c63-a7da-1732fd2dbb66).

### SQLite vs PostgresSQL
- SQLite is a serverless database management system (DBMS) that has the ability to run within applications, whereas PostgreSQL uses a Client-Server model and requires a server to be installed and listening on a network, similar to an HTTP server.

### Tables
#### Create table
```
CREATE TABLE <tabel_name> (
    <column_name> <data_type>,
    id INTEGER
);
```

#### Alter table
Sometimes we need to alter the schema of a table without deleting the table.

altering table name;
```
ALTER TABLE <table_name>
RENAME TO <new_table_name>;
```

altering column name
```
ALTER TABLE <table_name>
RENAME COLUMN <col_name> TO <new_col_name>;
```
add or drop a column;
```
ALTER TABLE <table_name>
ADD COLUMN <col_name> <col_data_type>

ALTER TABLE employees
ADD COLUMN salary INTEGER
```

### Migrations
A database migration is a set of changes to relational database. (ALTER is also example of migration)
- Good Migrations - Small, incremental, reversible changes to database.
- Bad Migration - If we alter the name of table in database and the applocation code has query based on previous table name. It will break the code. To avoid, it after migration, we should immediately deploy the changes to code to use new queries.
Up vs Down Migration
- Up i.e, create table, delete col, rename col, rename table, delete table. 
- Down; rollbacks to up migrations in case of emergency.

### SQL DataTypes
SQLite - NULL, INTEGER, REAL, TEXT, BLOB (binary large objects used for images, audio etc)
SQLite does have any boolean class, so we use integer(0, 1) to represent boolean values.

### Constraints
A constraint is a rule we create on our database. i,e `NOT NULL` contstraint on a column tells that this column will not accept `NULL` values.
`PRIMARY KEY` constraint is like `NOT NULL && UNIQUE`.

Defining a constraint;
```
CREATE TABLE employees(
    id INTEGER PRIMARY KEY,
    name TEXT UNIQUE, # all data in names column are unique
    title TEXT NOT NULL,
)
```
#### Primary Key
A key that uniquely identifies each row in a table. *One table can only have one primary key*

#### Foreign Key
These keys are what makes relational database relational. They define the relationships between tables. forexample,
```
CREATE TABLE departments (
    id INTEGER PRIMARY KEY,
    department_name TEXT NOT NULL
);
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
    salary INTEGER NOT NULL
    department_id INTEGER,
    FOREIGN KEY (department_id)
    REFERENCES departments(id)
);
```

#### Schema
It defines how the data is organized in database. Datatype, names of field, number of columns are part of schema.

### CRUD (Create, Read, Update, Delete)
HTTP POST - CREATE (CREATE / INSERT)
HTTP GET - READ (SELECT)
HTTP PUT - UPDATE (UPDATE)
HTTP DELETE - DELETE (DELETE)

#### INSERT
```
INSERT INTO employees(id, name, title)
VALUES (1, 'Alex', 'Doctor');
```

A SQL query written in Go;
```
sqlQuery := fmt.Sprintf(`
INSERT INTO users(name, age, country_code)
VALUES ('%s', %v, %s);
`, user.Name, user.Age, user.CountryCode)
```

#### COUNT
`*` is the wildcard.
```
SELECT COUNT(*) FROM users;
```
#### WHERE
```
SELECT username FROM users WHERE is_admin=true;
```
```
SELECT name FROM users WHERE first_name IS NULL;
```
```
SELECT name FROM users WHERE first_name IS NOT NULL;
```
#### DELETE
```
DELETE FROM employees
where id=234;
```
#### DataBase Backup
Some options;
- Take snapshots of database on daily or hourly bases.
- Have a replica which keeps track of all the activity happe

ning in database.
- Soft delete - That you mark a record as deleted by setting a flag (i.e, a column of *deleted* = true)
#### Update
```
UPDATE employees
SET job_title = 'Cloud Engineer', salary = 200000
WHERE id = 234;
```

### ORM (Object Relational Mapping)
A tool which allows to interact with database using a programming language.
In go, it can looks like this;
```
// this struct represent a table
type User struct {
    ID int
    Name string
    isAdmin bool
}

// using ORM
user := User{
    ID: 10,
    Name: 'Max';
    isAdmin: false,
}

db.Create(user)
```
Or;
```
// Using straight SQL 
user := User{
    ID: 10,
    Name: 'Max';
    isAdmin: false,
}

db.Exec("INSERT INTO users (id, name, is_admin) VALUES (?, ?, ?);",
    user.ID, user.Name, user.IsAdmin)
```
Note: The '?' in the SQL query you provided is a placeholder for parameterized queries. It's a way to safely include variables or values in the SQL statement without directly concatenating them into the string. Parameterized queries help prevent SQL injection attacks by ensuring that user inputs are treated as data rather than executable SQL code.

[Should we use ORM?](https://www.boot.dev/assignments/93443c8b-368d-4021-bbf1-939463e34d28)
In production, we usually use ORM but soemtimes we use straight SQL to run complex queries

### Basic Queries

#### AS Clause
An AS clause allows us to "alias" a piece of data in our query. The alias only exists for the duration of the query.
```
SELECT employee_id AS id, employee_name AS name FROM employees;
```
### SQL Functions
IIF;
```
IIF(carA > carB, "Car A is bigger", "Car B is bigger")
```
Exercise:
We need to look through CashPal's transaction data and determine whether or not any of the transactions need to be audited.

Return all the data from the transactions table, and add an extra column at the end called audit.

If a row's was_successful field is true, the audit field should say "No action required".
If a row's was_successful field is false, the audit field should say "Perform an audit".

```
SELECT *, 
  IIF(was_successful, "No action required", "Perform an audit") AS audit
  FROM transactions;

```
#### BETWEEN
```
SELECT employee_name, salary FROM employees
WHERE salary BETWEEN 50000 and 70000;
```
```
SELECT employee_name, salary FROM employees
WHERE salary NOT BETWEEN 50000 and 70000;
```
#### DISTINCT
```
SELECT DISTINCT departments FROM employees;
```
#### Logical Operators
*AND*
```
SELECT product_name, quantity, shipment_status
    FROM products
    WHERE shipment_status = 'pending'
    AND quantity BETWEEN 0 and 10;
```
```
SELECT * 
  FROM users
  WHERE country_code='CA' 
  AND age<18;
```
*OR*
```
SELECT product_name, quantity, shipment_status
    FROM products
    WHERE shipment_status = 'out of stock'
    OR quantity BETWEEN 10 and 100;
```
```
SELECT count(*) AS junior_count 
  FROM users
  WHERE (country_code='US' OR country_code='CA')
  AND age < 18;
```
#### IN
IN returns true or false if the first operand matches any of the values in the second operand. The IN operator is a shorthand for multiple OR conditions.
```
SELECT product_name, shipment_status
    FROM products
    WHERE shipment_status IN ('shipped', 'preparing', 'out of stock');
```
#### LIKE
Sometimes we don't have the luxury of knowing exactly what it is we need to query. Have you ever wanted to look up a song or a video but you only remember part of the name? SQL provides us an option for when we're in situations LIKE this.
**% Operator**
It matches 0 or more characters.
product starts with 'banana'
```
SELECT * FROM products
WHERE product_name LIKE 'banana%';
``` 
product ends with 'banana'
```
SELECT * FROM products
WHERE product_name LIKE '%banana';
``` 
product contains'banana'
```
SELECT * FROM products
WHERE product_name LIKE '%banana%';
```
**_ Operator**
It matches only a single character.
```
SELECT * FROM products
WHERE product_name LIKE '_oot';
```
Above query results in 'boot', 'root'.
```
SELECT * FROM products
WHERE product_name LIKE '__oot';
```
Above query results in 'shoot', 'broot'

Exercise: HR has been able to narrow down their query further! They want a report of all user's whose names that start with Al and are exactly 5 characters long.
```
SELECT * FROM users
WHERE name LIKE 'Al___';
```

## Structuring
### LIMIT
Give us only number of rows mentioned after LIMIT.
```
SELECT first_name, age
FROM Customers
LIMIT 2;
```
Will give 2 rows.

### ORDER BY
It sorts the data (ascendingly by default) and go hand in hand with LIMIT.
```
SELECT first_name, age
FROM Customers
ORDER BY age;
```
For sorting in descending order,
```
SELECT first_name, age
FROM Customers
ORDER BY age desc;
```
Get all records from `Customers` where age is between 25 and 35, order it in descending order and limit it to 2 rows.
```
SELECT *
FROM Customers
Where age Between 25 and 35
ORDER By age desc
LIMIT 2;
```
## Aggregations
A single value derived from combining other large amount of values. i.e, COUNT. We use it when we need to calculate additional data from raw data stored in database. 

### COUNT
```
SELECT COUNT(*)
FROM Customers
Where age Between 25 and 35
ORDER By age desc
LIMIT 2;
``` 

### SUM
To get sum of a column;
```
SELECT SUM(age)
FROM Customers
Where age Between 25 and 35;
```
### MAX
Get max value from a column;
```
SELECT first_name, MAX(age)
FROM Customers
Where age Between 25 and 35;
```
### MIN
Get min value from a column;
```
SELECT first_name, min(age)
FROM Customers;
```
### GROUP BY (Important concept)
It collects the rows into groups and each group contains rows that have identical value by which grouping is done.
```
SELECT *, count(*) as total
FROM shippings
group by status;
```
This will summarize how many distinct shipping status are there and the number of rows having the corresponding status.
### AVERAGE
Get the average of Non-Null values;
```
SELECT avg(age)
FROM customers;
```
### HAVING
To filter the results of `GROUP BY`, we use `HAVING`. It is like `WHERE` clause. The difference is `WHERE` clause filters rows before grouping but `HAVING` filters out after grouping the data through `GROUP BY` clause on groups.
```
SELECT *, count(*)
FROM shippings
group by status
having status='Pending';
```
```
SELECT sender_id, sum(amount) as balance
from transactions
where note LIKE '%lunch%'
group by sender_id
order by balance
having balance > 20;
```
### ROUND
To round the numbers;
```
select round(avg(age)) 
from customers
where country='PK' 
```

## SubQueries
Sometimes, a single query is not enough to retrieve the data, so apply another query on a query. Anything inside `()` is consider as main query on which another query is applied. 
```
select * from customers
where first_name =(
select first_name from customers
where age=25); 
```
```
select * from orders
where customer_id = (
select customer_id from customers
where first_name='John' and last_name='Doe');
```

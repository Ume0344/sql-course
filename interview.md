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

## Normalization

### 3 types of Relationships between entities
1- One to One 
- Where each entity has one and only one relationship with other entity. For example, A student can have only one last name.

2- One to Many
- Where each entity has relationship with multiple entities. For example, A student can have multiple courses. Here, we would define course_id as foreign key to student table.

- How to define a foreign key in a table
```
CREATE TABLE users (
    id  INTEGER PRIMARY KEY,
    name TEXT,
    age INTEGER
);
CREAT TABLE countries (
    id  INTEGER PRIMARY KEY,
    country_code TEXT ,
    user_id TEXT,
    FOREIGN KEY (user_id)
    REFERENCES users(id)
);
```

3- Many to Many 
- Where many entities has relationship with many other entities. For example, A student can have multiple classes and a class can have multiple students. One more example could be that a supplier can have many products and also a product can be supplied by many suppliers.

- UNIQUE (used for many-many relationship), a constraint to have unique rows having one and only one combination of columns in the table. The following example shows that table would not have multiple rows with same product_id and supplier_id. 
```
CREATE TABLE product_suppliers (
    product_id INTEGER,
    supplier_id INTEGER,
    UNIQUE(product_id, supplier_id)
);
```

### Database Normalization
Less normalized -> less data integrity, could have duplicate data.

More normalized -> more data integrity, less duplicate data.

**1st Normal Form (least normalized form)**:
1- Every row must have unique primary  key.
2- There should not be any nested table (table within table).

**2nd Normal Form**:
1- All columns that are not part of primary key should be only dependent on entire primary key (primary key could be of two columns).

**3rd Normal Form**:

1- All the columns not in primary key are dependent only on primary key. 

**Boyce-Codd Normal Form (most normalized form)**:

1- The column which is part of primary key should not be dependent on any other column which is not a part of primary column. 

*In production we dont care alot about 2nd, third and BC normal form*

**Data Integrity** -> Refers to accuracy and consistency of data.

**Data Redundancy** -> Same piece of data is stored in multiple places.

### RULES OF THUMB FOR DATABASE DESIGN
1-Every table should always have a unique identifier (primary key)

2-90% of the time, that unique identifier will be a single column named id

3-Avoid duplicate data

4-Avoid storing data that is completely dependent on other data. Instead, compute it on the fly when you need it.

5-Keep your schema as simple as you can. Optimize for a normalized database first. Only denormalize for speed's sake when you start to run into performance problems.

## Joins
To combine the columns of two or more tables.

### Inner Join (Join)
- Returns rows of table A that have matching records in table B .
```
SELECT *
FROM employees
INNER JOIN departments 
ON employees.department_id = departments.id;
```

### Left Join (OUTER JOIN)
- Returns all rows of table A and matching rows of Table B.
```
SELECT *
FROM employees
LEFT JOIN departments
ON employees.department_id = department.id
```

### Right Join (OUTER JOIN)
- Returns all rows of table B and matching rows of Table A.
- It is same as left join but the position of tables are swapped.

### Full Join
- Returns all rows in table A and table B.

### Self Join
- Joining table to itself.
```
SELECT * FROM employees e
JOIN employees m 
ON e.report_to = m.employee.id
```
### UNION
- To join data returned from multiple queries.
```
SELECT order_id, order_date, 'Active'
FROM orders
WHERE order_date >= '2023-01-01'
UNION
SELECT order_id, order_date, 'Archived'
FROM orders
WHERE order_date < '2023-01-01'

```


### Exercise 
```
The CashPal team needs a report on all the transactions a user has made. Join the users and transactions tables on users.id and transactions.user_id.

Your query should return 3 fields:

A user's name as name
The sum of all of their transaction amounts as sum
The count of all of their transactions as count
Be sure to group the data by the user's id.
Be sure to order the data by the sum field in descending order.
Be sure to still return user records of users who have no transactions.
```
**Solution**
```
SELECT users.name, 
 sum(transactions.amount) as sum,
 count(transactions.id) as count
 FROM users
 LEFT JOIN transactions 
 ON users.id = transactions.user_id
 GROUP BY users.id
 ORDER BY sum DESC;
```

### Exercise
```
Our front-end team is finalizing the profile page for CashPal. We need to write a query that returns all the user data they need for an individual user's profile. The query needs to return the following fields:

The user's id
The user's name
The user's age
The user's username
The user's country name, renamed to country_name
The sum of the user's transaction amounts, renamed to balance
Return only a single user record - specifically the one with id=6
```
**Solution**
```
SELECT users.id, users.name,
  users.age, users.username,
  countries.name as country_name,
  SUM(transactions.amount) as balance
  FROM users
  LEFT JOIN countries 
  ON countries.country_code = users.country_code
  LEFT JOIN transactions
  ON transactions.user_id = users.id
  where users.id = 6
  GROUP BY users.id;
```

## INSERT and UPDATE

### INSERT
To insert data to table.

Inserting a single row;
```
INSERT INTO customers (first_name, last_name, age, address)
VALUES ('John', 'Smith, '23', 'ZWeg1')
```

Inserting multiple rows;
```
INSERT INTO customers (first_name, last_name, age, address)
VALUES ('John', 'Smith, 23, 'ZWeg1'),
       ('Allen', 'Luther', 12, 'Wund23')
```

Inserting hierarchichal data means if we want to add newly generated id of one table to another table. Forexample, if we created a new order and inserted the order id into orders table and also want to add order id to products table.
```
INSERT INTO orders (order_id, customer_id, name)
VALUES (DEFAULT, 1, 'Crock')

INSERT INTO products (order_id, product_id, quantity)
VALUES (LAST_INSERT_ID(), 2, 2)
```

LAST_INSERT_ID() is a built-in function in MySQL which keeps track of last generated id.

### UPDATE
To update a single row.
```
UPDATE customers
SET last_name='David', age=45
WHERE customer_id=3;
```
```
UPDATE customers
SET last_name='David', age_in_months=age_in_years * 12
WHERE customer_id=3;
```

To update multiple rows;

This example will update column contact_person in all rows where customer_country='China'
```
UPDATE customers
SET contact_person='John'
WHERE customer_country='China';
```

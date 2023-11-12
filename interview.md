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

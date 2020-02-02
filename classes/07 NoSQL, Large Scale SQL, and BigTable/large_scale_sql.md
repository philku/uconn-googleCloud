# Large Scale SQL

## Overview

## Lab

## Resources

https://googleclouduconn.blogspot.com/p/chapter-6.html

Chapter 6 Cloud Spanner
Chapter 6. Cloud Spanner: large-scale SQL
What is NewSQL?
What is Spanner?
Administrative interactions with Cloud Spanner
Reading, writing, and querying data
Interleaved tables, primary keys, and other advanced topics
6.1. What is NewSQL?

rich querying, transactional semantics, strong consistency, and horizontal scalability

like SQL databases, but they have the scaling properties of NoSQL

query your data using familiar SELECT * FROM ... syntax

6.2. What is Spanner?

comfortably in MySQL. As the size of the data stored grew out of control, it became a problem.


system that combined the scalability of nonrelational storage with the features of a traditional MySQL database, leading to Spanner.

 NewSQL database that offers many of the features of a relational database (like schemas and JOIN queries) with many of the scaling properties of a nonrelational database (like being able to add more machines).

 split and redistribute data across more machines,

dynamic resizing and shuffling of data chunks

strongly consistent queries so you’ll never have a stale version of the data

6.3. Concepts

 infrastructural concept of an instance, and then dive into the data-model concepts like tables and keys

theoretical concepts like split points and transactions

6.3.1. Instances

a Cloud Spanner instance acts as an infrastructural container that holds a bunch of databases

multiple discrete units of computing power

serving your Spanner data

two aspects: a data-oriented aspect and an infrastructural aspect



instance acts as nothing more than a database container

run a query, you route it to the instance itself

Spanner instances are automatically replicated.

choose a configuration that maps to some combination of different zones.




physical component that makes up the computing power of an instance: a node.

6.3.2. Nodes

Spanner instances are made up of a specific number of nodes that can be used to serve instance data

nodes live in specific zones and are ultimately responsible for handling queries

identical replicas in each of the different zones



 three-node instance in a regional configuration (replicated across three zones), you have a total of nine nodes because each replica is a copy of both the data and the serving capacity

Spanner overcomes many of these issues by throwing more resources at the problem


6.3.3. Databases
Tables have a schema, which looks a lot like those of any other relational database. Tables have columns, which have types (such as INT64) and modifiers (such as NOT NULL)

adding data that doesn’t match the type defined in the schema results in an error

MySQL


CREATE TABLE employees (

  id INT NOT NULL AUTO_INCREMENT PRIMARY_KEY,

  name VARCHAR(100) NOT NULL,
  start_date DATE
);

Cloud Spammer

CREATE TABLE employees (
  employee_id INT64 NOT NULL,
  name STRING(100) NOT NULL,
  start_date DATE
) PRIMARY KEY (employee_id);


differences in data type names and location of the primary key directive.

6.4. Interacting with Cloud Spanner

 create some of the infrastructural resources.

enabling the Cloud Spanner API






Test Instance

choose a configuration geographically near you

have the VMs accessing Spanner in the same region as the instance itself.

leave the number of nodes set to one.


 you have to create a new database








 choose a name for the database

create some tables for your database.
6.4.2. Creating a table
create a simple employee information

two fields  a unique ID (primary key) for the employee, and the employee’s name.


CREATE TABLE employees (

    employee_id INT64 NOT NULL,
    name STRING(100) NOT NULL,
    start_date DATE
) PRIMARY KEY (employee_id);







Click Create, a page opens where you can see the details of your table, such as the schema



 created an instance, a database belonging to the instance, and a table belonging to the database


6.4.3. Adding data

differences between Spanner and other relational databases is the way you modify data

MySQL, you use an INSERT SQL query to add new data and an UPDATE SQL query to update

write to Cloud Spanner via a separate API, which is more similar to a nonrelational key-value system

demonstrate, use the @google-cloud/spanner Node.js

npm install @google-cloud/spanner@0.7.0




const spanner = require('@google-cloud/spanner')({

  projectId: 'your-project-id'
});

const instance = spanner.instance('test-instance');
const database = instance.database('test-database');
const employees = database.table('employees');

employees.insert([
  {employee_id: 1, name: 'Steve Jobs', start_date: '1976-04-01'},
  {employee_id: 2, name: 'Bill Gates', start_date: '1975-04-04'},
  {employee_id: 3, name: 'Larry Page', start_date: '1998-09-04'}
]).then((data) => {
  console.log('Saved data!', data);
});


6.4.4. Querying data

use Spanner’s Read API to query a single table.

execute a SQL query on the database

Start by using the Read API, by calling table.read() in the Node.js


const spanner = require('@google-cloud/spanner')({
  projectId: 'your-project-id'
});
const instance = spanner.instance('test-instance');
const database = instance.database('test-database');
const employees = database.table('employees');
const query = {
  columns: ['employee_id', 'name', 'start_date'],
  keys: ['1']
};

employees.read(query).then((data) => {
  const rows = data[0];
  rows.forEach((row) => {
    console.log('Found row:');
    row.forEach((column) => {
      console.log(' - ' + column.name + ': ' + column.value);
    });
  });
});

Found row:
 - employee_id: 1
 - name: Steve Jobs
 - start_date: Wed Mar 31 1976 19:00:00 GMT-0500 (EST)


you can use a special all flag on the query

const spanner = require('@google-cloud/spanner')({
  projectId: 'your-project-id'
});

const instance = spanner.instance('test-instance');
const database = instance.database('test-database');
const employees = database.table('employees');
const query = {
  columns: ['employee_id', 'name', 'start_date'],
  keySet: {all: true}
};

employees.read(query).then((data) => {
  const rows = data[0];
  rows.forEach((row) => {
    console.log('Found row:');
    row.forEach((column) => {
      console.log(' - ' + column.name + ': ' + column.value);
    });
  });
});

Found row:
 - employee_id: 1
 - name: Steve Jobs
 - start_date: Wed Mar 31 1976 19:00:00 GMT-0500 (EST)
Found row:
 - employee_id: 2
 - name: Bill Gates
 - start_date: Thu Apr 03 1975 20:00:00 GMT-0400 (EDT)
Found row:
 - employee_id: 3
 - name: Larry Page
 - start_date: Thu Sep 03 1998 20:00:00 GMT-0400 (EDT)


generic SQL-querying

you query a database rather than a specific table because the query might involve other tables (for instance, if you JOIN two tables together).

you send a string containing your SQL query.

Start by sending a simple query to retrieve all of the employees with a SQL query

const spanner = require('@google-cloud/spanner')({
  projectId: 'your-project-id'
});

const instance = spanner.instance('test-instance');
const database = instance.database('test-database');
const query = 'SELECT employee_id, name, start_date FROM employees';

database.run(query).then((data) => {
  const rows = data[0];
  rows.forEach((row) => {
    console.log('Found row:');
    row.forEach((column) => {
      console.log(' - ' + column.name + ': ' + column.value);
    });
  });
});

Found row:
 - employee_id: 1
 - name: Steve Jobs
 - start_date: Wed Mar 31 1976 19:00:00 GMT-0500 (EST)
Found row:
 - employee_id: 2
 - name: Bill Gates
 - start_date: Thu Apr 03 1975 20:00:00 GMT-0400 (EDT)
Found row:
 - employee_id: 3
 - name: Larry Page
 - start_date: Thu Sep 03 1998 20:00:00 GMT-0400 (EDT)


Now, filter this down to only Bill Gates. To do that, you need to add a WHERE clause in your SQL statement

const spanner = require('@google-cloud/spanner')({
  projectId: 'your-project-id'
});

const database = spanner.instance('test-instance').database('test-database');
const query = {
  sql: 'SELECT employee_id, name, start_date FROM employees
 WHERE employee_id = @id',
  params: {
    id: 2
  }
};

database.run(query).then((data) => {
  const rows = data[0];
  rows.forEach((row) => {
    console.log('Found row:');
    row.forEach((column) => {
      console.log(' - ' + column.name + ': ' + column.value);
    });
  });
});

Found row:
 - employee_id: 2
 - name: Bill Gates
 - start_date: Thu Apr 03 1975 20:00:00 GMT-0400 (EDT)

6.4.5. Altering database schema

change the structure of the data that you store.

Spanner supports schema alterations

 the most basic change to a database is adding a new table.

this type of operation (CREATE TABLE) works as you’d expect

deleting entire tables (DROP TABLE) works as expected

the new column can’t be a primary key.

the new column can’t have a NOT NULL requirement

you may already have data in the table, and those existing rows clearly don’t have a value for the new column and need to be set to NULL.

Columns themselves can also be modified

Change the type of a column from STRING to BYTES (or BYTES to STRING).
Change the size of a BYTES or STRING column, so long as it’s not a primary key column.
Add or remove the NOT NULL requirement on a column.
increase the length of a string column, so take your employees table and increase the length of the name column from 100 characters to the maximum supported

ALTER TABLE employees ALTER COLUMN name STRING(MAX) NOT NULL;


$ gcloud spanner databases ddl update test-database \
  --instance=test-instance \
  --ddl="ALTER TABLE employees ALTER COLUMN name STRING(MAX) NOT NULL"
DDL updating...done.


6.5. Advanced concepts

 blends a traditional relational database with a large-scale distributed storage system

6.5.1. Interleaved tables

typical relational database, such as MySQL, the data itself is flat.

Additional relational aspects, which are sometimes explained as relationships between tables themselves, with one table belonging to another.

When you have a large amount of data or a large number of requests for data, sometimes a single server can’t handle it.

create read replicas, which duplicate data and act as alternative servers to query for the data.


systems that have heavy read load and relatively light write load




common solution is to shard the data across multiple machines

Chop up the data into distinct pieces and delegate responsibility for different chunks to different machines

data for employees with names in the range A through L on one server and M through Z on another server.




make sure that, for example, paycheck information, insurance enrollment, and other employee data in different tables are similarly chopped up. '

Splitting up the data is easy for Spanner to do. In fact, Bigtable has supported this capability for quite some time.

You use interleaving tables to tell Spanner which data should live near and move with other data, even if that data is split across multiple tables.


it’s possible that the paycheck information for a user (say, Nicole) would end up on one machine, but her employee record would end up elsewhere.


In Spanner, you can fix this by interleaving the two tables together. Where you want to convey that an employee and their corresponding paychecks should be located near each other and move around together

6.5.2. Primary keys

it’s good practice to give each row what’s called a primary key.

 The value has a uniqueness constraint

 duplicate values aren’t permitted

CREATE TABLE employees (
  employee_id INT64        NOT NULL,
  name        STRING(1024) NOT NULL,
  start_date  DATE         NOT NULL
) PRIMARY KEY(employee_id);

CREATE TABLE paychecks (
  employee_id    INT64 NOT NULL,
  paycheck_id    INT64 NOT NULL,
  effective_date DATE  NOT NULL,
  amount_cents   INT64 NOT NULL
) PRIMARY KEY(employee_id, paycheck_id),
  INTERLEAVE IN PARENT employees ON DELETE CASCADE;

two tables: employees and paychecks. Each employee has an ID and a name, whereas each paycheck has an ID, a pointer to the employee (the employee’s ID), a date, and an amount. This should feel familiar, but there are two important things to notice:
Primary keys can be defined as a combination of two IDs (e.g., employee_id and paycheck_id).
When interleaving tables, the parent’s primary key must be the start of the child’s primary key (for instance, the paychecks primary key must start with the employee_id field) or you’ll get an error.

6.5.3. Split points

split points are the exact positions at which data in a table might be split into separate chunks and potentially handed off to another machine to cope with request load or data size

By using a compound primary key in the paychecks table, you’ve said that all paychecks of each employee should be kept alongside the record for the parent employee.

points that you haven’t specifically prohibited, which lie between two rows in a root table, are called split points.

. Recall that a root table is a table without a parent, which in this case is your employees table. Split points occur between every two different primary keys belonging to the root table,





Notice that all records with the same employee ID at the start of the primary key will be kept together

6.5.4. Choosing primary keys

MySQL offers a way to specify that fields should be automatically incremented

Spanner keeps all of the data in the database sorted lexicographically by primary key, keeping sequential data together.

 powerful because you can distribute your writes evenly across the key space



CREATE TABLE events (
  event_time TIMESTAMP  NOT NULL,
  event_type STRING(64) NOT NULL
) PRIMARY KEY(event_time);

millions of sensors broadcasting events and the total request rate was one write every microsecond (that’s 60,000 writes per second).


Spanner picks a split point (in this case, between any two events because this is a root table) and chops the data in half.

when writing new data, you should choose keys that are evenly distributed and never choose keys that are counting or incrementing (such as A, B, C, D, or 1, 2, 3).

6.5.5. Secondary indexes

indexes are an important performance tool for a database.

Indexes tell your database to maintain some alternative ordering of data in addition to the data already stored in the database

have data sorted by a column that you intend to filter on (for example, WHERE name = "Joe Gagliardi"), the search on that column can be done much more quickly

Although indexes can make queries of your data run more quickly

indexes also need to be updated and maintained


6.5.6. Transactions

idea of a transaction

ACID. Databases that support ACID transactional semantics are said to have atomicity (either all the changes happen or none of them do), consistency (when the transaction finishes, everyone gets the same results), isolation (when you read a chunk of data, you’re sure that it didn’t change out from under you), and durability (when the transaction finishes, the changes are truly saved and not lost if a server crashes).

Spanner supports ACID transactional semantics

Spanner supports two types of transactions: read-only and read-write.

Read-only transactions
make several reads of data in your Spanner database at a specific point in time

ith read-only transactions, you can be sure that the data hasn’t changed because you’re always reading data at a specific point in time.

 read-only transaction doesn’t hold any locks on your data and, therefore, doesn’t block any other changes that might be happening

const spanner = require('@google-cloud/spanner')({
  projectId: 'your-project-id'
});
const instance = spanner.instance('test-instance');
const database = instance.database('test-database', {max: 2});

const printRowCounts = (database, txn) => {
  const query = 'SELECT * FROM employees';
  return Promise.all([database.run(query), txn.run(query)]).then((results) => {
    const inside = results[1][0], outside = results[0][0];
    console.log('Inside transaction row count:', inside.length);
    console.log('Outside transaction row count:', outside.length);
  });
}

database.runTransaction({readOnly: true}, (err, txn) => {
  printRowCounts(database, txn).then(() => {
    const table = database.table('employees');
    return table.insert({
      employee_id: 40,
      name: 'Steve Ross',
      start_date: '1996-01-23'
    });
  }).then(() => {
    console.log('  --- Added a new row! ---');
  }).then(() => {
    printRowCounts(database, txn);
  });
});


Read-write transactions

read-write transactions are transactions that both read and modify data stored in Spanner

hat prevent you from doing things like losing an ATM deposit by operating on data that changed

Imagine you found a mistake in employee 40’s paycheck

1.  Read the amount of the paycheck.
2.  Update the amount of the paycheck to amount + $100.
you need to lock certain areas of the data to tell other jobs, “Don’t mess with this—I’m using it.”
Read-write transactions provide a way of locking exactly what you need, even when there’s a close overlap of data.



6.6. Understanding pricing

pricing has three different components: computing power, data storage, and network cost.

Cloud Spanner is billed by the total number of nodes created and priced on an hourly basis

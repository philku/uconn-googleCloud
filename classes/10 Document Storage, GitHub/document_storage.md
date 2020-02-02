# Document Storage

## Overview

## Lab

## Resources

https://googleclouduconn.blogspot.com/p/chapter-5.html

Chapter 5 Document Storage
This chapter covers
What’s document storage?
What’s Cloud Datastore?
Interacting with Cloud Datastore
Deciding whether Cloud Datastore is a good fit
Key distinctions between hosted and managed services

Document storage is a form of nonrelational storage that happens to be different conceptually from the relational databases

document database thinks in terms of collections and documents.

arbitrary sets of key-value pairs

must have in common is the document type

document database, you might have an Employees collection, which might contain two documents:


{"id": 1, "name": "James Bond"}

{"id": 2, "name": "Ian Fleming", "favoriteColor": "blue"}



 traditional table of similar data



able 5.1. Grid of employee records
ID
Name
Favorite color
1	"James Bond"	Null
2	"Ian Fleming"	"blue"



Table 5.2. Jagged collection of employees
Key
Data
1	{id: 1, name: "James Bond"}
2	{id: 2, name: "Ian Fleming", favoriteColor: "blue"}



SELECT * FROM Employees WHERE favoriteColor != "blue"

 in some document storage systems the answer to this query is an empty set

only documents considered are those that explicitly have a key called favoriteColor.

systems were designed with a focus on large-scale storage.

that all queries were consistently fast, the designers had to trade away advanced features like joining related data

things like lookups by a single key and simple scans through the data, but nowhere near as full-featured as a traditional SQL database.

5.1. What’s Cloud Datastore?

first launched as the default way to store data in Google App Engine

 stand-alone storage system as part of Google Cloud Platform.

designed to handle large-scale data

5.1.1. Design goals for Cloud Datastore

large-scale storage system makes for a great example: Gmail

Data locality
mail database would need to store all email for all accounts, you wouldn’t need to search across multiple accounts

concept of where to put data is called data locality.

Datastore is designed in a way that allows you to choose which documents live near other documents by putting them in the same entity group.

Result-set query scale
be frustrating if your inbox got slower as you receive more email.

index emails as they arrive so that when you want to search your inbox,

would be proportional only to the number of matching emails (not the total number of emails).

This idea of making queries as expensive, with regards to time, as the number of results is sometimes referred to as scaling with the size of the result set

Datastore uses indexing to accomplish this,

 query has 10 matches, it’ll take the same amount of time regardless of whether you have 1 GB or 1 PB of email data.

Automatic replication
 the fact that sometimes servers die, disks fail, and networks go down.

 email data in lots of places so it’s always available

data written should be replicated automatically to many physical servers.

 email is never on a single computer with a single hard drive. Instead, each email is distributed across lots of places.

 Google’s underlying storage systems are well suited to this requirement, and Cloud Datastore takes care of it.

5.1.2. Concepts

 how they fit together

Keys
the idea of a key, which is what Cloud Datastore uses to represent a unique identifier for anything that has been stored

 relational database world is the unique ID you often see as the first column in tables, but Datastore keys have two major differences from table IDs.

Datastore doesn’t have an identical concept of tables

Datastore’s keys contain both the type of the data and the data’s unique identifier

employee data in MySQL, the typical pattern is to create a table called employees and have a column in that table called id that’s a unique integer

rather than creating a table and then inserting a row, it happens all in one step: you insert some data where the key is Employee:1. The type of the data here (Employee) is referred to as the kind.

 two keys have the same parent, they’re in the same entity group

 parent keys are how you tell Datastore to put data near other data. (Give them the same parent!)

you might want to nest sub entities inside each other

keys can refer to multiple kinds in their path or the hierarchy, and the kind (type) of the data is the kind of the bottom-most piece.


Hierarchy

store your employee records as children of the company they work for, which could be Company:1:Employee:2

The kind of this key is Employee

the parent key is Company:1 (whose kind is Company)

key refers to employee #2, and because of its parent (Company:1

stored near all other employees of the same company; for example, Company:1:Employee:44 will be nearby.

can specify keys as strings, such as Company:1:Employee:jbond or Company:apple.com:Employee:stevejobs.

Entities
 primary storage concept in Cloud Datastore is an entity
an entity is nothing more than a collection of properties and values combined with a unique identifier called a key


n entity can have properties of all the basics, also known as primitives, such as

Booleans (true or false)
Strings (“James Bond”)
Integers (14)
Floating-point numbers (3.4)
Dates or times (2013-05-14T00:01:00.234Z)
Binary data (0x0401)



{
  "__key__": "Company:apple.com:Employee:jonyive",
  "name": "Jony Ive",
  "likesDesign": true,
  "pets": 3
}

Datastore exposes some more advanced types
Lists, which allow you to have a list of strings
Keys, which point to other entities
Embedded entities, which act as subentities
{
  "__key__": "Company:apple.com:Employee:jonyive",
  "manager": "Company:apple.com:Employee:stevejobs",
  "groups": ["design", "executives"],
  "team": {
    "name": "Design Executives",
    "email": "design@apple.com"
  }
}
reference to another key is as close as you can get to the concept of foreign keys

In the context of relational databases, a foreign key is a field (or collection of fields) in one table that uniquely identifies a row of another table or the same table.

no way to enforce that a reference is valid, so you have to keep references up to date; for example, if you delete the key, update the reference.

Lists of values typically aren’t supported in relational databases

In Datastore, if that structured data doesn’t need its own row in a table, you can embed that data directly inside another entity using embedded entities

 put the contents of the function inline rather than naming them as a function and calling them by name.



Operations



things you can do to an entity. The basic operations are
get—Retrieve an entity by its key.
put—Save or update an entity by its key.
delete—Delete an entity by its key.
 all of these operations require the key for the entity

omit the ID portion of the key in a put operation, Datastore will generate one automatically for you.

Indexes and queries
using GQL (a query language much like SQL)

 Datastore uses indexes to make a query possible (table 5.3).

Table 5.3. Queries and indexes, relational vs Datastore
Feature
Relational
Datastore
Query	SQL, with joins	GQL, no joins; certain queries impossible
Index	Makes queries faster	Makes advanced query possible


creating an index that stays up to date whenever information changes and that you can scan through to find matching emails.

index is nothing more than a specially ordered and maintained data set to make querying fast.


Table 5.4. An index over the sender field

Sender
Key
eric@google.com	GmailAccount:me@gmail.com:Email:8495
steve@apple.com	GmailAccount:me@gmail.com:Email:2441


index pulls out the sender field from emails and allows you to query over all emails with a certain sender value.

when the query finishes, all matching results have been found.

5.1.3. Consistency and replication

 distributed storage system for something like Gmail needs to meet two key requirements: to be always available and to scale with the result set.

One protocol that Cloud Datastore happens to use involves something called a two-phase commit.

 you break the changes you want saved into two phases: a preparation phase and a commit phase.

preparation phase, you send a request to a set of replicas, describing a change and asking the replicas to get ready to apply it.

 confirm that they’ve prepared the change, you send a second request instructing all replicas to apply that change.

this second (commit) phase is done asynchronously, where some of those changes may hang around in the prepared but not yet applied state.

arrangement leads to eventual consistency when running broad queries where the entity or the index entry may be out of date.

first push a replica to execute any pending commits of the resource and then run the query, resulting in a strongly consistent result.

maintaining entities and indexes in a distributed system is a much more complicated task

 Datastore would have two options:

Update the entity and the indexes everywhere synchronously, confirming the operation will take an unreasonably long time

Update the entity itself and the indexes in the background, keeping request latency much lower

Datastore chose to update data asynchronously to make sure that no matter how many indexes you add, the time it takes to save an entity is the same

Create or update the entity.
Determine which indexes need to change as well.
Tell the replicas to prepare for the change.
Ask the replicas to apply the change when they can.

Ensure all pending changes to the affected entity group are applied.
Execute the query.


Datastore uses these indexes to make sure your query runs in time that’s proportional to the number of matching results found.

Send the query to Datastore.
Search the indexes for matching keys.
For each matching result, get the entity by its key in an eventually consistent way.
Return the matching entities.



The indexes are updated in the background, so there’s no real guarantee regarding when the indexes will be updated.

 eventual consistency, which means that eventually your indexes will be up to date (consistent) with the data you have stored in your entities.

Listing 5.1. Example Employee entity
{
  "__key__": "Employee:1",
  "name": "James Bond",
  "favoriteColor": "blue"
}

SELECT * FROM Employee WHERE favoriteColor = "blue"

If the indexes haven’t been updated yet (they will eventually), you won’t get this employee back in the result.
ask specifically for the entity
get(Key(Employee, 1))
eventually consistent, specifically because the indexes that Datastore uses to find those entities are updated in the background.

change this employee’s favorite color

Listing 5.2. Employee entity with a different favorite color

{
  "__key__": "Employee:1",
  "name": "James Bond",
  "favoriteColor": "red"
}

you may see different results
Table 5.6. Summary of the different possible results


Entity updates


Index updated


Employee matches


Favorite color

Yes	Yes	No	Doesn’t matter
No	Yes	No	Doesn’t matter
Yes	No	Yes	red
No	No	Yes	blue

you may see different results,
 three possibilities are

The employee won’t be in the results.
The query still sees the employee as matching the query (favoriteColor = blue), and correctly so, so it ends up in the results.
The query still sees the employee as matching the query (favoriteColor = blue), so it ends up in the results, even though the entity doesn’t actually match! (favoriteColor = red)




combining querying with data locality to get strong consistency.




5.1.4. Consistency with data locality



 data locality as a tool for putting many pieces of data near each other




eventual consistency (that your queries run over indexes rather than your data, and those indexes are eventually updated in the background)




entity group, defined by keys sharing the same parent key




 query over a bunch of entities that all have the same parent key, your query will be strongly consistent.




Telling Datastore where you want to query over in terms of the locality gives it a specific range of keys to consider.




queries inside a single entity group are strongly consistent (not eventually consistent)



combining querying with data locality to get strong consistency.
query over a bunch of entities that all have the same parent key, your query will be strongly consistent.
where you want to query over in terms of the locality gives it a specific range of keys to consider.

pending operations in that range of keys are fully committed prior to executing the query


all Apple employees who have blue as their favorite color

 knows exactly which keys could be in the result set

first make sure no operations involving those keys are pending

{
  "__key__": "Company:apple.com:Employee:jonyive",
  "name": "Jony Ive",
  "favoriteColor": "blue"

}

update to  red

{
  "__key__": "Company:apple.com:Employee:jonyive",
  "name": "Jony Ive",
  "favoriteColor": "red"

}

query over all Apple employee

SELECT * FROM Employees WHERE favoriteColor = "blue" AND
    __key__ HAS ANCESTOR Key(Company, 'apple.com')

 query is limited to a single entity group

 results will always be consistent with the data,

single entity group can only handle a certain number of requests simultaneously

trading off eventual consistency and getting pretty low throughput overall in return. I


5.2. Interacting with Cloud Datastore

 enable it in the Cloud Console.





Jump to Datastore UI







go back to the To-Do List example

start by creating the TodoList entity.

you’ll first create some data

rather than defining a schema

typical for nonrelational storage

blue Create Entity button




 leave your entity in the [default] namespace

make it a TodoList kind

Datastore automatically assign a numerical ID.

give your TodoList entity a name.

Add Property button

name of the property to name

property type set to String

fill in the value of the propertycalled Groceries

leave the property indexed (marked by the check box).





Click Create, and you should see a newly created TodoList entity



Listing 5.5. Querying Cloud Datastore for all TodoList entities




Go to cloud shell and install datastore
$ npm install @google-cloud/datastore@0.4.0
pico tlist.js




cut and paste code into screen


1


2


3


4


5


6


7


8


9


10


11


12


13


14

const datastore = require('@google-cloud/datastore')({
  projectId: 'your-project-id'
});

const query = datastore.createQuery('TodoList');

datastore.runQuery(query)
  .on('error', console.error)
  .on('data', (entity) => {
    console.log('Found TodoList:\n', entity);
  })
  .on('end', () => {
    console.log('No more TodoLists');
  });
gcloud auth application-default login

Found TodoList:
{ key:
   Key {
     namespace: undefined,
     id: 5629499534213120,
     kind: 'TodoList',
     path: [Getter] },
  data: { name: 'Groceries' } }
No more TodoLists
copy

grocery list is returned with the name you stored.

creating a TodoItem using the hierarchical key structure I described earlier.

 your grocery list items will have keys that use the list as their parent.

const datastore = require('@google-cloud/datastore')({
  projectId: 'your-project-id'
});

const entity = {
  key: datastore.key(['TodoList', 5629499534213120, 'TodoItem']),
  data: {
    name: 'Milk',
    completed: false
  }
};

datastore.save(entity, (err) => {
  if (err) {
    console.log('There was an error...', err);
  } else {
    console.log('Saved entity:', entity);
  }
});

output that looks something like the following:

Saved entity: { key:
   Key {
     namespace: undefined,
     kind: 'TodoItem',
     parent:
      Key {
        namespace: undefined,
        id: 5629499534213120,
        kind: 'TodoList',
        path: [Getter] },
     path: [Getter],
     id: 5629499534213120 },

  data: { name: 'Milk', completed: false } }

the key property, which has a parent key pointing to your TodoList entity.

key has an automatically generated ID for you to reference later.

add a few more items to the grocery list with a script

const itemNames = ['Eggs', 'Chips', 'Dip', 'Celery', 'Beer'];
const entities = itemNames.map((name) => {
  return {
    key: datastore.key(['TodoList', 5629499534213120, 'TodoItem']),
    data: {
      name: name,
      completed: false
    }
  };
});

datastore.save(entities, (err) => {
  if (err) {
    console.log('There was an error...', err);
  } else {
    entities.forEach((entity) => {
      console.log('Created entity', entity.data.name, 'as ID',
     entity.key.id);
    })
  }
});



see that your entities were created

Created entity Eggs as ID 5707702298738688
Created entity Chips as ID 5144752345317376
Created entity Dip as ID 6270652252160000
Created entity Celery as ID 4863277368606720

Created entity Beer as ID 5989177275449344

 back to the Cloud Console and query for all of the items in your grocery list.

SELECT * FROM TodoItem

  WHERE __key__ HAS ANCESTOR Key(TodoList, 5629499534213120)

run this query using the GQL tool in the Cloud Console,

heck one of these items off the list, and then see if you can ask for only the uncompleted ones.

5.3. Backup and restore

Cloud Datastore backups are a bit unusual in that they’re not exactly backups in the sense that you’ve gotten used to them

Datastore’s backup ability is more of an export that’s able to take a bunch of data from a regular Datastore query and ship it off to a Cloud Storage bucket.


data exported to Cloud Storage could be equally inconsistent.

exports are not a snapshot taken at a single point in time. Instead, they’re more like a long-exposure photograph of your data.

disable Datastore writes beforehand and then re-enable them once the export completes.

need a Cloud Storage bucket

 place that’ll hold your exported data,

$ gsutil mb -l US gs://my-data-export

Creating gs://my-data-export/...

 disable writes to your Datastore instance via the Cloud Console, using the Admin tab in the Datastore section




$ gcloud beta datastore export gs://my-data-export/export-1
Waiting for [projects/your-project-id-here/operations/
     ASA1MTIwNzE4OTIJGnRsdWFmZWQHEmxhcnRuZWNzdS1zYm9qLW5pbWRhFAosEg] to
     finish...done.
metadata:
  '@type':
     type.googleapis.com/google.datastore.admin.v1beta1.ExportEntitiesMetadata
  common:
    operationType: EXPORT_ENTITIES
    startTime: '2018-01-16T14:26:02.626380Z'
    state: PROCESSING
  outputUrlPrefix: gs://my-data-export/export-1
name: projects/your-project-id-here/operations/

     ASA1MTIwNzE4OTIJGnRsdWFmZWQHEmxhcnRuZWNzdS1zYm9qLW5pbWRhFAosEg

verify that the data arrived in your bucket, again using the gsutil tool,

$ gsutil du -sh gs://my-data-export/export-1
32.2 KiB    gs://my-data-export/export-1



restoring.

restoring is more like importing,

use all the same IDs as before

will overwrite any entities that use those IDs.

any entities that you created after the previous export (and therefore that are unaffected by the import) will still remain.
The import can edit and create entities, but will never delete any entities.


disable writes ahead of time

 point to the metadata file that was created during the export.
$ gsutil ls gs://my-data-export/export-1
gs://my-data-export/export-1/export-1.overall_export_metadata
gs://my-data-export/export-1/all_namespaces/



trigger an import job using the gcloud command similar to before, as follows.

$ gcloud beta datastore import gs://my-data-export/export-1/export-
     1.overall_export_metadata
Waiting for [projects/your-project-id-here/operations/
     AiA4NjUwODEzOTIJGnRsdWFmZWQHEmxhcnRuZWNzdS1zYm9qLW5pbWRhFAosEg] to
     finish...done.
metadata:
  '@type': type.googleapis.com/google.datastore.admin.v1beta1.ImportEntitiesMetadata
  common:
    operationType: IMPORT_ENTITIES
    startTime: '2018-01-16T16:26:17.964040Z'
    state: PROCESSING
  inputUrl: gs://my-data-export/export-1/export-1.overall_export_metadata
name: projects/your-project-id-here/operations/

AiA4NjUwODEzOTIJGnRsdWFmZWQHEmxhcnRuZWNzdS1zYm9qLW5pbWRhFAosEg


those entities would be reverted to how they were at the time of the export.


5.4. Understanding pricing
Google determines Cloud Datastore prices based on two things: the amount of data you store and the number of operations you perform on that data. Let’s look at the easy part first: storage.
5.4.1. Storage costs
measured in GB, costing $0.18 per GB per month


total storage size for billing purposes of a single entity includes the kind name (for example, Person), the key name (or ID), all property names (for example, favoriteColor), and 16 extra overhead bytes.


properties have simple indexes created, where each index entry includes the kind name, the key name, the property name, the property value, and 32 extra overhead bytes.

ng names (indexes, properties, and keys) tend to explode in size, so you’ll have far more total data than the actual data stored.

5.4.2. Per-operation costs

any requests that you send to Cloud Datastore,

able 5.7. Operation pricing breakdown
Operation type
Cost per 100,000 entities
Read	$0.06
Write	$0.18
Delete	$0.02

etrieve 100,000 of your entities, your bill will be 6 cents

5.5. When should I use Cloud Datastore?




two places where Datastore shines are durability and throughput,

5.5.1. Structure
Cloud Datastore excels at managing semistructured data where attributes have types

no single schema across all entities (or documents) of the same kind

Datastore also allows you to express the locality of your data using hierarchical keys

(where one key is prefixed with the key of its parent).

segment data between units of isolation

desire to segment data between units of isolation

enables automatic replication of your data, is what allows it to be so highly available as a storage system.

queries across all the data will be eventually consistent.


5.5.2. Query complexity

 nonrelational storage system

Cloud Datastore doesn’t support the typical relational aspects (for example, the JOIN operator).

allow you to store keys that act as pointers to other stored entities,

 no referential integrity and no ability to cascade or limit changes involving referenced entities.

Referential integrity refers to the accuracy and consistency of data within a relationship. In relationships, data is linked between two or more tables . This is achieved by having the foreign key (in the associated table) reference a primary key value (in the primary – or parent – table).

delete an entity in Cloud Datastore, anywhere you pointed to that entity from elsewhere becomes an invalid reference.

certain queries require that you have indexes to enable them

limitations are the consequence of the structural requirements that went into designing Cloud Datastore, whereas other limitations enable consistent performance for all queries.

5.5.3. Durability

 Because Megastore was built on the premise that you can never lose data, everything is automatically replicated and not considered saved until saved in several places.

Datastore handles this entirely on its own, meaning that the only setting for durability is as high as possible.

global queries being only eventually consistent.

data needs to replicate to several places before being called saved

5.5.4. Speed (latency)
Compared to many in-memory storage systems
Cloud Datastore won’t be as fast for the simple reason that even SSDs are slower than RAM

 relational database system like PostgreSQL or MySQL, Cloud Datastore will be in the same ballpark

as your SQL database gets larger or receives more requests at the same time, it’ll likely get slower.

 Cloud Datastore’s latency stays the same regardless of the level of concurrency

Cloud Datastore certainly won’t be blazing fast like in-memory NoSQL storage systems, but it’ll be on par with other relational databases

5.5.5. Throughput

accommodate as much traffic as you care to throw at it.

the pessimistic locking that comes with relational databases like MySQL doesn’t apply

 able to scale up to many concurrent write operations.

adding more servers on Google’s side to keep up

5.5.6. Cost

storing a few gigabytes, your total cost of storage and querying could be around $50 a month

 add more and more data, and query that data more and more frequently, overall costs can skyrocket—primarily because of indexes.

 benefit of never worrying that your data will be unavailable

5.5.7. Overall

To-Do ListTable 5.8. To-Do List application storage needs
Aspect
Needs
Good fit?
Structure	Structure is fine, not necessary though.	Sure
Query complexity	We don’t have that many fancy queries.	Definitely
Durability	High—We don’t want to lose stuff.	Definitely
Speed	Not a lot.	Definitely
Throughput	Not a lot.	Sure
Cost	Lower is better for toy projects.	Definitely



bit of overkill on the scalability side

To-Do List app could become something enormous, then Datastore is a safe bet to go with because it means that scaling to handle tons of traffic is something you don’t need to worry about too much.

E*Exchange

Table 5.9. E*Exchange storage needs
Aspect
Needs
Good fit?
Structure	Yes, reject anything suspect—No mistakes.	Maybe
Query complexity	Complex—We have fancy questions to answer.	No
Durability	High—We can’t lose stuff.	Definitely
Speed	Things should be relatively fast.	Probably
Throughput	High—Lots of people may be using this.	Definitely
Cost	Lower is better, but willing to pay top dollar.	Definitely



 Datastore is probably not the best fit for E*Exchange if used on its own

 doesn’t enforce strict schema requirements

E*Exchange wants clear validation of any data entering the system.

enforce that schema in your application rather than relying on the database. So although it’s possible to do it, it’s not built into Datastore.

can’t do extremely complex queries,

eventually consistent queries will be challenging to design around for a system that requires highly accurate and up-to-date information like E*Exchange.

InstaSnap

Snapagram

able 5.10. InstaSnap storage needs
Aspect
Needs
Good fit?
Structure	Not really—Structure is pretty flexible.	Definitely
Query complexity	Mostly lookups; no highly complex questions.	Definitely
Durability	Medium—Losing things is inconvenient.	Sure
Speed	Queries must be very fast.	Maybe
Throughput	Very high—Kim Kardashian uses this.	Definitely
Cost	Lower is better, but willing to pay top dollar.	Definitely

InstaSnap is the single-query latency - needs to be extremely fast.

in conjunction with some sort of in-memory cache

nstaSnap is a pretty solid fit because of the relatively simple queries combined with the enormous throughput requirements. As a matter of fact, SnapChat (the real app) uses Datastore as one of its primary storage systems.

5.5.8. Other document storage systems

Table 5.11. Brief comparison of document storage systems
Name
Cost
Flexibility
Availability
Durability
Speed
Throughput
Cloud Datastore	High	Medium	High	High	Medium	High
MongoDB	Low	High	Medium	Medium	Medium	Medium
DynamoDB	High	Low	Medium	Medium	High	Medium
HBase	Medium	Low	Medium	High	High	High
Cloud Bigtable	Medium	Low	High	High	High	High


s possible to configure systems like HBase and MongoDB for high availability, when that happens, cost will go up

Summary

Document storage keeps data organized as heterogeneous (jagged) documents rather than homogeneous rows in a table.
Using document storage effectively may involve duplicating data for easy access (denormalizing).
Document storage is great for storing data that may grow to huge sizes and experience huge amounts of traffic, but it comes at the cost of not being able to do fancy queries (for example, joins that you do in SQL).
Cloud Datastore is a fully managed storage system with automatic replication, result-set query scale, full transactional semantics, and automatic scaling.
Cloud Datastore is a good fit if you need high scalability and have relatively straightforward queries.
Cloud Datastore charges for operations on entities, meaning the more data you interact with, the more you pay.
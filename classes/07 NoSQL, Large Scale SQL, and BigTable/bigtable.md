# BigTable

## Overview

## Lab

## Resources

https://googleclouduconn.blogspot.com/p/chapter-7.html


Chapter 7 Cloud Bigtable
Cloud Bigtable: large-scale structured data

What is Bigtable? What went into its design?
How to create Bigtable instances and clusters
How to interact with your Bigtable data
When is Bigtable a good fit?
What’s the difference between Bigtable and HBase?
 businesses have become more interested in the history of data changes over time than in a snapshot at a single point.

the cost of storing a single byte has dropped significantly.

 more uses for all of this just-in-case data such as machine learning, pattern recognition, and prediction engines.

 storage systems that can provide fast access to extremely large datasets, while also maintaining the ability to update these datasets continuously

 Google’s Bigtable, first announced in 2006

open source project Apache HBase

 Google launched Cloud Bigtable as a managed cloud service to address the growing need for these large-scale storage systems.

7.1. What is Bigtable?

It was built to solve a specific but complex problem: How do you store and continuously update petabytes of data, with incredibly high throughput, low latency, and high availability?

using a globally sorted key-value map, which automatically rebalances data based on service use to reach the performance and scale requirements needed.

7.1.1. Design goals

primary use case for Bigtable was the web search index

Google’s web search index is one of those things that must be always on and always fast

many of the requirements are related to both performance and scale

Large amounts of (replicated) data

The search index will obviously be enormous, with overall sizes measured in petabytes, which means that it’s far too large for a single server to manage.

 the index would be that it’s distributed across many different servers

 need to ensure that the data itself is stored in more than one place

Low latency, high throughput

the search index clearly sees a ton of traffic, potentially millions of queries every second.

search index starts failing as more and more requests come in at the same time, folks will take their searches elsewhere.

Each search request needs to return a result quickly

 little time to query the database

Rapidly changing data

New web pages are added all the time and the search index will be updated by a web crawler frequently.

the system must handle lots of updates at the same time

updates take too long, they will start to pile up and the index will slip out of date

History of data changes

 data being stored will change rapidly over time

way to easily see the data as it was at a particular point in time

to see history of the data in a row, you’ll need a third dimension: time.




able to ask for the latest value in a row, as well as all the values that this row has had over time.

Strong consistency
anyone querying the index will never see stale data.

Row-level transactions

allow atomic read-modify-write sequences or risk two updates overwriting each other.

t expose a way to return an error if someone else has changed a row’s data while you’re attempting to work on it.



Subset selection
you don’t always want to request all of the data stored for a given set of results

a way of asking for only a specific set of properties

ask for things like only the two most recent values.

Being able to limit the pieces the storage system should return, allows you to store more data in one chunk and request only small bits of that large chunk.


7.1.3. Design overview

unique storage system that did things quite differently from most of the nonrelational systems

Bigtable is a large table of data with some important differences from the tables you’ve come to know

Bigtable is much more like a jagged key-value map than a grid. In fact, the authors of the research paper describing Bigtable called it “a sparse, distributed, persistent, multi-dimensional sorted map”



Bigtable is less like a relational database and a bit more like a big key-value store that distributes data across lots of servers while keeping all the keys in that map sorted.

Bigtable allows you to do both key lookups (as you would in any key-value store) as well as scans over key ranges and key prefixes.


7.2. Concepts

think a bit differently about the structure and access patterns of your data

think ahead about what types of questions you’ll want to ask about your data, because the ability to answer different questions is determined frequently by the way in which you structure that data.

7.2.1. Data model concepts
tables, rows, column families, and column qualifiers.




data model concepts apply most specifically to Cloud Bigtable, they’re also relevant if you use HBase

Row keys
may look a bit like a relational database at a glance, data stored is much more like a key-value store

the key used to find content is called the row key.

choose the format of this key carefully.

the address of a given chunk of data. Bigtable allows you to quickly find data using a row key, but it doesn’t allow you to find data using any secondary indexes

Row key sorting

Row keys are always unique. If you have collisions, you’ll overwrite data.
Row keys are lexicographically sorted across the entire table. High traffic to lots of keys with the same prefix could result in serious performance problems.
Row key prefixes and ranges can be used in queries to make the query more efficient. Poorly structured keys will require inefficient full-table scans of your data.
n choosing a row key is choosing a format that’s both useful to you as the application developer and efficient for Bigtable to store,

examples of row key formats that would be a particularly good fit for Bigtable:

String IDs (hashes)—If your identifier is an opaque ID (such as Person #52), use a hash of that value (such as 'person_' + crc32(52)). The hash ensures that the row key is both a fixed length and evenly distributed (lexicographically) throughout the key space.

Timestamps—It’s often the case that you’ll need to retrieve data based on a point in time, which makes timestamps an obvious choice.

Combined values—Sometimes rows contain information relating two different concepts, for example, a tag of a person in a post involves the person tagged and the person doing the tagging.

Hierarchical structured content—The last format, similar to Java package-naming formats, is to use a reverse hierarchy prefix format.

Columns and column families

 key-value stores, the data that’s pointed at by a particular key is a completely unstructured piece of data.

 could be a bunch of bytes representing an image, or it could be a JSON document storing a user profile

allow you to define certain aspects resembling a schema, which makes it easy to specify which bits of data to retrieve.

keys of this map are called column qualifiers

dynamic pieces of data

Each of these belongs to a single family, which is a grouping that holds these column qualifiers and act much more like a static column in a relational database.

column qualifiers can be anything you want and can be thought of as data—something you’d never do in a relational database. Using this type of structure also means that when you visualize data in Bigtable as a table, most of the cells will be empty, or you call a sparse map.

visualizing your Bigtable data

imagine a to-do list where you’re storing items that have been completed.

able 7.1. Visualizing To-Do List
Row key
Completed
item-1	item-1-notes	item-2	item-2-notes
237121cd (user-3)			true	"Right on time"
4d4aa3c4 (user-1)	true		true	"2 days late!"
946ce0c9 (user-2)

7.2.2. Infrastructure concepts

Cloud Bigtable acts as a managed service

you don’t have to manage individual virtual machines like you would if you were running your own HBase cluster.

 Bigtable is one of the more confusing services, particularly when it comes to how replication is handled.

looking at the hierarchy of concepts that you can manage yourself: instances, clusters, and nodes. See figure 7.5.



basic structure here is that an instance is the top-most concept and can contain many clusters, and each cluster contains several nodes (with a minimum of three).

Instances
instance as the primary resource you refer to when thinking about your Bigtable deployment

when you write data to Bigtable, you’d refer to writing it to a specific Bigtable instance.

Bigtable you send your data to the instance

changes are propagated to all the other clusters

Bigtable should route your queries to the closest cluster

Instances are globally scoped, meaning that they remain addressable regardless of whether a particular zone is experiencing an outage.

Clusters
limited to a single cluster per instance.

grouping for a bunch of nodes, each of which is responsible for handling some subset of queries sent to a Bigtable instance.

Each cluster has a unique name, a location (zone), and some performance settings such as the type of disk storage to use as well as the number of nodes to run.

Each cluster holds a copy of your data,

Nodes
you can think of a cluster as a grouping together of multiple nodes, the nodes themselves are hidden from you in the API. You can communicate only with the particular cluster that’s responsible for routing your request to a particular node.

allows the cluster to ensure that requests are spread evenly across the nodes and also allows the cluster to rebalance data to maintain this even distribution.

Tablets
Tablets are a way of referencing chunks of data that live on a particular node

they can be split, combined, and moved around to other nodes to keep access to data spread evenly across the available capacity.



more tablets accumulate on a single node, the cluster may relocate some of those tablets onto another node to redistribute the data in a more balanced fashion



some tablets are more frequently accessed than others. In figure 7.8, three tablets are responsible for 35% of all the read queries on the entire system.



few hot tablets are colocated on a single node, Bigtable rebalances the cluster by shifting some of the less frequently accessed tablets to other nodes that have more capacity



single tablet could become too hot (it’s being written to or read from far too frequently).




great to understand nodes, tablets, and re-balancing of data, Bigtable itself is a complex storage system, and understanding every nuance is incredibly difficult. In general, the most important thing you can do when using Bigtable is to choose row keys carefully so that they don’t concentrate traffic in a single spot.

7.3. Interacting with Cloud Bigtable
Bigtable has a simple hierarchy involving instances,clusters, and nodes, and the data model for each of these is simple as well, involving tables, rows, column families, and column qualifiers.
# Cloud SQL

## Overview

## Lab

## Resources

https://googleclouduconn.blogspot.com/p/chapter-4.html

Chapter 4 Cloud SQL
What is Cloud SQL?
Configuring a production-grade SQL instance
Deciding whether Cloud SQL is a good fit
Choosing between Cloud SQL and MySQL on a VM

SQL forms of structured data storage

relational database comes from the idea that these databases store related data

great general-purpose storage systems. great general-purpose storage systems. In Google Cloud, this is called Cloud SQL



various flavors of relational databases (such as MySQL or PostgreSQL)

 highlight the things that Cloud SQL does differently.


4.1. What’s Cloud SQL?

Cloud SQL is a VM that’s hosted on Google Compute Engine, managed by Google, running a version of the MySQL binary.

change all of those settings in the Cloud Console

Cloud SQL currently supports both MySQL and PostgreSQL

integrating with Cloud SQL involves nothing more than changing the hostname in your configuration to point at a Cloud SQL instance.


Cloud SQL automates some of the more tedious tasks, like upgrading to a newer version of MySQL, running recurring backups, and securing your Cloud SQL instance so it only accepts connections from people you trust.


4.2. Interacting with Cloud SQL

Cloud Console and the Cloud SDK to turn on a Cloud SQL instance

store your To-Do List data in Cloud SQL and run a few example queries.

be sure to pick a region that’s nearby, so your queries won’t be traveling around the world and back.






Create Instance



Select MySQL




Instance name is todo-list

password set as uconnstamford

region is us-east1

make sure you have access.

 create new users

Cloud Console by clicking on the Cloud SQL instance and choosing the Users tab









Now we can use the cloud shell to check if our instance is running


A Linux shell is provided for every project




john_iacovacci1@cloudshell:~ (uconn-engr
)$  gcloud sql instances list
NAME       DATABASE_VERSION  LOCATION    TIER              PRIMARY_ADDRESS  PRIVATE_ADDRESS  STATUS
todo-list  MYSQL_5_7         us-east1-c  db-n1-standard-1  34.74.144.157    -                RUNNABLE
john_iacovacci1@cloudshell:~ (uconn-engr)$

Now exit shell and go back to Instance Overview




In the Connect to this instance section

Click on

Connect using Cloud Shell







A string will appear in your shell and all you need to do is hit return

gcloud sql connect todo-list --user=root --quiet

once the instance is connected it will prompt you
for the password
Use the password entered when you created the SQL instance

you are now in the MySQL environment

MySQL [(none)]>

create a database for your app,

MySQL [(none)]> CREATE DATABASE todo;
Query OK, 1 row affected (0.00 sec)

create  table for your To-Do Lists
MySQL [(none)]> use todo;
Database changed
MySQL [todo]> create table todolists
(id INT NOT NULL AUTO_INCREMENT,
name VARCHAR(255) NOT NULL,
PRIMARY KEY (id));
Query OK, 0 rows affected (0.02 sec)




Field Attribute NOT NULL is being used because we do not want this field to be NULL. So, if a user will try to create a record with a NULL value, then MySQL will raise an error.
Field Attribute AUTO_INCREMENT tells MySQL to go ahead and add the next available number to the id field.
Keyword PRIMARY KEY is used to define a column as a primary key. You can use multiple columns separated by a comma to define a primary key.

create the example lists





MySQL [todo]> INSERT INTO todolists
(`name`) VALUES ("Groceries"),
("Christmas shopping"),
("Vacation plans");
Query OK, 3 rows affected (0.01 sec)
Records: 3 Duplicates: 0 Warnings: 0

you can use a SELECT query to check if the lists are there, as follows.

SELECT * FROM todolists;

+----+--------------------+
| id | name               |
+----+--------------------+
|  1 | Groceries          |
|  2 | Christmas shopping |
|  3 | Vacation plans     |
+----+--------------------+
3 rows in set (0.00 sec)







Database changed
MySQL [todo]> CREATE TABLE todoitems (id INT(11) NOT NULL
 AUTO_INCREMENT, todolist_id INT(11) NOT NULL REFERENCES
 todolists(id), name varchar(255) NOT NULL, done BOOL
NOT NULL DEFAULT '0', PRIMARY KEY (id));
Query OK, 0 rows affected (0.02 sec)

MySQL [todo]> INSERT INTO todoitems (`todolist_id`,
 `name`, `done`) VALUES (1, "Milk", 0), (1, "Eggs", 0)
, (1, "Orange juice", 1), (1, "Egg salad", 0);
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0


'
MySQL [todo]> select * from todoitems;
+----+-------------+--------------+------+
| id | todolist_id | name         | done |
+----+-------------+--------------+------+
|  1 |           1 | Milk         |    0 |
|  2 |           1 | Eggs         |    0 |
|  3 |           1 | Orange juice |    1 |
|  4 |           1 | Egg salad    |    0 |
+----+-------------+--------------+------+
4 rows in set (0.00 sec)



 Delete in the Cloud Console









4.3. Configuring Cloud SQL for production



run Cloud SQL in a production-grade environment.



the environment that’s safe for you to run a business in.

reliable backups, failover procedures, and proper security practices.

4.3.1. Access control

temporarily ignore security. You might allow open access to a Cloud SQL instance (for example, 0.0.0.0/0 in CIDR notation)

this is not acceptable.

What IP addresses or subnetworks should you allow to connect to an instance?


 assign a static IP to these machines and then specifically limit access to those in the Authorization section when looking at the Cloud SQL instance.

 VM running using the IP address 104.120.19.32, you could allow access from that exact IP using CIDR notation, which would be 104.120.19.32/32







Classless Inter-Domain Routing is a method for allocating IP addresses and IP routing.


you can rely on the pattern of IP addresses and CIDR notation.

your VMs live on a virtual network that assigns IPs from a special subnet for your project

all of your Compute Engine VMs on a single network will have IP addresses following the same pattern, and you can grant access to the pattern rather than each individual IP address.

assigning internal IP addresses (10.240.0.0/16), which means that your machines will all have IPs matching this CIDR expression (for example, 10.240.0.1). To limit access to these machines, you can use 10.240.0.0/16 (where /16 means the last two numerals are wildcards).

4.3.2. Connecting over SSL



SSL (Secure Sockets Layer) is nothing more than a standard way of sending data from point A to point B over an untrusted wire.


Whenever you’re establishing a secure connection as a client, you need three things:
The server’s CA certificate
A client certificate
A client private key
 Cloud Console and jump into the SSL tab










To get the server’s CA certificate, click the aptly named View Server CA Certificate button.

Cloud SQL supports connecting to an instance using the Transport Layer Security (SSL/TLS) protocol

Cloud SQL uses a self-signed, per-instance server certificate and a certificate (public/private key pair)

certificates work together to enable the server (instance) and client (application) to encrypt their communication

Click Allow only SSL connections.

certificate has an expiration date

Cloud SQL provides a way to rotate your server certificate

 Create new certificate

In the Create a client certificate dialog box, give the certificate a name unique for this.





give it a unique name







Download client-key.pem

Download client-cert.pem

Download server-ca.pem

 this point, you have:

A server certificate saved as server-ca.pem.
A client public key certificate saved as client-cert.pem.
A client private key saved as client-key.pem.




4.3.3. Maintenance windows

 the need to upgrade software once in a while

like security patches or upgrades to newer versions

give it some guidance

tell Google when it’s OK to do things like system upgrades, so your customers don’t notice the database disappearing or getting slower in the middle of the day.


Cloud SQL lets you set a specific day of the week and time of the day

Google doesn’t know what your business is.

Maintenance Schedule section






Maintenance Window




picking a day of the week.

pick a single-hour window




4.3.4. Extra MySQL options

MySQL configuration file (my.cnf). In Cloud SQL, you don’t have access to the my.cnf file, but you still can change these parameters—via an API (or via the Cloud Console) rather than a configuration file.

creating large in-memory temporary tables. By default, there’s a limit to how big those tables can be, which is 16 MB.

u get better performance by raising the limit from 16 MB to something more in line with your system, say 256 MB.

 Add Database Flags from the configuration options section




4.4. Scaling up (and down)

 starting out on a small VM type (maybe a single-core VM) and then moving to a larger, more powerful VM later on.

 two things go into determining the performance of your Cloud SQL instance:

Computing power (for example, the VM instance type)
Disk performance (for example, the size of the disk, because size and performance are tied)
4.4.1. Computing power
 Cloud SQL instance details page

change the machine type


Save, you’ll have to restart your database
4.4.2. Storage
 disk size and performance are tied together

A larger disk not only can store more bytes, it provides more IOPS to access those bytes.

IOPS (input/output operations per second) is the standard unit of measurement for the maximum number of reads and writes to non-contiguous storage locations.

running low on disk space

being accessed more frequently and needs more IOPS capacity

make your disk bigger

By default, disks used as part of Cloud SQL have automatic growth enabled. As your disk gets full

Edit Instance page, under the Configuration Options, you should see a section called Configure Machine Type and Storage


you can increase the size of your database, but you can’t decrease it.

4.5. Replication

 fundamental component to designing highly available systems is removing any single points of failure

 running without any service interruptions

 Cloud SQL makes it easy to implement the most basic forms of replication. It does so by providing two different push-button replica types: read replicas and failover replicas.

read replica is a clone of your Cloud SQL instance that follows the primary or master instance, pulling in any changes made to the master




A failover replica is similar to a read replica, except its primary job is to be ready as a replacement primary instance in case of some sort of disaster




To create these replicas, all you have to do is click in the Cloud Console. Start first by creating a failover replica.





idea behind a failover replica is that you’re preparing for some sort of catastrophe.

but it also could be an outage of an entire zone. By creating a failover replica in a different zone than the primary, you can be certain that even if one zone were to fail for whatever reason, your database would be able to continue working with little interruption.

'
4.6. Backup and restore
Cloud SQL does a solid job of making backups simple so that you don’t have to think about them until you need them.

4.6.1. Automated daily backups
The simplest, quickest, and probably most useful backup for Cloud SQL is the automatic one that occurs daily at a time you specify when you create the Cloud SQL instance.

choose a backup window when creating your Cloud SQL instance






cloud SQL will snapshot all of your data to disk every day and keep a copy of that snapshot for seven days on a rolling window

backup itself is a disk-level snapshot, which begins with a special user (cloudsqladmin) sending a FLUSH TABLES WITH READ LOCK query to your instance. This command tells MySQL to write all data to disk and prevents writes to your database while that’s happening. If a backup is in progress



4.6.2. Manual data export to Cloud Storage

 Cloud SQL provides a managed import and export of your data that relies on Google Cloud Storage to store the backup.

 instance details page for your Cloud SQL instance, and click the Export button at the top of the page.



Click the Browse button next to the field for the file path, and at the top of the new dialog that opens up




you can create a new location


walk through how to restore it into your Cloud SQL instance
s.


Import on the instance details page


Importing is nothing more than executing a set of SQL statements against your Cloud SQL instance and allowing you to use Cloud Storage as the source of the input.


4.7. Understanding pricing

Google Cloud considers two basic principles of pricing for computing resources: computing time and storage.

slight markup on CPU time for managing the MySQL binary and configuration for you.

a small Cloud SQL instance would cost about 5¢ per hour, and the top-of-the-line, high-memory, 16-core, 104 GB memory machine would cost about $2 per hour.

 the going price is the same as persistent SSD storage, which is 17¢ per GB per month.

 concept of sustained-use discounts for computing resources

running instances around the clock costs about 30% less than the sticker price.

Table 4.3. Different sizes of Cloud SQL instances and costs
ID
CPU Cores
Memory
Hourly price
Monthly price
Effective hourly price
g1-small	1	1.70 GB	$0.0500	$25.20	$0.0350
n1-standard-1	1	3.75 GB	$0.0965	$48.67	$0.0676
n1-standard-16	16	60 GB	$1.5445	$778.32	$1.0810
n1-highmem-16	16

104 GB	$2.0120	$1,014.05	$1.4084

4.8. When should I use Cloud SQL?



 look at a summary of Cloud SQL using the scorecard






4.8.1. Structure

Most relational databases store highly structured data with a complete schema defined ahead of time that’s strictly enforced.

can prevent data corruption errors that happen when different people make different assumptions about how types are cast from one to the other.

database can optimize your data a bit more because it has more information about both the data that exists currently and the data that’ll be added later.

Cloud SQL scores high on this metric, so if your data is or can easily be fit to a schema, Cloud SQL is definitely a good option.

4.8.2. Query complexity

 SQL is an advanced language that provides some impressive query capabilities.

 if you know you’ll have complex questions to ask of your data, SQL is probably a good fit.

4.8.3. Durability

 it says, “I saved your data to disk,”

 the general consensus is that relational storage systems (like MySQL) are capable of providing a high level of durability.

 stores all the data on Persistent Disk, you benefit from the higher levels of durability and availability that Persistent Disk offers.

4.8.4. Speed (latency)

the latency of a query over your data is a function of the amount of data that your database needs to analyze to come up with your answer.

data grows, your queries may get slower and slower.

as queries start stacking up in your database, future queries will pile up on top of each other, effectively making a long line of people all asking for data and not getting answers.

 have hundreds of gigabytes of data, you may want to consider different storage strategies

4.8.5. Throughput

 he topic of performance, relational storage provides strong locking and consistency guarantees—the data is never stale—but with these guarantees come things like pessimistic locking, where the database tries to prevent lots of people from all writing at the same time, lowering the overall throughput for the database.

 migrating to a different system as your data and concurrency requirements increase beyond what’s reasonably possible with something like MySQL.

4.9. Cost

Cloud SQL’s costs also are on the same level as running any database yourself on Compute Engine (such as your own MySQL instance)

Cloud SQL comes in very low on the cost scale for data sets that are suitable for a MySQL database. F
4.9.1. Overall
Table 4.5. To-Do List application storage needs
Aspect
Needs
Good fit?
Structure	Structure is fine; not necessary, though.	Sure
Query complexity	We don’t have that many fancy queries.	Definitely
Durability	High—We don’t want to lose stuff.	Definitely
Speed	Not a lot.	Definitely
Throughput	Not a lot.	Definitely
Cost	Lower is better for toy projects.	Mostly


E*Exchange

able 4.6. E*Exchange storage needs
Aspect
Needs
Good fit?
Structure	Yes, reject anything suspect; no mistakes.	Definitely
Query complexity	Complex—We have fancy questions to answer.	Definitely
Durability	High—We can’t lose stuff.	Sure
Speed	Things should be pretty fast.	Probably
Throughput	High—Lots of people may be using this.	Maybe
Cost	Lower is better, but willing to pay top dollar.	Definitely

InstaSnap
Table 4.7. InstaSnap storage needs
Aspect
Needs
Good fit?
Structure	Not really—Structure is pretty flexible.	Not really
Query complexity	Mostly lookups; no highly complex questions.	Not really
Durability	Medium—Losing things is inconvenient.	Sure
Speed	Queries must be very fast.	Not really (with lots of data)
Throughput	Very high—Kim Kardashian uses this.	Not really
Cost	Lower is better, but willing to pay top dollar.	Definitely
4.10. Weighing Cloud SQL against a VM running MySQL

just want MySQL and don’t care all that much about customizing their instance

planning to turn on a VM, install MySQL, and change the password, Cloud SQL was made for you.

motivations for shifting toward the cloud was to reduce your overall TCO (total cost of ownership)

In short, Cloud SQL’s focus isn’t to be a better, faster MySQL, it’s to be a simpler, lower-overhead MySQL.

Summary
Relational databases are great for storing data that relates to other data using foreign key references, such as a customer database.
Cloud SQL is MySQL in a box that runs on top of Compute Engine.
When choosing your storage capacity, don’t forget that size is directly related to performance. It’s OK (and expected) to have lots of empty space.
When you have enough Cloud SQL instances to justify hiring a DBA, it might make sense to manage MySQL yourself on Compute Engine instances.
Always configure Cloud SQL to encrypt traffic using an SSL certificate to avoid eavesdropping on the internet.
Don’t worry if you chose too slow of a VM. You can always change the computing power later. You also can increase the storage space, but it’s more work to decrease it if you overshoot.
Use failover replicas if you want your system to be up even when a zone goes down.
Enable daily backups if you want to be sure to never lose data.
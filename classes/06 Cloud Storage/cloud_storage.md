# Cloud Storage

## Overview

## Lab

## Resources

https://googleclouduconn.blogspot.com/p/chapter-8.html

Chapter 8 Cloud Storage
What is object storage?
What is Cloud Storage?
Interacting with Cloud Storage
a globally unique nameDeciding whether Cloud Storage is a good fit
primary design goal of these systems is to reduce complexity of the underlying disks and data centers and instead provide a simple API for uploading and retrieving files

 key-value storage for large values with automatic replication and caching around the world.

object storage tends to be one of the most common and most standardized

two key concepts: buckets and objects.

 bucket as a container that stores your data

a globally unique name

geographical location and the storage class

buckets as “disks,” in the sense that you can choose what type of disk you want (for example, SSD, regular disk, replicated disk

this “disk” is extraordinarily large—there’s no limit to how many bytes can end up in a bucket

each file in the bucket must not be larger than 5 terabytes

bucket itself is replicated and spread across many physical disks to maintain high levels of durability and availability.

Objects are the files that you put inside a bucket

have a unique name inside the bucket, and as on typical file systems, slashes (/) are treated specially so that you can browse directories



Locations
buckets can have locations
 buckets exist either at the regional level  or spread across multiple regions

concerned about latency between your VMs and your data on GCS, you might want to choose a specific region

 multiregional bucket to ensure data is always closest to where you or your customers are

8.2. Storing data in Cloud Storage
first have to create a bucket

bucket names need to be globally unique










Hit create bucket from storage / browser selection






Enter a unique name




Select local region






use standard storage



create bucket



Cloud Storage currently has a separate command-line tool called gsutil

john_iacovacci1@cloudshell:~ (uconn-engr)$ gsutil ls
gs://staging.uconn-engr.appspot.com/
gs://uconn-engr.appspot.com/
gs://uconnstamford/
gs://us.artifacts.uconn-engr.appspot.com/
john_iacovacci1@cloudshell:~ (uconn-engr)$

Now upload a simple text file with gsutil

create a sample file by using linux echo command and std output redirection

 echo "This is my first file!" > my_first_file.txt

 cat my_first_file.txt

john_iacovacci1@cloudshell:~ (uconn-engr)$ cat my_first_file.txt
This is my first file!
john_iacovacci1@cloudshell:~ (uconn-engr)$


 gsutil cp my_first_file.txt gs://uconnstamford/

john_iacovacci1@cloudshell:~ (uconn-engr)$
gsutil cp my_first_file.txt gs://uconnstamford/
Copying file://my_first_file.txt [Content-Type=text/plain]...
/ [1 files][   23.0 B/   23.0 B]
Operation completed over 1 objects/23.0 B.
john_iacovacci1@cloudshell:~ (uconn-engr)$




the file (called an object in this context) made its way into your newly created bucket.

npm install @google-cloud/storage@0.2.0


8.3. Choosing the right storage class
storage classes come with different performance characteristics (both latency and availability), as well as different prices.
8.3.1. Multiregional storage

Multiregional storage is the most commonly used option and the one likely to fit the needs of most applications. The flip side is that it’s also the most expensive of the options available because it replicates data across several regions inside the chosen location.

8.3.2. Regional storage
the regional storage class is like a slimmed-down version of the multiregional storage class. Instead of replicating data across several regions inside an area (for example, “United States”), this class replicates the data across different zones inside a single region

 lower availability, and latency to destinations far away from the region
8.3.3. Nearline storage
match the data archival use case by making a few key trade-offs that you shouldn’t notice if you’re using the data as intended

8.3.4. Coldline storage
 extreme end of the data-archival spectrum

primarily in the case of a serious disaster

 all transaction logs for the past year. That data would be a much better fit for the Coldline storage

where you have data that you rarely need

8.4. Access control

t how to control who’s able to access or modify the data after it’s stored.

8.4.1. Limiting access with ACLs

interacting with your data while authorized as a service account


 when you want to allow others to access your data? How do you restrict who can do what?


everything you create is locked down to be accessible by only those people who have access to your project.

add someone else for other parts of your project, they also will have access to your data in Cloud Storage.



Cloud Storage allows fine-grained access control of your buckets and objects through a security mechanism called Access Control Lists (ACLs).

Table 8.2. Description of roles for Cloud Storage
Role
Meaning (buckets)
Meaning (objects)
Readers	Bucket readers can list the objects in a bucket.	Object readers can download the contents of an object.
Writers	Bucket writers can list, create, overwrite, and delete objects from a bucket.	(This doesn’t apply because you can’t have object writers.)
Owners	Bucket owners can do everything readers and writers can do, as well as update metadata such as ACLs.	Object owners can do everything readers can do, as well as update metadata such as ACLs.

, you control access to your objects by assigning these roles to different actors

ACL for your bucket in the Cloud Console. You can do this by clicking the vertical three-dot button on the far right in your list of buckets and selecting Edit bucket permissions.
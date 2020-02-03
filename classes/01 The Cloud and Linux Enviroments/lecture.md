
<b>Text export for future formatting anf corrections, do not use</b>

https://googleclouduconn.blogspot.com/p/chapter-1.html

Chapter 1 What is “Cloud”?
Chapter 1. What is “cloud”?
This chapter covers

Overview of “the cloud”


When and when not to use cloud hosting and what to expect


Explanation of cloud pricing principles


What it means to build an application for the cloud


A walk-through of Google Cloud Platform

Cloud is a collection of services that helps developers focus on their project rather than on the
 infrastructure that powers it.
Cloud services are things like Amazon Elastic Compute Cloud (EC2) or Google Compute Engine
 (GCE), which provide APIs to provision virtual servers, where customers pay per hour for the
 use of these servers.
infrastructure, where computing, storage, analytics, networking
Away from CPUs and RAM and toward APIs for higher-level operations such as storing or
 querying for data.
 Cloud services aim to solve your problem
 extremely flexible no provisioning or long-term contracts.
scale up and down with no advanced notice
 paying only for the resources you use in a given month.
1.1. What is Google Cloud Platform?
Google Cloud Platform (often abbreviated as GCP) is a collection of products that allows the world
 to use some of Google’s internal infrastructure.
 on-demand virtual machines via Google Compute Engine
object storage for storing files via Google Cloud Storage.
 APIs to some of the more advanced Google-built technology
Bigtable, Cloud Datastore, or Kubernetes.
differences
created incredible new technologies
 MapReduce (the research paper that spawned Hadoop and changed how we handle “Big Data”)
 Bigtable (the paper that spawned Apache HBase), and Spanner.
 Google operates at such a scale that it has many economic advantages
lower prices. immense physical infrastructure,
which means it buys and builds custom hardware to support it
1.2. Why cloud?
cloud hosting offers a lot of flexibility
how much computing power you need.
Maintenance built in for several products.
No need to manage your own databases, operating systems, and  your own hardware
1.2.1. Why not cloud?
 Google itself. Google’s infrastructural footprint is exabytes of data, hundreds of thousands of CPUs,
 a relatively stable and growing overall workload. In addition, Google is a big target for attacks
 (for example, denial-of-service attacks) and government espionage and has the budget and expertise
 to build gigantic infrastructural footprints. All of these things together make Google a bad candidate
 for cloud hosting.
 figure 1.2, which shows a more typical company of the internet age, where growth is spiky and
unpredictable and tends to drop without much notice. In this case, the company bought enough
computing capacity (the top line) to handle a spike, which was needed up front, but then when traffic
 fell (the bottom line), it was stuck with quite a bit of excess capacity.
Figure 1.2. Unexpected pattern of resource consumption


1.3. What to expect from cloud services
1.3.1. Computing

Provisioning your machine will be fast.
Compared to colocated or on-premises hosting will be  significantly faster.
If you turn on a VM right now, you might notice that there’s no mention of payment.
typical virtual private server (VPS), where you agree on a set price and purchase the VPS for a full
year, making monthly payments
Google doesn’t mention payment at this time for a simple reason: they don’t know how long you’ll
keep that machine running, so there’s no way to know how much to charge you.
at the end of the month or when you turn off the VM.
1.3.2. Storage
Storage is necessary.
storing data is a complicated thing to do.
 data to be edge-cached to speed up downloads for users on the internet? Are you optimizing for
throughput or latency?
How many concurrent readers do you need to support?
abstraction provided by a storage service gives you the ability to configure your storage mechanisms
 for various levels of performance, durability, availability, and cost.
the failure aspects of storing data typically disappear.
a hard drive never fails  and your data was never lost.
reduced-availability options - try to download your data and get an error telling you to try again later
, paying much less for storage of that class
Virtual disks in the cloud - choices about how you can store your data, capacity (measured in GB)
and in performance (I/O).
Cloud Datastore -  unique to cloud hosting, relying on huge, shared, highly scalable systems built by
and for Google.
1.3.3. Analytics (aka, Big Data)
Analytics, see this area called “Big Data.”
logging and storing almost everything,
amount of data they have to analyze and use to draw  conclusions is growing
Enormous amounts of data more manageable, new and interesting open source projects are
popping up, such as Apache Spark, HBase, and Hadoop.
1.3.4. Networking
those pieces to talk to each other, your system isn’t a single system—it’s more of a pile of isolated
 systems.
expect that it has a connection to the internet and that it will be fast enough.
 faster-than-normal network connections, advanced firewalling abilities (where you only allow
certain IPs to talk to certain ports), load balancing (where requests come in and can be handled by
 any one of many machines), and SSL certificate management (where you want requests to be
encrypted but don’t want to manage the certificates for each individual virtual machine).
typical computing stack (such as configure VPNs, set up firewalls with iptables, and balance
 requests across servers using HAProxy) are all still possible.
simplify the common cases, rely on Google’s Cloud Load Balancer to route requests.
1.3.5. Pricing
Comparing only the dollar cost of buying the hardware from a vendor versus a cloud hosting
provider is going to favor the vendor
Compare costs of hosting infrastructure use  TCO, or total cost of ownership.  the cost of
purchasing the physical hardware
 costs such as human labor (like hardware administrators or security guards), utility costs
 (electricity or cooling)
support and on-call staff who make sure that any software services running building redundancy
for your systems so that  data is never lost
necessary knowledge to design the system for this configuration.
1.4. Building an application for the cloud
1.4.1. What is a cloud application?
difference is in the assumptions made about the application’s architecture.
In a traditional application, we tend to deploy  binaries running on particular servers
a typical cloud application relies on hosted or managed services whenever possible.
relies on containers the way a traditional application would rely on servers. cloud application is
often much more flexible and able to grow and shrink
1.4.2. Example: serving photos
The old way of doing this was to store the image data in your relational database, and then
whenever someone wanted to see the profile photo, you’d retrieve it from the database and return
it through your web server, as shown in figure 1.3.
Figure 1.3. Serving photos dynamically through your web server

storing binary data in your database is inefficient.
storing the binary data of a photo in your database, you’re putting extra load on the database itself,
 but not using it for the things it’s good at, like joining relational data together.
don’t need transactional semantics on your photo (which here, we don’t), it makes more sense to put
 the photo somewhere on a disk and then use the static serving capabilities of your web server to
 deliver those bytes, as shown in figure 1.4.
Figure 1.4. Serving photos statically through your web server

have a single web server living somewhere inside a data center, serving a photo it has stored locally
 on its disk.
if someone nearby (for example, in New York) requests that photo, they’ll get a relatively zippy
response.
someone far away, like in Japan, requests the photo? The only way to get it is to send a request from
Japan to the United States, and then the server needs to ship all the bytes from the United States
back to Japan.
the answer is edge caching, or relying on a content distribution network. services is that you give
 them copies of your data in lots of different geographical locations.
send a URL pointing to this content distribution provider,
returns the photo using the closest available server.
cloud hosting is to provide managed services that solve the problem
managed storage service, which handles content distribution automatically
Figure 1.5. Serving photos statically through Google Cloud Storage

cloud is more than a different way of managing computing resources. managed or hosted services via
simple APIs to do complex things
1.4.3. Example projects
To-Do List
To-Do List is nothing more than an application that allows users to create lists, add items to the lists,
 and mark them as complete.
In short, the To-Do List is a useful demonstration of doing something real, but incredibly simple,
with cloud infrastructure.
InstaSnap
InstaSnap  -  application allows users to take photos or videos, share them on a “timeline” and have
them self-destruct.
focus is on scaling the application to handle hundreds of thousands of requests every single second.
add up to enormous amounts of data.
 thousands of people to request the same photos at the same time.
E*Exchange
E*Exchange is an application for trading stocks in the United States
 enterprise-grade features of cloud infrastructure
concerns about using shared services, with regard to security and access control. Summary


Cloud has become a buzzword, a collection of services that abstract away computer infrastructure.


Cloud is a good fit if you don’t want to manage data centers and needs change often.


Cloud is a bad fit if your usage is steady over long periods of time.


When in doubt, if you need tools for GCP, start at http://cloud.google.com.

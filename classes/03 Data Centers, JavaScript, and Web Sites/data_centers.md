# Data Centers

## Overview

## Lab

## Resources

https://googleclouduconn.blogspot.com/p/chapter-3.html

Chapter 3 cloud data center
Googliness
Chapter 3. The cloud data center

What data centers are and where they are
Data center security and privacy
Regions, zones, and disaster isolation
your web host was physically located in a data center

 deploying in the cloud is similar to traditional hosting

your resources live inside a data center




 strict security to enter the premises

Locations

some services do abstract away the idea of location so that your resources live in multiple places simultaneously, but for many services (such as Compute Engine), resources live in a single place. This means you’ll likely want to choose one near your customers.

As of this writing, Google Cloud operates data centers in 15 different regions around the world




Provider
Data centers
Google Cloud	44 (across 15 cities)
Amazon Web Services	49 (across 18 cities)
Azure	36 (across 19 cities)
Digital Ocean	11 (across 7 cities)
Rackspace	6


 two factors might make you choose a provider based on the data center locations it offers, and both are focused on network latency:

need ultralow latency between your servers and your customers.

one millisecond slower than your competitors means you’ll lose out on a trade.

customers that are far away from the nearest data center.

imple as loading a web page from Australia could be frustratingly slow.




3.2. Isolation levels and fault tolerance

3.2.1. Zones
A zone is the smallest unit in which a resource can exist.
single facility that holds lots of computers (like a single data center)
 two resources living not only geographically nearby, but in the same physical building
natural disaster occurs
resources in this single zone are likely to go offline together
malfunction such as a power outage occurred, it likely would affect the entire zone.
Delta Air Lines says the total bill for its devastating computer outage will come to $150 million.
The problem occurred when the company lost power at its operations center in Atlanta early on the morning of Aug. 8, causing computers needed to book in passengers and fly jets to be down for nearly five hours.
The computer system failure that caused Southwest Airlines to cancel or delay more than 2,000 flights last month will cost the airline between $54 million and $82 million in lost revenue and increased costs.
 Sony
One of the more infamous service outages of recent years occurred in 2011 when Sony took its PlayStation Network offline on April 20 in the wake of a cyberattack that compromised the personally identifiable information (PIN) of the service’s 77 million users. The outage lasted for 23 days while Sony worked to rebuild its cybersecurity infrastructure. Best estimates put the total losses resulting from the attack as high as $250 million.

3.2.2. Regions
collection of zones is called a region
two resources in the same region but different zones, say us-east1-b and us-east1-c, the resources will be somewhat close together (meaning the latency between them will be shorter than if one resource were in a zone in Asia), but they’re guaranteed to not be in the same physical facility.

wo resources might be isolated from zone-specific failures (like a power outage), they might not be isolated from catastrophes (like a tornado).

.2.3. Designing for fault tolerance

zonal means that if the zone it lives in goes down, it also goes down.

turn on a single VM and you have a zonal service—and the least highly available.

Regional—  replicated throughout multiple zones in a single region.

If one zone goes down, you automatically flip to the instance in the other zone.

Multiregional—composition of several different regional services
catastrophe occurs that takes down an entire region, your service should still continue to run
Global—special case of a multiregional service.
regions are spread around the world, crossing legal jurisdictions and network providers.
want to use multiple cloud providers (for example, Amazon Web Services alongside Google Cloud) to protect the service against disasters spanning an entire company.

 your levels of fault tolerance and isolation are at any time.
system becomes absolutely critical, you at least know which pieces will need redundant deployments
3.2.4. Automatic high availability

designed richer systems that are automatically highly available.
rely on Google Cloud Storage, which provides the same level of fault isolation (among other things) for your basic storage needs.

 systems follow this pattern, such as Google Cloud Datastore, which is a multiregional nonrelational storage system that stores your data in five different zones, and Google App Engine, which offers two multiregional deployment options (one for the United States and another for Europe) for your computing needs

typically you have to build things with a bit more structure

design your data model in a way that forces you to choose whether you want queries to always return the freshest data, or you want your system to be able to scale to large numbers of queries.
understand fault tolerance, regions, zones,

3.3. Safety concerns

personal and business privacy have become a mainstream topic of conversation
leaks of passwords, credit card data, and personal information have led the online world to become far less trusting
the company holding that information will get hacked or a government organization will request access to the data

putting your servers in someone else’s data center typically involves giving up some control over your assets (such as data or source code) in exchange for other benefits (such as flexibility or lower costs).


3.3.1. Security
securing resources

Privacy—Only authorized people should be able to access the resources.
Availability—The resources should never be inaccessible to authorized people.
Durability—The resources should never be corrupted or go missing.
simple theft. For example, if someone breaks into your home and steals your hard drive
breaks your goals for availability and durability

wasn’t encrypted at all, this also breaks the privacy goal
natural disasters, such as earthquakes, fires, and floods, but in the case of storing data at home, it also includes more common accidents, such as power surges, hard drive failures, and kids spilling water on electronic equipment.accidentally formatting the drive because you thought it was a different drive
you’re accidentally telling it to do the wrong thing


cloud providers plan for these problems

Secure facilities—Any facility housing resources (like hard drives) should be a high-security area, limiting who can come and go

Encryption—Anything stored on disks should be encrypted. This is to prevent theft compromising data privacy.

Replication—Data should be duplicated in many different places. This is to prevent a single failure resulting in lost data (durability) as well as a network outage limiting access to data (availability)
Backup—Data should be backed up off-site and can be easily restored on request.

providing this sort of protection in your own home isn’t just challenging and expensive—by definition it requires you to have more than one home


3.3.2. Privacy
 Google Cloud Storage might keep your photo in an encrypted form
  ask for it back, it arrives unencrypted.  data is stored in encrypted form and transferred between data centers similarly, when you ask for your data,
 Google Cloud does havGoogle were to receive a court order, it does have the technical ability to comply with the order and decrypt your data without your consent.e the encryption key

many cloud services provide the ability to use your own encryption keys, meaning that the best Google can do is hand over encrypted data, because it doesn’t have the keys to decrypt it.


3.3.3. Special cases

special situations require heightened levels of security

Government agencies often have strict requirements.
Companies in the U.S. healthcare industry must comply with HIPAA regulations.
Companies dealing with the personal data of German citizens must comply with the German BDSG.
cloud providers have come up with a few options:
Amazon offers GovCloud to allow government agencies to use AWS.
Google, Azure, and AWS will all sign BAAs to support HIPAA-covered customers.
Azure and Amazon offer data centers in Germany to comply with BDSG.

 cloud data centers are safe enough for your typical needs, and you’re open to exploring them for your special needs.

3.4. Resource isolation and performance

breakthrough that opened the door to cloud computing was the concept of virtualization
 build a large cluster of physical computers, then lease out smaller virtual ones by the hour.
profitable as long as the leases of the smaller virtual computers covered the average cost to run the physical computers.

one person using a virtual half computer could run a CPU-intensive workload that spills over into the resources of another person using a second virtual half computer

issue has come to be known as the noisy neighbor problem

only get perfect resource isolation on bare metal (nonvirtualized) machines.

the cloud services ultimately run on top of a system called Borg
way of efficiently parceling work across Google’s vast fleet of ... servers.

 same system internally for other services (such as Gmail and YouTube), resource isolation (or perhaps better phrased as resource fairness) is a feature that has almost a decade of work behind it and is constantly improving

Summary
Google Cloud has many data centers in lots of locations around the world for you to choose from.
The speed of light is the limiting factor in latency between data centers, so consider that distance when choosing where to run your workloads.
When designing for high availability, always use multiple zones to avoid zone-level failures, and if possible multiple regions to avoid regional failures.
Google’s data centers are incredibly secure, and its services encrypt data before storing it.
If you have special legal issues to consider (HIPAA, BDSG, and so on), check with a lawyer before storing information with any cloud provider.


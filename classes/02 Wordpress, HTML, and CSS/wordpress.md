# WordPress

## Overview
WordPress is a <b>CMS (Content Management System)</b> that can be hosted on a
server. It runs using the programming language PHP, and offers the admin
the capability of quickly deploying a polished site. It is tailored
primarily to blogs, personal, and company sites - but can be expanded
with <b>plugins</b> to add a wide variety of features. <b>Themes</b>
also help easily change the look and feel of the site.

## Lab
### Deploying WordPress
You will be deploying WordPress on the App Engine through the Google
Cloud Shell. The instructions can be found here:
[CodeLab: Running WordPress on App Engine Flexible Environment](https://codelabs.developers.google.com/codelabs/cloud-wordpress-on-flex/index.html?index=..%2F..index#3)

### Creating Pages
To create pages on your WordPress website, simply log into the admin
portal, and click New > Page. You can edit the content of the page
her with either the WYSIWYG (What You See Is What You Get) editor, or
by creating HTML. The "Publish" button will publish the page to your
website.


## Resources


<p align="center">

</p>


## Lecture
Chapter 2: WordPress


### 2.1: What is WordPress?
Laying out the pieces of a WordPress deployment
Turning on a SQL database to store your data
Turning on a VM to run WordPress
Turning everything off

Deploying WordPress yourself using
Google Compute Engine and Google Cloud SQL to host your infrastructure


Simple WordPress Setup:
<p align="center">
    <img src="https://dpzbhybb2pdcj.cloudfront.net/geewax2/Figures/02fig01_alt.jpg">
</p>

1. Request a page from the server.
2. WordPress server queries the database.
3. database sends back a result (for example, the content of the page)
4. WordPress server sends back a web page.

Cloud services for more complicated hosting problems like content distribution:
<p align="center">
    <img src="https://dpzbhybb2pdcj.cloudfront.net/geewax2/Figures/02fig02_alt.jpg">
</p>

1. WordPress server modifies references to static content so that rather
than requesting it from the WordPress server, the browser requests it
from Google Cloud Storage
2. requests for images and other static content will be handled directly by Google Cloud Storage
distributing your content around the world and caching the data close to your readers. Static content will be delivered quickly

### 2.2 Digging into the database
The most popular open source database is MySQL. In the early days
of cloud computing, the standard way to turn on a database like MySQL
was to create a virtual machine, install the MySQL binary package,
and manage that virtual machine like any regular server.

Cloud providers noticed that databases all seemed to follow this
same pattern and started offering managed database services,
where you don’t have to configure the virtual machine yourself
but instead turn on a managed virtual machine running a specific binary.

Amazon has Relational Database Service (RDS),
Azure has SQL Database service,
and Google has Cloud SQL service.

For a WordPress deployment, which is typical, Cloud SQL is a great fit.


### 2.2.1. Turning on a Cloud SQL instance

1. The first step to turning on our database is to go to
[Cloud Console](https://console.cloud.google.com) and
click SQL in the left-side navigation menu.
<p align="center">
    <img src="https://1.bp.blogspot.com/-tFt8GQtcIdM/XW3JowEJLgI/AAAAAAAALNQ/JI-P0h-cvxAx3o32TpJ250mG7DXyf7NjwCLcBGAs/s640/g20.JPG">
</p>

2. Click Create instance
<p align="center">
    <img src="https://1.bp.blogspot.com/-X_o1AV_EoLA/XW3J9-yGewI/AAAAAAAALNY/2V5CR1AJ30oj15j0b-5okhrZp-uwEFNLgCLcBGAs/s640/g21.JPG">
</p>

3. Select a second Generation MySQL instance by clicking "Choose MySQL" -
This creates a virtual machine that Google will manage for you, as well as installs and configures MySQL.
<p align="center">
    <img src="https://1.bp.blogspot.com/-X_o1AV_EoLA/XW3J9-yGewI/AAAAAAAALNY/2V5CR1AJ30oj15j0b-5okhrZp-uwEFNLgCLcBGAs/s640/g21.JPG">
</p>

4. Name your database - Let’s name the database <i>wordpress-db</i>
the name has to be unique inside your project.
<p align="center">
    <img src="https://1.bp.blogspot.com/-DKITiT_rmFw/XW3McrtzZjI/AAAAAAAALOA/9kkFVhCye4MjDCrGJKLf3fwBDdxF9SbgQCLcBGAs/s640/g23.JPG">
</p>

5. Choose a password - I used <i>uconnstamford</i> for the password.
<p align="center">
    <img src="https://1.bp.blogspot.com/-r7hAtqKITns/XW3M7sniyaI/AAAAAAAALOI/YJ2OiQPRzzkna2i3BeZ4QbFy8dWGCHREwCLcBGAs/s640/g24.JPG">
</p>

6. Creation will take a few minutes. Once it is complete a green checked circle will appear
<p align="center">
    <img src="https://1.bp.blogspot.com/-glVtSqxk2to/XW3OZsRMxHI/AAAAAAAALOU/2lmL1ieMsC4g487w5I4c1TILdmaqQ7bPwCLcBGAs/s640/g25.JPG">
</p>

7. Once created, click into the instance and hit edit
<p align="center">
    <img src="https://1.bp.blogspot.com/-_RXTZYMFaqY/XjY-LgGn2MI/AAAAAAAAL5I/or2adrkpYmg9CbE6t8-OCz0kX5kYCttuACLcBGAsYHQ/s640/w2.JPG">
</p>

8. Once you hit edit the you need to go into connectivity, in the overview page
<p align="center">
    <img src="https://1.bp.blogspot.com/-bg20U8jDoEY/XjY-1h21FgI/AAAAAAAAL5Q/-KPMxTzmTaYYMKzg-Uz09o9HCu3ZnhDZgCLcBGAsYHQ/s640/w3.JPG">
</p>

### 2.2.2. Securing your Cloud SQL instance

9. Add a new newtwork - Open the Authorization tab,
click the Add network button, add “the world” in CIDR notation
(0.0.0.0/0, which means “all IPs possible”), then hit done and save.
<p align="center">
    <img src="https://1.bp.blogspot.com/-bg20U8jDoEY/XjY-1h21FgI/AAAAAAAAL5Q/-KPMxTzmTaYYMKzg-Uz09o9HCu3ZnhDZgCLcBGAsYHQ/s640/w3.JPG">
</p>
Note: You should never leave this setting for your production environments.

### 2.2.3. Connecting to your Cloud SQL instance

10. From Instance Details on overview tab click: Connect using Cloud Shell
<p align="center">
    <img src="https://1.bp.blogspot.com/-R4rIn7gZ5KU/XXXEeOPkytI/AAAAAAAALRg/iyIi_84rMP0DPhwECayTXPs6KbQ9yKYmwCLcBGAs/s640/a1.JPG">
</p>

```
Welcome to Cloud Shell! Type "help" to get started.
Your Cloud Platform project in this session is set to uconn-engr.
Use “gcloud config set project [PROJECT_ID]” to change to a different project.
john_iacovacci1@cloudshell:~ (uconn-engr)
$ gcloud sql connect wordpress-db --user=root --quiet
```

It will type out a connection string where you just have to hit return

```
Whitelisting your IP for incoming connection for 5
minutes...done.
Connecting to database with SQL user [root].
Enter password:
```

Enter your password, once the password is accepted you are now ready to work in MySQL.
Note: When typing the password, no stars or characters will display, but you are still typing the password in.

```SQL
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 95
Server version: 5.7.14-google-log (Google)
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MySQL [(none)]>
```



### 2.2.4. Configuring your Cloud SQL instance for WordPress

Objectives:
* Create a database called wordpress.
* Create a user called wordpress.
* Give the wordpress user the appropriate permissions on the MySQL prompt


11. Create the database:

Input:
```SQL
MySQL [(none)]> CREATE DATABASE wordpress;
```

Output:
```
Query OK, 1 row affected (0.04 sec)
```


12. Create a user account for WordPress to use for access to the database:

Input:
```SQL
MySQL [(none)]> CREATE USER wordpress IDENTIFIED BY 'uconnstamford';
```

Output:
```
Query OK, 0 rows affected (0.05 sec)
```


13. Give this new user the right level of access to do things to the database (like create tables, add rows, run queries, and so on):

Input:
```SQL
MySQL [(none)]> GRANT ALL PRIVILEGES ON wordpress.* TO wordpress;
```

Output:
```
Query OK, 0 rows affected (0.04 sec)
```


14. Tell MySQL to reload the list of users and privileges.

Input:
```SQL
MySQL [(none)]>  FLUSH PRIVILEGES;
```

Output:
```
Query OK, 0 rows affected (0.04 sec)
```



### 2.3. Deploying the WordPress VM

use compute engine to create a Virtual Machine



Create a new VM for our WordPress site



Name instance wordpress

leave defaults



Make sure we allow traffic






click into the instance then connect via ssh





check boxes for allowing HTTP and HTTPS traffic are selected because we want our WordPress server to be accessible to anyone through their browsers.


2.4. Configuring WordPress

connect to VM via SSH.

On Linux operating systems that use the APT package management system, the apt-get command is used to install, remove, and perform other operations on installed software packages. The apt-get command, and other core APT utilities, are available by default in the Debian, Ubuntu, and Linux Mint operating systems.


john_iacovacci1@wordpress:~$ sudo apt-get update
Hit:1 http://security.debian.org stretch/updates InRelease
Ign:2 http://deb.debian.org/debian stretch InRelease
Hit:3 http://packages.cloud.google.com/apt cloud-sdk-stretch InRelease
Hit:4 http://deb.debian.org/debian stretch-updates InRelease
Hit:5 http://deb.debian.org/debian stretch-backports InRelease
Hit:6 http://deb.debian.org/debian stretch Release
Hit:7 http://packages.cloud.google.com/apt google-compute-engine-stretch-s
table InRelease
Hit:8 http://packages.cloud.google.com/apt google-cloud-packages-archive-k
eyring-stretch InRelease
Reading package lists... Done
john_iacovacci1@wordpress:~$



sudo apt-get install apache2 mysql-client php7.0-mysql php7.0 libapache2-mod-php7.0 php7.0-mcrypt php7.0-gd

john_iacovacci1@wordpress:~$ sudo apt-get install apache2 mysql-client php7.0-mysql php7.0 libapache2-mod-
php7.0 php7.0-mcrypt php7.0-gd

Apache is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free.




wget http://wordpress.org/latest.tar.gz

john_iacovacci1@wordpress:~$ wget http://wordpress.org/latest.tar.gz
--2019-09-11 01:00:31--  http://wordpress.org/latest.tar.gz
Resolving wordpress.org (wordpress.org)... 198.143.164.252
Connecting to wordpress.org (wordpress.org)|198.143.164.252|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://wordpress.org/latest.tar.gz [following]
--2019-09-11 01:00:31--  https://wordpress.org/latest.tar.gz
Connecting to wordpress.org (wordpress.org)|198.143.164.252|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 11198223 (11M) [application/octet-stream]
Saving to: ‘latest.tar.gz’
latest.tar.gz              100%[======================================>]  10.68M  --.-KB/s    in 0.1s
2019-09-11 01:00:31 (86.5 MB/s) - ‘latest.tar.gz’ saved [11198223/11198223]

GNU Wget (or just Wget, formerly Geturl, also written as its package name, wget) is a computer program that retrieves content from web servers.

john_iacovacci1@wordpress:~$tar xzvf latest.tar.gz

The Linux ‘tar’ stands for tape archive, is used to create Archive and extract the Archive files.
john_iacovacci1@wordpress:~$  cd wordpress
john_iacovacci1@wordpress:~/wordpress$
john_iacovacci1@wordpress:~/wordpress$ cp wp-config-sample.php wp-config.php

john_iacovacci1@jiacovacci:~$
~/wordpress$ cp wp-config-sample.php wp-config.php





set some configuration parameters

where WordPress should store data and how to authenticate


pico wp-config.php

edit the file to point to your Cloud SQL instance.







<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://codex.wordpress.org/Editing_wp-config.php
 *
 * @package WordPress
 */
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );
/** MySQL database username */
define( 'DB_USER', 'wordpress' );
/** MySQL database password */
define( 'DB_PASSWORD', 'uconnstamford' );
/** MySQL hostname */
define( 'DB_HOST', '35.237.31.226' );
/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );
?>

move all those files out of your home directory and into somewhere that Apache can serve them.



remove the Apache default page, index.html. The easiest way to do this is using rmand then rcp
 sudo rm /var/www/html/index.html


cd ~/wordpress/

sudo rcp -r * /var/www/html









Now navigate to the web server in your browser http://35.193.13.130

following the prompts should take about 5 minutes, and you’ll have a working WordPress installation!



Configure WP with email account and password

add new pages






You tube of creating WordPress Pages
2.5. Reviewing the system
So what did you do here? You set up quite a few different pieces:
You turned on a Cloud SQL instance to store all of your data.
You added a few users and changed the security rules.
You turned on a Compute Engine virtual machine.
You installed WordPress on that VM.
 set the security rules on the Cloud SQL instance to accept connections from anywhere (0.0.0.0/0)?

accept requests (your VM), you should fix that.
 If you don’t, the database is vulnerable to attacks from the whole world.

But if we lock down the database at the network level, even if someone discovers the password, it’s useful only if they are connecting from one of our known machines.

Cloud Console, and navigate to your Cloud SQL instance.

edit the Authorized Network, changing 0.0.0.0/0 to the IP address followed by /32

rename the rule to read us-central1-c/wordpress






edit configuration




VM instance could change. To avoid that, you’ll need to reserve a static IP address


2.6. Turning it off

The first thing to turn off is the GCE virtual machine.

two options, Stop and Delete.

delete an instance, it’s gone forever

When you stop an instance, it’s still there, but in a paused state from which you can pick up exactly where you left off.

 disk that stores the operating system and all your configuration settings needs to stay around. You are billed for your disks whether or not they’re attached to a running virtual machine
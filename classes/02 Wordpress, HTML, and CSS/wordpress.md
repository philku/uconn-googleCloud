# WordPress

## Overview
WordPress is a <b>CMS (Content Management System)</b> that can be hosted on a
server. It runs using the programming language PHP, and offers the admin
the capability of quickly deploying a polished site. It is tailored
primarily to blogs, personal, and company sites - but can be expanded
with <b>plugins</b> to add a wide variety of features. <b>Themes</b>
also help easily change the look and feel of the site.


## Lecture and Lab
Chapter 2: WordPress

You will be deploying WordPress on the App Engine through the Google
Cloud Shell. Your instructor may elect to have you follow the instructions
in this document or the Google Code Lab: [CodeLab: Running WordPress on App Engine Flexible Environment](https://codelabs.developers.google.com/codelabs/cloud-wordpress-on-flex/index.html?index=..%2F..index#3)


### 2.1: What is WordPress?

In this lesson we will cover:
* Laying out the pieces of a WordPress deployment
* Turning on a SQL database to store your data
* Turning on a VM to run WordPress
* Deploying WordPress
* Turning everything off


Simple WordPress Setup:
<p align="center">
    <img src="https://dpzbhybb2pdcj.cloudfront.net/geewax2/Figures/02fig01_alt.jpg">
</p>

1. Request a page from the server.
2. WordPress server queries the database.
3. database sends back a result (for example, the content of the page)
4. WordPress server sends back a web page.

Cloud services can augment WordPress to distribute content such as images
faster:
<p align="center">
    <img src="https://dpzbhybb2pdcj.cloudfront.net/geewax2/Figures/02fig02_alt.jpg">
</p>

1. WordPress server modifies references to static content so that rather
than requesting it from the WordPress server, the browser requests it
from Google Cloud Storage
2. requests for images and other static content will be handled directly by Google Cloud Storage
distributing your content around the world and caching the data close
to your readers. Static content will be delivered quickly.

<hr>

### 2.2 Digging into the database
The most popular open source database is MySQL. In the early days
of cloud computing, the standard way to turn on a database like MySQL
was to create a virtual machine, install MySQL,
and manage that virtual machine like any regular server.

Cloud providers noticed that databases all seemed to follow this
same pattern and started offering managed database services -
where you don’t have to configure the virtual machine yourself
but instead turn on a managed virtual machine running a specific binary.

Amazon has Relational Database Service (RDS),
Azure has SQL Database service,
and Google has Cloud SQL service.

For a WordPress deployment, which is typical, Cloud SQL is a great fit.

<hr>

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

8. Once you hit edit the you'll need to navigate into connectivity, in
the overview page
<p align="center">
    <img src="https://1.bp.blogspot.com/-bg20U8jDoEY/XjY-1h21FgI/AAAAAAAAL5Q/-KPMxTzmTaYYMKzg-Uz09o9HCu3ZnhDZgCLcBGAsYHQ/s640/w3.JPG">
</p>

<hr>

### 2.2.2. Securing your Cloud SQL instance

9. Add a new network - Open the Authorization tab,
click the Add network button, add “the world” in CIDR notation
(0.0.0.0/0, which means “all IPs possible”), then hit done and save.
<p align="center">
    <img src="https://1.bp.blogspot.com/-bg20U8jDoEY/XjY-1h21FgI/AAAAAAAAL5Q/-KPMxTzmTaYYMKzg-Uz09o9HCu3ZnhDZgCLcBGAsYHQ/s640/w3.JPG">
</p>
Note: You should never leave this setting for your production
environments.

<hr>

### 2.2.3. Connecting to your Cloud SQL instance

10. From Instance Details on overview tab click: Connect using Cloud Shell
<p align="center">
    <img src="https://1.bp.blogspot.com/-R4rIn7gZ5KU/XXXEeOPkytI/AAAAAAAALRg/iyIi_84rMP0DPhwECayTXPs6KbQ9yKYmwCLcBGAs/s640/a1.JPG">
</p>


It will type out a connection string where you just have to hit return:
```
Welcome to Cloud Shell! Type "help" to get started.
Your Cloud Platform project in this session is set to uconn-engr.
Use “gcloud config set project [PROJECT_ID]” to change to a different project.
john_iacovacci1@cloudshell:~ (uconn-engr)
$ gcloud sql connect wordpress-db --user=root --quiet
```

After connecting, a prompt will appear for a password.
Enter your password, once the password is accepted you are now ready to work in MySQL.
Note: When typing the password, no stars or characters will not display,
but if you are in the cloud shell, you are still typing the password
even if it may seem like you're not.
```
Whitelisting your IP for incoming connection for 5
minutes...done.
Connecting to database with SQL user [root].
Enter password:
```

You are connected once the following appears:
```SQL
MySQL [(none)]>
```


<hr>

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


<hr>

### 2.3. Deploying the WordPress VM

Here we sill use use compute engine to create a Virtual Machine

15. Select Compute Engine from the top left navigation menu
<p align="center">
    <img src="https://1.bp.blogspot.com/-d0-zLXxGUdA/XjZEStvdMuI/AAAAAAAAL5c/NAMAETLL3WkevgSty5cCpSoScvAEw7f4gCLcBGAsYHQ/s640/w4.JPG">
</p>

16. Create a new VM for our WordPress site
<p align="center">
    <img src="https://1.bp.blogspot.com/-2XWch0SHJI0/XjZFk1ciqxI/AAAAAAAAL5o/GIZhk2KwZGsyDLSsQI5dT4hffJ16HhFwQCLcBGAsYHQ/s640/w5.JPG">
</p>

17. Name the instance <b>wordpress</b> and leave the rest of the defaults
<p align="center">
    <img src="https://1.bp.blogspot.com/-IR1uLF2S6Mw/XjZGtaZ-SzI/AAAAAAAAL50/699NKoyeDYoMcX7Z3uDaTrAhPoN1dIRhgCLcBGAsYHQ/s640/w6.JPG">
</p>

18. Make sure we allow traffic
<p align="center">
    <img src="https://1.bp.blogspot.com/-ACIU6zVk8es/XXXKrrcpAMI/AAAAAAAALRw/lBIVHCffwEIP2NInWT2f5UoLtoWI3ymPQCLcBGAs/s640/a3.JPG">
</p>

18. Make sure we allow traffic by checking Allow HTTPS and Allow HTTP under Firewall
<p align="center">
    <img src="https://1.bp.blogspot.com/-ACIU6zVk8es/XXXKrrcpAMI/AAAAAAAALRw/lBIVHCffwEIP2NInWT2f5UoLtoWI3ymPQCLcBGAs/s640/a3.JPG">
</p>

18. Click Create. Once your instance is created you will see a green check by the name "wordpress"
<p align="center">
    <img src="https://1.bp.blogspot.com/-eUON0tu3vpc/XjZIHzDgTyI/AAAAAAAAL6A/6H87MUh7sh4BccW7DJBMSo1mKdaIC8dhgCLcBGAsYHQ/s640/w7.JPG">
</p>

<hr>
### 2.4. Configuring WordPress

18. Once your VM has been created, click into the instance and connect via SSH
<p align="center">
    <img src="https://1.bp.blogspot.com/-GKk5qucZsmM/XXhG5NE-nvI/AAAAAAAALSI/4ZOqFrw_rDM0ZeqBPPlAQgcbaLYY1zLIgCLcBGAsYHQ/s1600/a4.JPG">
</p>

On Linux operating systems that use the <b>APT</b> package management
system, the <b>apt-get</b> command is used to install, remove,
and perform other operations on installed software packages.
The apt-get command, and other core APT utilities,
are available by default in the Debian, Ubuntu, and Linux Mint
operating systems.

<hr>
19. Update with apt-get

Input:
```
sudo apt-get update
```

Output:
```
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
```


<hr>
20. Install required packages

Apache is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free.

Input:
```
sudo apt-get install apache2 mysql-client php7.0-mysql php7.0 libapache2-mod-php7.0 php7.0-mcrypt php7.0-gd
```



<hr>
21. Get wordpress with wget

GNU Wget (or just Wget, formerly Geturl, also written as its package name, wget) is a computer program that retrieves content from web servers.

Input:
```
wget http://wordpress.org/latest.tar.gz
```

Output:
```
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
```


<hr>
22. Extract wordpress to the wordpress directory

The Linux ‘tar’ stands for tape archive, is used to create Archive and extract the Archive files.

Input:
```
tar xzvf latest.tar.gz
```


<hr>
23. Navigate to the wordpress directory

The Linux ‘tar’ stands for tape archive, is used to create Archive and extract the Archive files.

Input:
```
cd wordpress
```

Your terminal input line should now look like this:
```
john_iacovacci1@wordpress:~/wordpress$
```
The <i>~/wordpress</i> section signifies that you are in the wordpress directory


<hr>
24. Copy the sample configuration

Here you will copy the file <i>wp-config-sample.php</i> and rename the
copy <i>wp-config.php</i>

Input:
```
cp wp-config-sample.php wp-config.php
```



<hr>
24. Edit the wordpress configuration

24.a. Double check the IP of your Cloud SQL instance and jot it down, you will need this in the next step.
<p align="center">
    <img src="https://1.bp.blogspot.com/-3QWpfBeFzUI/XjZPjW0A4KI/AAAAAAAAL6c/Y6r1wX-e8rUY-zJ1YxVhZCbhqFy3v5gmQCEwYBhgL/s640/w8.JPG">
</p>

24.b. Set the configuration parameters

Edit the file with pico/nano using the following command:
```
pico wp-config.php
```

This will open a PHP file in your editor that should look somewhat like
the following:

```PHP
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
```

The IP address for 'DB_HOST' will need to be changed to the IP address of
your Cloud SQL instance that you checked in 24.a.

You should also ensure that all the following variables are accurate:
<table>
  <tr>
    <th>DB_NAME</th>
    <td>The name of your database</td>
  </tr>
  <tr>
    <th>DB_User</th>
    <td>The name of your database user</td>
  </tr>
  <tr>
    <th>DB_PASSWORD</th>
    <td>The password you assigned to the user</td>
  </tr>
  <tr>
    <th>DB_HOST</th>
    <td>The IP of your Cloud SQL Instance</td>
  </tr>
  <tr>
    <th>DB_CHARSET</th>
    <td>utf8</td>
  </tr>
</table>



<hr>
25. Move the wordpress files so Apache can serve them

From the wordpress directory, run the following command:

Input:
```
sudo cp -r * /var/www/html
```

Note for troubleshooting: Now that the files are in a different location you will be required
to edit files in either the new location, or the old location and copy
them over again in order to see the results on the site.

<hr>
26. remove the Apache default page, index.html. The easiest way to do this is using rmand then rcp

Input:
```
sudo rm /var/www/html/index.html
```


<hr>
27. Check your installation

Navigate to the web server in your browser http://35.193.13.130

<hr>
28. Configure the Site

Configure WP with email account and password
<p align="center">
    <img src="https://1.bp.blogspot.com/-JjqxYtAH2hM/XXldSh5o1SI/AAAAAAAALSU/w8zNAUE_Zk8MmbMz9HjSJO5ADV33K-cKACLcBGAsYHQ/s640/wp.PNG">
</p>

<hr>
29. Add pages

To add pages, select <b>New \> Page</b> from the top of your screed or,
from the left-hand menu, select <b>Pages \> New</b>
<p align="center">
    <img src="https://1.bp.blogspot.com/-Zh7PiE9sC_U/XjZU9V7WNtI/AAAAAAAAL6w/cdhfLNGpX4oTjh9gmxOu4rSDswAH1-ZNwCLcBGAsYHQ/s320/w11.JPG">
</p>

You can edit the content of the page
her with either the WYSIWYG (What You See Is What You Get) editor, or
by creating HTML. The "Publish" button will publish the page to your
website.

Here is a video on how to create pages in WordPress:
[Video of creating WordPress Pages](https://ithemes.com/tutorials/creating-wordpress-page/)


### 2.5. Reviewing the system
So what did you do here? You set up quite a few different pieces:
* You turned on a Cloud SQL instance to store all of your data.
* You added a few users and changed the security rules.
* You turned on a Compute Engine virtual machine.
* You installed WordPress on that VM.

Remember that you set the security rules on the Cloud SQL instance to
accept connections from anywhere (0.0.0.0/0), you should fix that by
changing the network to only the IP of your instances that require access.
If you don’t, the database is vulnerable to attacks from the whole world.

If we lock down the database at the network level, even if someone
discovers the password, it’s useful only if they are connecting from
one of our known machines.

1. Go to Cloud Console, and navigate to your Cloud SQL instance.
2. Edit the Authorized Network, changing 0.0.0.0/0 to the IP address of your server followed by /32
3. Rename the rule to read us-central1-c/wordpress
<p align="center">
    <img src="https://1.bp.blogspot.com/-mzcJC7W8Upc/XXlk7eu2W1I/AAAAAAAALSw/dc-_BpzwvH05CBBehmFWJFI5mzmig3iAgCLcBGAsYHQ/s640/co.PNG">
</p>


Note: If you have an ephemeral IP, the IP of the VM instance could
change. To avoid that, you’ll need to reserve a static IP address


### 2.6. Turning it off

To turn off the items turned on during this lab, first shut off or
delete the GCE virtual machine. There are two options: Stop and Delete.

#### Stop
When you stop an instance, it’s still there, but in a paused state from
which you can pick up exactly where you left off. The disk that stores
the operating system and all your configuration settings needs to stay
around. You are billed for your disks whether or not they’re attached
to a running virtual machine

#### Delete
If you delete an instance, it’s gone forever

Next, shut off or delete the Cloud SQL instance.
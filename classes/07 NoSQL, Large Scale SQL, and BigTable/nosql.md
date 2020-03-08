# NoSQL
You have already learned about SQL databases in previous lessons. Another
option you may decide to use to store data may be a NoSQL datbase.
NoSQL databases are databases that do not use Structured Query Language
to perform actions on data. A person may elect to use a NoSQL database
if they require storing items in there database that may have a non-standardized
schema.

## Overview
The main differences between SQL databases and NoSQL databases can be found
in the language you use to control the database, the structure of the database,
and scalability.

### Language
Unlike SQL (Structured Query Language) databases, NoSQL databases do not
use a standardized language for commands. You will, for example, use
different commands to perform the same function in MongoDB and Google
Firestore.

### Structure
SQL databases are table based databases whereas NoSQL databases are document based, key-value pairs, graph databases or wide-column stores. This means that SQL databases represent data in form of tables which consists of n number of rows of data whereas NoSQL databases are the collection of key-value pair, documents, graph databases or wide-column stores which do not have standard schema definitions which it needs to adhered to.

SQL databases have predefined schema whereas NoSQL databases have dynamic schema for unstructured data.

### Scalability
SQL databases are vertically scalable whereas the NoSQL databases are horizontally scalable. SQL databases are scaled by increasing the horse-power of the hardware. NoSQL databases are scaled by increasing the databases servers in the pool of resources to reduce the load.

## Lab
In this lab, you will use App Engine and Datastore to create a web application
that stores information about books.

### 1
Navigate to Google Cloud Console, and start a new project if needed.

### 2
Navigate to "Source Repositories" in the left menu.

![Source Repositories Menu Item](https://github.com/philku/uconn-googleCloud/img/class_07_01.png)

### 3
Click "Add Repository" on the top right of the page.

![Add Repository](https://github.com/philku/uconn-googleCloud/img/class_07_02.png)

### 4
Select create new repository

![New Repository](https://github.com/philku/uconn-googleCloud/img/class_07_03.png)

### 5
Name your repository "default" and add your project ID to the project field.

![New Repository](https://github.com/philku/uconn-googleCloud/img/class_07_04.png)

### 6
Exit out of the repository tab.

### 7
Navigate to "Firestore" in the left menu of Google Cloud Console.

### 8
Select "Native Mode"

### 9
Open the Google Cloud Shell

### 10
Make a new directory by entering the following command.

Input:
```
mkdir bookshelf
```

### 11
Navigate into the new directory by entering the following command

Input:
```
cd bookshelf
```

### 12
Each Google Cloud Platform project comes with a single Cloud Source Repository called ‘default', which is currently empty. Type the following command to clone your default repository.

The first time this is run, you may need to enable the api in the command line. To do this if asked, simply press "y" then enter.

Input:
```
gcloud source repos clone default
```


### 13
Type the following command to change to the default directory.

Input:
```
cd default
```


### 14
The code for this course is hosted on GitHub. Type the following command to pull the code into your empty project repository.

Input:
```
git pull https://github.com/philku/gce-nosql-appengine-python.git
```


### 15
Type the following command to push your copy of the code to your project repository.

Input:
```
git push origin master
```


### 16
You use the pip command to install Python packages listed as requirements. The pip utility is already installed in Cloud Shell. Type the following command to install the packages and dependencies required for this lab into the lib directory.

Input:
```
pip3 install -r requirements.txt
```


### 17
You are now ready to deploy the Bookshelf application to App Engine. You use the gcloud command-line utility that is already installed in Cloud Shell to manage your App Engine application. Type the following command to deploy the application.

Input:
```
gcloud app deploy
```

The system asks you into which GCP region you want to deploy your application.

Enter a choice, using its number in the list presented, that is geographically close to you.

You are asked to confirm that you want to proceed with the deployment:

```Do you want to continue (Y/n)?```

Type Y and press return.

The output displays the progress of the deployment.

The deployment has successfully completed once you see the following message.

```Deployed service [default] to [https://<project-id>.appspot.com]```


### 18
Visit your site by entering the following command

Input:
```
gcloud app browse
```


### 19
Navigate to the webpage, add a book utilizing the "add field" button to add a field for "Pages".

Here you can see that since we are using a NoSQL Database, we can add fields to the book that have not already been defined in the schema.

### 20
Navigate to Firestore in the Google Cloud Console. Take a look at your data.

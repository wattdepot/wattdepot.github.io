---
layout: userguide
title: Quick Start
---
# 1. Prerequisites

## Operating System

The core development team has developed and run WattDepot on OS X, Windows 7, and Linux, so any of those platforms 
should be fine for development.

## Java

WattDepot requires [Java Platform Standard Edition] (http://www.oracle.com/us/technologies/java/standard-edition/overview/index.html) 
version 1.7 or above. If do not have Java installed you can get it from the previous link. Note that you will want the 
JDK (Java Development Kit) rather than the JRE (Java Runtime Environment) since you will be developing. To check the 
version of Java installed on your computer type `java -version` from the command line.

    $ java -version
    java version "1.7.0_40"
    Java(TM) SE Runtime Environment (build 1.7.0_40-b43)
    Java HotSpot(TM) 64-Bit Server VM (build 24.0-b56, mixed mode)

## PostgreSQL

Currently, WattDepot is using [Hibernate](http://hibernate.org/) for the persistence layer and the 
[PostgreSQL] (http://www.postgresql.org/) database for storage. You can configure Hibernate to use other database 
backends, but we do not have experience with other backends at this time.

If you do not have PostgreSQL installed already, you will have to 
[download and install it](http://www.postgresql.org/download/) before running WattDepot. We recommend version 9.1 or 
higher.

If you already have PostgreSQL, one way to check the version is using the command line client tool `psql`:

    $ psql --version
    psql (9.2.4)

However, it is possible to have the `psql` client installed without the actual database server (such as on OS X 
Mountain Lion). In that case you might see an error like the following:

    $ psql
    psql: could not connect to server: No such file or directory
	Is the server running locally and accepting
	connections on Unix domain socket "/var/pgsql_socket/.s.PGSQL.5432"?

In this case, you should [download PostgreSQL](http://www.postgresql.org/download/) to get the server as well.

# 2. Installation

## Download WattDepot

Download the latest release of WattDepot from the [releases]
(https://github.com/wattdepot/wattdepot/releases) page.

## Set up WattDepot server home directory

WattDepot stores all its files in a server home directory. By default, this will be a *.wattdepot3* directory in your 
home directory. If you want to create the *.wattdepot3* directory elsewhere, set the `wattdepot.user.home` Java 
property to the desired parent directory. The name *.wattdepot3* directory name itself is not configurable.

Within the server home directory are subdirectories that store all the data for a particular server instance. The 
default subdirectory is server, but this can be changed on the command line with the -d argument. This allows multiple 
WattDepot server instances to run in parallel, each writing to a different subdirectory. Create this directory:

    mkdir ~/.wattdepot3
    mkdir ~/.wattdepot3/server

## Customize the wattdepot-server.properties

The server reads properties on launch from a file named `wattdepot-server.properties` in the server subdirectory. A 
default properties file can be found in the wattdepot files you downloaded as 
`src/main/resources/wattdepot-server.properties`. Copy that file to your new server subdirectory (~/.wattdepot3/server 
if you are using the defaults), and rename it to `wattdepot-server.properties`.

After the file is copied into place, open it in your favorite text editor and change or uncomment any lines you want. 
At a minimum, you will want to change the default administrator username and password.

## Run the server

Change to the top level of the wattdepot distribution you downloaded. Run the server:

    $ java -cp wattdepot-*version*.jar org.wattdepot.server.WattDepotServer
    

# 3. Administration

## A. Log in to WattDepot as administrator

Once the WattDepot Server is running, point your browser to the Wattdepot administration page.

    http://<WattDepot_server_url>/wattdepot/admin/

The browser will ask you for a username and password. Use the Administrator's username and password.

{% include responsive-img.html photo="admin-password.png" %}

Log in as the administrator and you will see the following "admin interface". Here you can create the organizations 
and users.

{% include responsive-img.html photo="wattdepot-admin-start.png" %}

### I. Create an Organization

To create a new organization click the plus button on the right side of the screen. You will see the Add Organization
 dialog box.
 
{% include responsive-img.html photo="add-org-dialog.png" %}

Clicking on "Save Changes" will create the organization.

{% include responsive-img.html photo="uhm-defined.png" %}

### II. Create a User

Switch over to the Users tab to start defining users.

{% include responsive-img.html photo="user-admin.png" %}

To create a new user click the plus button on the right side of the screen. You will see the Add User dialog box.
 
{% include responsive-img.html photo="add-user.png" %}

Remember to select the organization for your new user and hit "Save Changes".

{% include responsive-img.html photo="cmoore-defined.png" %}

### III. Log out as adminstrator

Close your browser to log out from WattDepot. 

## B. Log in as User

Restart your browser and go to your organizations administration page.

    http://<WattDepot_server_url>/wattdepot/<organization_id>/
    
The browser will ask you for your username and password. Use the user's id and password you just created.

{% include responsive-img.html photo="org-admin.png" %}

The organization id is in the upper right-hand corner. You have tabs for defining Depositories, Sensors, 
SensorGroups, Collection Process Definitions, and Measurement Pruning Definitions.

### I. Create a Depository

Depositories hold measurements of a given [Measurement Type](measurementtypes.html). To create a depository click the
 blue plus symbol.
 

### II. Create a Sensor

### III. Create a Collection Process Definition

### IV. Start the collector

### V. View the data

---
layout: userguide
title: Quick Start
---
# 0. Introduction

This Quick Start guide will lead you through the process of downloading, configuring, and running WattDepot to collect sensor data. If you are interested in modifying or extending WattDepot, check out the [Developer Guide](/developerguide/developerguide.html).

# 1. Prerequisites

## Operating System

The core development team has developed and run WattDepot on OS X, Windows 7, and Linux, so any of those platforms should be able to run WattDepot.

## Java

WattDepot requires [Java Platform Standard Edition] (http://www.oracle.com/us/technologies/java/standard-edition/overview/index.html) version 1.7 or above. If do not have Java installed, you can get it from the previous link. Oracle provides both a JRE (Java Runtime Environment) and a JDK (Java Development Kit). If you only want to run WattDepot, you can download the JRE. If you plan to do development on WattDepot, download the JDK.

To check the version of Java installed on your computer type `java -version` from the command line. You should see something like this (but the version number will probably be higher):

    $ java -version
    java version "1.7.0_40"
    Java(TM) SE Runtime Environment (build 1.7.0_40-b43)
    Java HotSpot(TM) 64-Bit Server VM (build 24.0-b56, mixed mode)

## PostgreSQL

Currently, WattDepot is using [Hibernate](http://hibernate.org/) for the persistence layer and the [PostgreSQL] (http://www.postgresql.org/) database for storage. You can configure Hibernate to use other database backends, but we do not have experience with other backends at this time.

If you do not have PostgreSQL installed already, you will have to [download and install it](http://www.postgresql.org/download/) before running WattDepot. We recommend version 9.1 or higher. If you only plan to use PostgreSQL with WattDepot, you might find the graphical installer to be the easiest option.

If you already have PostgreSQL, one way to check the version is using the command line client tool `psql`:

    $ psql --version
    psql (9.2.4)

However, it is possible to have the `psql` client installed without the actual database server (such as on OS X Mountain Lion). In that case you might see an error like the following:

    $ psql
    psql: could not connect to server: No such file or directory
	Is the server running locally and accepting
	connections on Unix domain socket "/var/pgsql_socket/.s.PGSQL.5432"?

In this case, you should [download PostgreSQL](http://www.postgresql.org/download/) to get the server as well.

### Creating WattDepot PostgreSQL user and database

Once you have PostgreSQL installed, you will need to create a user and database so that WattDepot can store things in PostgreSQL. You can do this by connecting to the database as the `postgres` superuser with the `psql` command line tool:

    $ psql -U postgres

    psql (9.3.4)
    Type "help" for help.

    postgres=# CREATE USER 'your-user-name' WITH PASSWORD 'put-your-password-here';
    CREATE ROLE
    postgres=# CREATE DATABASE wattdepot;
    CREATE DATABASE
    postgres=# GRANT ALL PRIVILEGES ON DATABASE wattdepot to 'your-user-name';
    GRANT
    postgres=# \q

Note that by default, PostgreSQL will only allow connections from the local host, but will allow connections to any PostgreSQL user without a password (`trust` authentication mode). This is probably acceptable for experimenting with WattDepot, but for a production instance you would probably want to turn on password authentication.

You can test that the new user and database has been created properly by connecting to it. Note that we do not need to specify a password since we are using trust authentication from the local system:

    $ psql -U 'your-user-name'

    psql (9.3.4)
    Type "help" for help.

    postgres=# \l
                                   List of databases
       Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges    
    -----------+----------+----------+-------------+-------------+------------------------
     postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
     template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres           +
               |          |          |             |             | postgres=CTc/postgres
     template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres           +
               |          |          |             |             | postgres=CTc/postgres
     wattdepot | your-user| UTF8     | en_US.UTF-8 | en_US.UTF-8 |
    (4 rows)
    postgres=# \q
	$

# 2. Installation

## Download WattDepot

Download the latest release of WattDepot from the [releases](https://github.com/wattdepot/wattdepot/releases) page.

## Set up WattDepot server home directory

WattDepot stores all its files in a server home directory. By default, this will be a *.wattdepot3* directory in your home directory. If you want to create the *.wattdepot3* directory elsewhere, set the `wattdepot.user.home` Java property to the desired parent directory. The name *.wattdepot3* directory name itself is not configurable.

Within the server home directory are subdirectories that store all the data for a particular server instance. The default subdirectory is server, but this can be changed on the command line with the -d argument. This allows multiple WattDepot server instances to run in parallel, each writing to a different subdirectory. Create this directory:

    mkdir ~/.wattdepot3
    mkdir ~/.wattdepot3/server

## Customize the wattdepot-server.properties

The server reads properties on launch from a file named `wattdepot-server.properties` in the server subdirectory. A default properties file can be found in the wattdepot release file you downloaded as `wattdepot-server.properties`. Copy that file to your new server subdirectory (~/.wattdepot3/server if you are using the defaults), and rename it to `wattdepot-server.properties`.

{% highlight bash %}
# wattdepot-server.properties This file is part of WattDepot.
#
# Copyright (C) 2013  Cam Moore
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

## WattDepot server properties files. WattDepot looks for this file in
## the directory <wattdepot-server.homedir>/.wattdepot3/server/. Copy this file to that
## directory and edit the properties to match your installation.

## homedir is where the .wattdepot3 directory is. This directory holds the properties and log files.
## Defaults to current user's home directory.
#wattdepot-server.homedir=/home/user/

## Administrator account, has full access to all WattDepot data
## Change these to someting unique and secure!
wattdepot-server.admin.name=admin
wattdepot-server.admin.password=nimda

## Backend storage implementation.
#wattdepot-server.wattdepot.impl=org.wattdepot.server.depository.impl.hibernate.WattDepotPersistenceImpl

## TCP port used for incoming connections defaults to 8192
#wattdepot-server.port=8192

## TCP port used for testing defaults to 8194
#wattdepot-server.test.port=8194

## Database connection driver. Default to org.postgresql.Driver
wattdepot-server.db.connection.driver=org.postgresql.Driver

## WattDepot database uri. format postgres://<db.username>:<db.password>@<database-host>:<database-port>/<database-name>
wattdepot-server.database.url=postgres://myuser:secret@localhost:5432/wattdepot


## show the sql statements
#wattdepot-server.db.show.sql=false

## Automatically validates or exports schema DDL to the database when
## the SessionFactory is created. With create-drop, the database schema
## will be dropped when the SessionFactory is closed explicitly.
## Valid values are validate | update | create | create-drop
## defaults to update.
#wattdepot-server.db.update=update

## Enable logging. Defaults to true.
#wattdepot-server.enable.logging=true

## Set the logging level. Defaults to INFO.
#wattdepot-server.logging.level=INFO
{% endhighlight %}

After the file is copied into place, open it in your favorite text editor and change or uncomment any lines you want. 
At a minimum, you must change:

  * The `wattdepot-server.admin.name`, you can change this value in the wattdepot-server.properties file or create the environment variable `WATTDEPOT_ADMIN_NAME`.
  * The `wattdepot-server.admin.password`, you can change this value in the wattdepot-server.properties file or create the environment variable `WATTDEPOT_ADMIN_PASSWORD`.
  * and the `wattdepot-server.database.url`, you must change this url to use the psql user and password you created in PostgreSQL. Replace &lt;myuser&gt; with the user name and &lt;secret&gt; with the user's password. You can create the environment variable `WATTDEPOT_DATABASE_URL`.

The environment variables will override the values in the `wattdepot-server.properties` file.

If these three values are not defined WattDepot will not run.

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

Fill out the information. Only the administrator can change the users' passwords. The administrator will have to tell 
the users their passwords. Remember to select the organization for your new user and hit "Save Changes".

{% include responsive-img.html photo="cmoore-defined.png" %}

### III. Log out as adminstrator

Close your browser to log out from WattDepot. You have to exit your browser to reset the login information (username and password).

## B. Log in as User

Restart your browser and go to your organizations administration page.

    http://<WattDepot_server_url>/wattdepot/<organization_id>/
    
In our example the University of Hawaii, Manoa organization's administration page is:

    http://wattdepot-test-deploy.herokuapp.com/wattdepot/uhm/

The browser will ask you for your username and password. Use the user's id and password you just created.

{% include responsive-img.html photo="org-admin.png" %}

The organization id is in the upper right-hand corner. You have tabs for defining Depositories, Sensors, SensorGroups, Collection Process Definitions, and Measurement Pruning Definitions.

Even though WattDepot is focused on energy and power, we are going to show you how to create a temperature depository, National Oceanic and Atmospheric Administration web sensor, and a Collector Process Definition to gather temperature data. The reason for this is you can follow the steps for your own installation and start collecting data without having your own smart meters installed. 

### I. Create a Depository

Depositories hold measurements of a given [Measurement Type](measurementtypes.html). To create a depository click the blue plus symbol. 
 
{% include responsive-img.html photo="add-depository-dialog.png" %}
 
We gave the depository a descriptive id and name and selected the Measurement Type "Temperature (F)" for this depository. Once you hit "Save Changes" you will see the defined depository.

{% include responsive-img.html photo="temperature-defined.png" %}

### II. Create a Sensor

Switch to the "Sensors" tab to define our NOAA Weather Sensor.

{% include responsive-img.html photo="sensor-admin.png" %}

Press the blue plus sign to bring up the Add Sensor Dialog Box.

{% include responsive-img.html photo="add-sensor-dialog.png" %}

The url for the sensor is the current observation XML page for the Honolulu Airport. If you go to [w1.weather.gov/xml/current_obs/](http://w1.weather.gov/xml/current_obs/) you can select the state and weather station you are interested in. We choose the NOAA Weather sensor model. For this sensor we do not need to supply any properties.

{% include responsive-img.html photo="noaa-sensor-defined.png" %}

In this guide we will not be creating any Sensor Groups. Sensor Groups are used to aggregate measurements together.

### III. Create a Collection Process Definition

We need to create a Collector Process Definition to tell our collectors:
 
 * what measurements we want to collect, 
 * how often to collect the measurements and 
 * where to store the measurements.

Switch to the Collector Process Definitions tab.

{% include responsive-img.html photo="cpd-admin.png" %}

And press the blue plus sign to open the Add Collector Process Definition dialog.

{% include responsive-img.html photo="add-cpd-dialog.png" %}

* We provide a descriptive id and name. 
* Choose the sensor that we are going to collect from. In this example there is only one choice in our selection box. 
* Enter the polling interval in seconds. NOAA updates their current observations once an hour so we will poll their website once an hour or every 3600 seconds.
* Choose the depository to store the measurements in. In this example there is only one choice in our selection box.
* Finally, provide the property name and value that we want to collect from. The property "registerName" tells the collector process that we are interested in values from that XML field in the XML data.
  
{% include responsive-img.html photo="hono-temp-defined.png" %}


### IV. Start the collector

We now need to start the collector process.  WattDepot comes with a Java program that will query a WattDepot server and start all the Collectors for a given Organization.

Open a command prompt and change directory to where you have the WattDepot jar file. Run the following command to see the command line options for the AllOrganizationCollectors program.

{% highlight bash %}
10:59 [~/wattdepot] $ java -cp wattdepot-*version*.jar org.wattdepot.client.http.api.collector.AllOrganizationCollectors -h
usage: AllOrganizationCollectors
 -d,--debug                  Displays debugging information.
 -h                          Usage: AllOrganizationCollectors -s <server uri> -u
                             <username> -p <password> -o <orgId> [-d]
 -o,--organizationId <arg>   User's Organization id.
 -p,--password <arg>         Password
 -s,--server <arg>           WattDepot Server URI.
                             (http://server.wattdepot.org)
 -u,--username <arg>         Username
11:00 [~/wattdepot] $ 
{% endhighlight %}

To start the collector processes we need to supply:
 
 * The WattDepot Server's URL, in our example it is http://wattdepot-test-deploy.herokuapp.com/.
 * The user's id, in this case cmoore.
 * The user's password, hidden.
 * The user's organization id, in this case uhm

The following is an example of starting the processes.

{% highlight bash %}
11:08 [~/wattdepot] $ java -cp wattdepot-*version*.jar org.wattdepot.client.http.api.collector.AllOrganizationCollectors -s http://wattdepot-test-deploy.herokuapp.com/ -u cmoore -p ****** -o uhm -d
All Organization Collectors:
    WattDepotServer: http://wattdepot-test-deploy.herokuapp.com/
    Username: cmoore
    OrganizationId: uhm
    Password: ********
Aug 14, 2014 11:08:19 AM org.wattdepot.client.ClientProperties initializeProperties
INFO: Loading Client properties from: /Users/carletonmoore/.wattdepot3/client/wattdepot-client.properties
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting honolulu-temperature
Loading Client properties from: /Users/carletonmoore/.wattdepot3/client/wattdepot-client.properties
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Loading Client properties from: /Users/carletonmoore/.wattdepot3/client/wattdepot-client.properties
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Starting the Apache HTTP client
Stopping the HTTP client
Started polling Weather in Honolulu sensor at 2014-08-14T11:08:25.425-10:00
Starting the Apache HTTP client
Stopping the HTTP client
Measurement [id= honolulu-noaa1408050505868°f, sensorId=honolulu-noaa, timestamp=2014-08-14T11:08:25.868-10:00, value=87.0, units=°F]
[clip]

{% endhighlight %}

As long as you are running this Java program it will collect measurements from all the organization's defined collector process definitions and send those measurements to the WattDepot Server.

### V. View the data

There are two ways to see if WattDepot is collecting measurements.

1. Go to the summary page. It shows a row for each Depository, Sensor combination that is reporting data. The top line of the page shows the number of depositories and sensors that are collecting measurements. It also shows the total number of measurements stored in WattDepot.

{% include responsive-img.html photo="summary-page.png" %}

  Pressing the "Show Details" button will update the current statistics for the Depository-Sensor pair.
  
2. Go to the chart page. On this page, you can request to see graphs of the data stored in WattDepot.

{% include responsive-img.html photo="chart-page.png" %}

  * Select the depository then the sensor. This will update the start time and end time choices indicating the earliest and latest data available for that depository-sensor combination.
  * Choose the start time and end time.
  * Choose the type if interpreted value you want to view.
  
    * **Point Values**: Give you the interpolated value at the sample times.
    * **Interval Values**: Give you the change in value at the sample times. (e.g. The end of sample interval value - start of sample interval value)
  * Choose the sample frequency. The sample frequency determines the number of samples to take. Be careful, large time windows with small sample frequency can lead to very many samples and very slow performance.
  
  Press the Visualize button to see the graph. If you want to graph more than one set of measurements press the blue plus sign. This will give you another row to select the measurements, time range, type of value, and sample frequency. 
  
{% include responsive-img.html photo="temperature-graph.png" %}


---
layout: userguide
title: Developer Guide
---

# Prerequisites

## Operating System

The core development team has developed and run WattDepot on OS X, Windows 7, and Linux, so any of those platforms 
should be fine for development. We do not recommend Windows, instead use [Vagrant](http://www.vagrantup.com/) to provide 
a good development environment.

## Java

WattDepot requires [Java Platform Standard Edition] (http://www.oracle.com/us/technologies/java/standard-edition/overview/index.html) 
version 1.7 or above. If do not have Java installed you can get it from the previous link. Note that you will want the 
JDK (Java Development Kit) rather than the JRE (Java Runtime Environment) since you will be developing. To check the 
version of Java installed on your computer type `java -version` from the command line.

    $ java -version
    java version "1.7.0_40"
    Java(TM) SE Runtime Environment (build 1.7.0_40-b43)
    Java HotSpot(TM) 64-Bit Server VM (build 24.0-b56, mixed mode)

## Maven

WattDepot uses [Maven3] (http://maven.apache.org/index.html) as the build system. If you don't have Maven installed you 
can download it from the previous link. To check the version of Maven installed type `mvn -version` from the command line.

    $ mvn -version
    Apache Maven 3.1.1 (0728685237757ffbf44136acec0402957f723d9a; 2013-09-17 05:22:22-1000)
    Maven home: /usr/local/Cellar/maven/3.1.1/libexec
    Java version: 1.7.0_40, vendor: Oracle Corporation
    Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_40.jdk/Contents/Home/jre
    Default locale: en_US, platform encoding: UTF-8
    OS name: "mac os x", version: "10.9", arch: "x86_64", family: "mac"

## Integrated Development Environment

It is strongly recommended that you have a good Java IDE.

## PostgreSQL

Currently, WattDepot is using Hibernate for the persistence layer and the [PostgreSQL] (http://www.postgresql.org/) 
database for storage. We recommend version 9.1 or higher. You can configure Hibernate to use other database backends, 
but we do not have experience with other backends at this time. To check the version of PostgreSQL type `psql -version` 
at the command line.

    $ psql --version
    psql (9.2.4)

This actually checks the version of the PostgreSQL client.

## Git

WattDepot uses Git and GitHub for our version control. You can install Git from [git-scm.com] (http://git-scm.com/downloads). 
To check the version of git you have installed type `git --version` from the command line.

    $ git --version
    git version 1.8.3.4 (Apple Git-47)

If you prefer a graphical interface to Git, GitHub has nice Git clients for [OS X](http://mac.github.com/) and 
[Windows](http://windows.github.com/) that can make your life easier. Since WattDepot is hosted at GitHub, the graphical 
GitHub client makes it very easy to work with Git and is highly recommended.


# Downloading Sources

The WattDepot source code and documentation are hosted on [GitHub](http://github.com/), and are managed using 
[Git](http://git-scm.com/), which you should have installed as part of the [Prerequisites](#Prerequisites). 
You can clone the repository to your local system using either the command line or a GUI tool:

## Command line

    $ git clone git@github.com:wattdepot/wattdepot.git

## GUI and IDE

WattDepot's Git repository may be accessed using many different [client programs and plug-ins]
(http://git-scm.com/downloads). As mentioned above, we like the free clients provided by GitHub: 
[GitHub for OS X](http://mac.github.com/) and [GitHub for Windows](http://windows.github.com/). 
On the [WattDepot project page](http://github.com/wattdepot/wattdepot) there is a button labeled "Clone in 
Desktop" that will automatically launch your GitHub client and start the clone operation.

## Contributing to WattDepot

If you plan to submit changes back to WattDepot, rather than cloning the WattDepot repository, you should first fork the 
repository on GitHub, and then clone it to your local system. This way, you can submit pull requests for bugs or new 
features that you add. First you will need to [create a GitHub account](https://github.com/signup) if you don't have one 
already, and then follow the wonderful [GitHub forking tutorial](https://help.github.com/articles/fork-a-repo). You can 
read more about [pull requests at GitHub](https://help.github.com/articles/using-pull-requests).

# Building the System

WattDepot uses [Maven 3](http://maven.apache.org/index.html) as a build system. Maven is configured with the pom.xml 
file found at the root of the WattDepot source directory. We defined three profiles in the POM, *dev*, *rel*, and *heroku*. The default profile is *dev*, which runs all the QA goals, but does not build the release .tar.gz, .tar.bz2, or .zip distribution files. The *rel* profile should be used for creating releases, since it runs all the QA goals and builds the .tar.gz, .tar.bz2, and .zip distribution files.  The *heroku* profile is for deploying WattDepot to Heroku and doesn't run any of the QA goals.

Maven has a number of lifecycle phases - the most useful in this project are clean, compile, test, package, verify, and site.

  * `mvn clean` will delete the target directory.
  * `mvn compile` will compile the project.
  * `mvn test` will compile the project, then run JUnit and QA tests.
    * FindBugs
    * Checkstyle 
    * PMD
    * JavaDocs are created

    (Note: Running tests requires you to setup the wattdepot-server.properties file in order to supply the wattdepot-server.db.username property.)
  * `mvn package` will compile the project, run the tests, and then create a .jar file for a distribution
    * The .jar file
    * The POM zips everything into a distribution. (This step is only done if you use the *rel* profile. `mvn -P rel package`)
  * `mvn verify` runs the compile, test and package phases and ensures the result is valid
  * `mvn site` executes FindBugs, Checkstyle, PMD, and JavaDocs, then presents the results in an easy to read website 
  (located in target/site)

Rather than executing a whole phase, you can also run an individual goal. For example, to run FindBugs without doing 
anything else, use:

    $ mvn findbugs:check
    
The same works for `mvn pmd:check` and `mvn checkstyle:check`. However, note that this does not recompile the project. 
If you make changes to the source code, you should run `mvn compile` before running any other checks. 

Another useful tip is adding properties to Maven calls with the `-D` argument. For example, to run the package phase 
without running JUnit tests, use:
    
    $mvn package -DskipTests
    
This can be a great time saver if you've just run the test phase and now want to package. These properties can also 
affect the running of WattDepot. Say you want to test multiple configurations without changing the 
wattdepot-server.properties file between each test. Try:

    $ mvn test -Dwattdepot-server.db.username=test


# WattDepot Branching Model

## Centralized "truth" repository

There is one central "truth" repository, origin, which is located at [http://github.com/wattdepot/wattdepot]
(http://github.com/wattdepot/wattdepot). It is the central push-pull location. Developers can of course clone their 
own repository and do whatever they want locally, but our model controls the structure of this repo. 

## Two main branches: master and develop

There are two main branches:

  * [master](https://github.com/wattdepot/wattdepot/tree/master). The HEAD of master always reflects a 
  production-ready state. 

  * [develop](https://github.com/wattdepot/wattdepot/tree/develop). The HEAD of develop always reflects a state with 
  the latest delivered development changes for the next release.

When the source code in the develop branch reaches a stable point and is ready to be released, 
all of the changes should be merged back into master somehow and then tagged with a release number.

See [Branching Details](branching.html) for more information.

# Making a Release

Most implementation effort should be committed to the develop branch.  When appropriate, 
the develop branch will be merged into master and a release will be made.  This procedure is documented [here]
(release.html).  For the time being, Cam is the only one who will make releases. 

# Deploying to Heroku

We are thinking about using the [Heroku Button](https://blog.heroku.com/archives/2014/8/7/heroku-button) for 
deploying to Heroku. This should be implemented soon. Check back often to see when we complete this work.  


# Writing Collector Processes

TBW

# Writing Clients

TBW

## Developing an HTML Dashboard

TBW

# Implementing Persistence

TBW

# Evaluating Performance
## Goals

There are several goals for WattDepot performance evaluation. We want to answer the following questions.

1. What is the maximal throughput for Measurements?

2. What is the responsiveness, providing information about the Measurements and defined objects?

3. What happens to the system (throughput, responsiveness) after 24 hours, 7 days, ... of load?

4. What is the performance under a variety of simulated "real world" mixtures of puts and gets?

5. What are the answers to all of these questions on different platform installations (local machine, Mopsa, Heroku, ...)?



## Performance Evaluation Tools

We have provided several tools to help evaluate WattDepot's performance for several low level operations. These operations are:

* Put a measurement. Storing data in WattDepot.
* Get the earliest value. Information about the measurements in WattDepot.
* Get the latest value. This is typically how you would get the current power.
* Get a value at a given time. This is generally how you would get the power or temperature at a given time.
* Get a value from a start time to an end time. This is generally how you would calculate the Energy used.

In the package

    org.wattdepot.client.http.api.performance

you will find five programs:

    FindMeasurementThroughput    
    FindGetEarliestValueThroughput
    FindGetLatestValueThroughput
    FindGetValueDateThroughput
    FindGetValueDateDateThroughput

Each of the programs have the same command line arguments.

* -s <server URI>
: The URI to the WattDepot server. e.g. http://server.wattdepot.org:8192/

* -u <username>
: The User Id of a defined WattDepot user.

* -p <password>
: The User's password, used to connect to the WattDepot server.

* -o <organizationId>
: The User's organization id.

* -n <numSeconds>
: The number of seconds to run before checking the performance rates. We recommend values between 15 and 90. Shorter doesn't let the threads run often enough for a good average. Longer an you have to wait too long to see results.

The programs start a monitoring thread that wakes up every *numSeconds* and checks the average time it took to perform the operation. The monitoring thread then calculates a new rate for the number of operations per second. The new rate is the average of the previous average times. Since it is an average of averages, the rate should slowly converge on the maximum rate possible. The monitoring thread then starts up the new rate worker threads that perform the operation once a second.


### Using the tools

1. Install WattDepot following the [installation instructions](installationguide/installation). You need to install version 3.0.0-M9 or above.
2. For performance testing we recommend you reduce the logging to a minimum. In the wattdepot-server.properties file change the *wattdepot-server.loggin.level* to SEVERE.
3. [Start the WattDepot server](installationguide/installation#Run the server). 
4. Define an Organization and User to use in the performance testing. Remember the User's password, we'll use it later.
5. Choose the performance evaluation(s) you want to run, Measurements, Earliest value, Latest value, Value at a given time, or Value from one time to another. 
  * We do not recommend running more than three of these tools at the same time. They can overload your WattDepot server.
  * We recommend you run the Measurements with the Get Values. This simulates a more realistic scenario, getting values while measurements are being added to the Server.  

For example if you run the following:

    $ java -cp wattdepot-3.0.0-M9.jar org.wattdepot.client.http.api.performance.FindGetValueDateThroughput -s http://localhost:8192/ -u cmoore -p secret -o uh -n 15

You should see something like:

    Starting the Apache HTTP client
    Stopping the HTTP client
    Ave get value (date) time = 0.04042766666666667 => 25 gets/sec.
    Setting rate to 25
    Ave get value (date) time = 0.016198 => 62 gets/sec.
    Setting rate to 35
    Ave get value (date) time = 0.012042333333333334 => 83 gets/sec.
    Setting rate to 44
    Ave get value (date) time = 0.011197 => 89 gets/sec.
    Setting rate to 50
    Ave get value (date) time = 0.011237909090909092 => 89 gets/sec.
    Setting rate to 55
    Ave get value (date) time = 0.010520545454545454 => 95 gets/sec.
    Setting rate to 59

Every 15 second two more lines are added to the output.  The output tells you what the average time it to perform the operation was and how many operations/second this results in. The second line tells you what the rate the tool is setting to get the next set of performance data. 

The above data shows that we can perform about 90 get value at a given time operations per second.

Record your results.


## Real World Scenarios

We've used WattDepot in two distinct real world situations.  Supporting the Kukui Cup at University of Hawaii, Manoa and Hawaii Pacific University. <link to documentation?>

### Kukui Cup: University of Hawaii, Manoa

#### Measurement Generation

40 Sensors producing two measurements, power and energy, every 15 seconds (20 measurements/second).

#### WattDepot Queries

Makahiki status page generates queries about the latest measurement for each Lounge every 10 seconds. There are 20 Lounges (3.333 queries/second). The JavaScript calls *&lg;server_url&gt; + "/depository/power/value/gviz/?sensor=" + source + "&latest=true";* for each lounge.

Additionally, there are several activities that ask the players to explore the energy and power information stored in WattDepot.

Heat Map, Hot Spot, and Visualizer calls   *host_uri + '/depository/' + dataType + '/values/gviz/?sensor=' + source[i] +'&start=' + startTime + '&end=' + endTime + '&interval='+interval*
PowerMeter calls *&lg;server_url&gt; + "/depository/power/value/gviz/?sensor=" + source + "&latest=true";*

Approximately 350 players times X % who do the activities equals N queries in a typical two week challenge. Based upon the 2014 Kukui Cup there were 61, 22, 21, 44 = 148 players who completed the activities.

### Kukui Cup: Hawaii Pacific University

#### Measurement Generation

6 Sensors producing two measurements, power and energy, every 10 seconds (2 measurements/second).

#### WattDepot Queries

Makahiki status page generates queries about the latest measurement for each Team every 10 seconds. There are 6 teams (1 query/second). 

Additionally, there are several activities that ask the players to explore the energy and power information stored in WattDepot.

Heat Map, Hot Spot, and Visualizer calls   

    <server_url>/depository/<depository_id>/values/gviz/?sensor=<sensor_id>&amp;start=<startTime>&end=<endTime>&interval=<interval>
    
PowerMeter calls 

    <server_url>/depository/<depository_id>/value/gviz/?sensor=<sensor_id>&latest=true


### Aarhus University

#### Measurement Generation

#### WattDepot Queries



## Results
[Feb 12th 2014 Mopsa Server](results-mopsa.html)


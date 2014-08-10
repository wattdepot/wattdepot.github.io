---
layout: userguide
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
file found at the root of the WattDepot source directory. Maven has a number of lifecycle phases - the most useful in 
this project are clean, compile, test, package, verify, and site.

  * `mvn clean` will delete the target directory.
  * `mvn compile` will compile the project.
  * `mvn test` will compile the project, then run JUnit and QA tests. (Note: the postgres storage implementation 
  requires you to setup the wattdepot-server.properties file in order to supply the wattdepot-server.db.username 
  property.)
    * FindBugs
    * Checkstyle 
    * PMD
    * JavaDocs are created
  * `mvn package` will compile the project, run the tests, and then create .jar for a distribution
    * The .jar file
    * The POM zips everything into a distribution
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

# Making a Release

# Deploying to Heroku

# Writing Collector Processes

# Writing Clients

## Developing an HTML Dashboard

# Implementing Persistence


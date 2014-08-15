---
layout: userguide
title: User Guide
---

# Domain Model

## Administrator

Every WattDepot server installation requires the definition of a single administrator (whose name by default is "root" 
but which can be changed by the installer or when the server is run) and associated password (with no default value, and 
which must be specified by the installer). This administrator is automatically placed in the predefined organization 
named "admin", which grants the administrator special capabilities not available to users.  These capabilities include 
the ability to:

* add and delete organizations
* add and delete users

## Organization

Users, sensors, sensor groups, collectors, depositories, and measurements all belong to a single "organization".  
Organizations create a namespace for WattDepot entities. For example, a single WattDepot server might hold data from two 
organizations, "University of Hawaii" and "Hawaii Pacific University". Organizations are defined with an id, which is a 
short alphabetic abbreviation suitable for identifying the organization in URLs. The organization "University of Hawaii" 
might have the id "uh" and "Hawaii Pacific University" might have the id "hpu".

The organization id is generally the first element of a WattDepot API call. For example, to retrieve information about 
the University of Hawaii repositories, you might invoke:

```
GET http://server.wattdepot.org/wattdepot/uh/depositories/
```

To get Hawaii Pacific University's sensors you would use:

```
GET http://server.wattdepot.org/wattdepot/hpu/sensors/
```

You must be logged in as an HPU user to see HPU's entities.

In general, you can only manipulate entities created within your organization. There is one exception: the pre-defined 
organization with the id "public" which defines "global" entities such as measurement types and sensor models. 

## User

To use a WattDepot server, begin by requesting a user name and password from the server's administrator. You must also 
indicate if you are joining an existing organization or if you wish a new organization to be created on the server. Once 
you have obtained these credentials, you can:

* Add and delete depositories
* Add and delete sensors (and their associated models, and properties)
* Add and delete sensor groups
* Add and delete collector process definitions
* Add and delete measurements (if you are doing this manually)
* Add and delete measurement pruning definitions
* Query the server for measurements and measured values in a variety of ways

All of these manipulations occur within your organization.

<hr/>

## Sensor

Sensors represent a device that measures (or predicts) a physical phenomena. Sensors have the following properties:

* URL: If available for this meter, the URL represents an IP address where a collector can programmatically access 
  measurement(s) made by this sensor. 
* Sensor Model: A description of the sensor. This can potentially include its protocol, version, etc.
* Properties: A potentially empty list of key-value pairs that provide additional information about this sensor.

Note that some physical devices can measure multiple phenomena (such as energy from multiple floors, or both energy and 
power, or energy and temperature).  In these cases, the physical device will be represented by multiple Sensors. 

## Sensor Group

Users often find it convenient to request aggregations of sensor data. For example, a user might wish to know the 
energy consumed by a building, which might involve aggregating the energy measurements associated with Sensors located 
on each floor of the building.

By defining a Sensor Group and associating individual Sensors with it, users can obtain aggregate measurements.

All Sensors in a Sensor Group must collect data of the same Measurement Type.

## Collector

Collectors are processes that contact a Sensor, obtain Measurements from it, then store this data in a WattDepot server.

One implementation of a Collector might be a software program that uses the IP address of a Sensor to contact it and 
request a measurement value.  After obtaining that value, it then contacts a WattDepot server and uses the HTTP API to 
store that value in the server.

An alternative implementation of a Collector is a person who reads a meter manually to obtain a measurement, then uses a 
GUI interface to a WattDepot server to store that value.

In order for a Collector to do its job, it must be able to obtain information about the Sensor it is supposed to 
contact. It is also useful for the Collector to know how often it should collect data, what data to collect, and where 
to store it in the server. WattDepot represents this information with the "Collector Process Definition" entity.

In prior versions of WattDepot, the collector software programs hard-wired information about when, where, and how often 
to collect data into their source code. In this version, we expect Collector programs to first query the WattDepot 
server to obtain their process definition, and use that definition to determine what to do.  By representing the task 
associated with a Collector in the server explicitly as process definition, the server can provide significant new 
capabilities to users. For example, a server can now understand how frequently to receive data from a Collector, and 
signal the user if data is not being received as expected.

## Measurement Type

WattDepot provides all the units of measurement provided by the [JScience API](http://www.unitsofmeasurement.org/). 
Supported units include both [SI Units](http://jscience.org/api/javax/measure/unit/SI.html) and 
[Non-SI Units](http://jscience.org/api/javax/measure/unit/NonSI.html).

In addition, it is possible in WattDepot to define new Measurement Types. Adding a new Measurement Type requires changes 
to the source code and rebuilding of the system.

Measurement Types are members of the "public" organization, which makes them visible to all users of a server.

## Depository

Depositories store measurements made by Sensors and collected by Collectors.

Depositories can store Measurements made by different Sensors and different Collectors, but all Measurements must be of 
one and only one Measurement Type.

## Measurement

Measurements are made by Sensors and collected by Collectors.  A Measurement includes the following information:

* the Sensor that made the Measurement,
* a timestamp indicating when the Measurement was made by the Sensor,
* the numeric value of the Measurement,
* the Measurement Type.

Sensors can either measure phenomena as they occur, or predict phenomena that may or may not occur.  Sensors can provide 
measurements for phenomena with a timestamp in the future.  It is the responsibility of users to check the sensor 
associated with a measurement to determine its validity and meaning.

## Interpolated Value

Interpolated Values are different from Measurements. Measurements are the "raw data" in WattDepot.  For example, a 
Collector may obtain a power measurement from a Sensor every 10 seconds.

Unfortunately, WattDepot users often want to know a value at a specific timestamp regardless of whether or not a 
Collector has obtained a Measurement at that timestamp.  For example, a user might want a visualization that shows the 
power being consumed by a building every hour on the hour.

For this use case, WattDepot provides Interpolated Values.  Interpolated Values differ from Measurements in that 
WattDepot will interpolate in the cases where there does not exist a Measurement corresponding to the precise timestamp 
requested by the user. (In real life, this is the case virtually all of the time).

### Kinds of Interpolated Values

WattDepot supports two kinds of Interpolated Values: point values and difference values. 

Point values are calculated for a single timestamp. If there exists a Measurement corresponding to the timestamp, 
WattDepot will return that value. If the timestamp is between two Measurements, WattDepot
calculates the linear interpolated value and returns it.  If the timestamp is not bounded by two Measurements, then 
WattDepot will return an error.

For difference values, WattDepot gets the point values for the starting timestamp and the ending timestamp and returns 
the difference.

It is the responsibility of the user to assess the validity of Interpolated Values returned by WattDepot. WattDepot will 
return the type of Interpolated Value the user requests even if it makes no physical sense.

## Measurement Pruning Definitions

WattDepot will store all the measurements collected. However the number of measurements can grow quickly. It is often 
the case that the user is not interested in old fine grained data, so we created the concept of Measurement Pruning. 
Measurement Pruning Definitions (MPD)s allow the user to define the desired frequency of measurements for old data.  

For example, in the Kukui Cup, we want energy and power data every 15 seconds, but only desire a five minute frequency 
for data that is older than 1 day. This greatly reduces the number of measurements WattDepot needs to store.  

MPDs have:

* A depository id. This tells the pruning process which depository to select the measurements from.
* A sensor id. This tell the pruning process which sensor created the measurements. The sensor id can be a sensor group
  id.
* An ignore window. This tell the pruning process to ignore measurement less than window days old.
* A collection window. This tell the pruning process to only consider measurements older than the ignore window in days, 
  but younger than ignore window + collection window days old.
* A minimum gap in seconds. The minimum gap tells the pruning process to remove measurements that have a gap less than
  the minimum.


# Administration

See the [Quick Start]({{ site.baseurl }}/quick_start/quickstart.html#3.Administration) guide for information about 
adding Depositories, Sensors, and Collector Process Definitions.

## Sensor Groups

## Measurement Pruning Definitions

## Loading From CSV File

The administration UI is fine for adding a few items. If you want to add many sensors you should use the Organization
Domain tool.  WattDepot provides a tool for defining an organization's domain model in a CSV file.  Then you can 
upload the file to the WattDepot Server, thus saving you lots of tedious web clicking and typing.
 
The CSV file has the following format.
 
### A section for Depositories

The Depositories section should start with a comment showing the structure of each line defining the depositories. 
The example comment is:
  
     # Depositories: 'Depository', Name, MeasurementType Name, MeasurementType Unit, OrgId

The lines defining the depositories must have the following information in the given order:

* The String "Depository" followed by a comma
* The Name of the depository followed by a comma
* The MeasurementType Name followed by a comma
* The MeasurementType Unit followed by a comma
* The Organization Id
  

    Depository,Power,Power (W),W,uhm
 
Defines the 'Power' depository with the MeasurementType Power (W), the unit is W and the organization id is uhm.

We recommend you use the Organization Administration UI to create your Depositories since the MeasurementType Unit is
 often a non-standard character like the degree symbol.
 
### A section for Sensors

The Sensors section should start with a comment showing the structure of the lines.

     # Sensors: 'Sensor', Name, URI, ModelId, OrgId, num properties, prop1.key, prop1,value, ...,
 
The lines defining the sensors must have the following information in the given order:

* The String "Sensor" followed by a comma
* The name of the sensor followed by a comma
* The URI for the sensor followed by a comma
* The Sensor Model Id followed by a comma
* The Organization Id followed by a comma
* The number of properties for the sensor followed by a comma
* Property, Key Value pairs for the number of properties

<pre>  
Sensor,Honolulu NOAA,http://w1.weather.gov/xml/current_obs/display.php?stid=PHNL,noaa-weather,uh,0,
</pre>
    
Defines the Honolulu NOAA sensor of type noaa-weather and 0 properties

    Sensor,Lokelani 10th Telco,http://192.168.105.39,shark,uh,1,registerName,Student_Lounge
     
Defines the Lokelani 10th Telco sensor of type shark with 1 property, whose key is 'registerName', 
value of 'Student_Lounge'.

### A section for Sensor Groups

The Sensor Groups should start with a comment showing the definition of the Sensor Group lines.

     # SensorGroups: 'SensorGroup', Name, OrgId, num sensors, sensorId1, sensorId2, ...,

The lines defining the sensor groups must have the following information in the given order:

* The String "SensorGroup" followed by a comma
* The Name of the SensorGroup followed by a comma
* The Organization Id followed by a comma
* The number of sensors in the group followed by a comma
* The Sensor Ids for the members of the group followed by a comma

<pre>
SensorGroup,Ilima A,uh,2,ilima-4th-telco,ilima-4th-lounge,
</pre>

Defines the 'Ilima A' Sensor group consisting of two sensors on the fourth floor.

<pre>
SensorGroup,Ilima Total,uh,10,ilima-4th-telco,ilima-4th-lounge,ilima-6th-telco,ilima-6th-lounge,ilima-8th-telco,ilima-8th-lounge,ilima-10th-telco,ilima-10th-lounge,ilima-12th-telco,ilima-12th-lounge,
</pre>

Defines the 'Ilima Total' group with 10 sensors. 
     
### A section for Collector Process Definitions

The Collector Process Definitions section should start with a comment showing the format of the CPD lines.

     # CollectorProcessDefinitions: 'CollectorProcessDefinition', Name, SensorId, Polling, DepositoryId, OrgId, num properties, prop1.key, prop1.value, ...,

The CPD definition lines have the following format:
     
* The String "CollectorProcessDefinition" followed by a comma
* The name of the CPD followed by a comma
* The Sensor Id to poll followed by a comma
* The polling interval in seconds followed by a comma
* The Depository Id to send the measurements to followed by a comma
* The Organization Id followed by a comma
* The number of properties followed by a comma
* Property, Key Value pairs for the number of properties

<pre>
CollectorProcessDefinition,Ilima 4th Telco Energy,ilima-4th-telco,15,energy,uh,0,
</pre>

Defines the 'Ilima 4th Telco Energy' collector using the ilima-4th-telco sensor and the energy depository with a 
polling interval of 15 seconds with no properties.

<pre>
CollectorProcessDefinition,Honolulu Temperature,honolulu-noaa,3600,temperature,uh,1,registerName,temp_f
</pre>

Defines the 'Honolulu Temperature' collector using the honolulu-noaa sensor and the temperature depository with a 
polling interval of one hour or 3600 seconds. It also has one Property key = 'registerName', value = 'temp_f'.
     
### A section for Measurement Pruning Definitions
 
     # MeasurementPruningDefinitions: 'MeasurementPruningDefinition', Name, DepositoryId, SensorId, OrgId, ignore window days, collect window days, minimum gap seconds

# Data Visualization


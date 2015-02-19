---
layout: userguide
title: HTTP API
---
# HTTP API

WattDepot provides a language-independent interface to its server using HTTP. To understand this interface,
it helps to be familiar with the [WattDepot Domain Model](../userguide/userguide.html).

Path elements that have '{}' surrounding them are variables.  They are generally substituted with an ID for the type. For example {org-id} should be replaced with a valid organization id. The URLs below do not include the WattDepot server's address.

*(We need to figure out how to document the arguments and return values from these calls. One possibility is to provide more complete documentation in the JavaDocs, and then provide a (stable) link to the latest released JavaDoc on this page.)*

*(Also, it might be useful to compare this API with the [WattDepot 2.0 API](https://code.google.com/p/wattdepot/wiki/RestApi), to ensure that we are not missing anything important, and that we are documenting the appropriate features. Some things I notice are missing from this page: (1) Access control info. (Admin vs. regular user); (2) health API call; (3) sample return values; (4) HTTP status codes.)*


---------------
## Organization

See the [Organization domain model description](../userguide/userguide.html#organization) for details about this concept.

Organization operations require administrator credentials.

### GET /wattdepot/admin/

Retrieve the web interface for managing Organizations and Users associated with a WattDepot server. Only administrators can do this.

### GET /wattdepot/admin/organizations/

Retrieve the list of defined organizations in a WattDepot server as a JSON string.

### GET /wattdepot/{org-id}/organization/{org-id}

Retrieve the Organization with the given {org-id} as a JSON string. This call allows members of the organization to retrieve their own organization.

### PUT /wattdepot/admin/organization/

Create a new organization. The request entity must be a valid JSON string representation of the new Organization. Only administrators can do this.

### POST /wattdepot/admin/organization/{org-id}

Update the organization with the given {org-id}. The request entity must be a valid JSON string representation of the updated Organization.  Only administrators can do this. The *id* of the organization is fixed and cannot be changed after it is created.

### DELETE /wattdepot/admin/organization/{org-id}

Delete the organization. Only administrators can do this. **WARNING**, this operation deletes all Users, Sensors, Collector Process Definitions, Depositories and Measurements belonging to this organization.

### GET /wattdepot/{org-id}/

Retrieve the web interface for managing an organization associated with a WattDepot server. 

-------------
## User

See the [User domain model description](../userguide/userguide.html#user) for details about this concept.

### GET /wattdepot/admin/users/

Retrieve a representation of all the users associated with a WattDepot server as a JSON string. Only the administrator can do this.

### GET /wattdepot/{org-id}/user/{user-id}

Retrieve a representation of a user as a JSON string. Members of the organization can do this.

### GET /wattdepot/{org-id}/users/

Retrieve a representation of all users associated with this group as a JSON string. Members of the organization may do this.

### PUT /wattdepot/{org-id}/user/

Create a new user. The request entity must be a valid JSON string representation of the User. Only administrators can do this.

### POST /wattdepot/{org-id}/user/{user-id}

Update this representation of a user. The request entity must be a valid JSON string representation of the updated User. Only administrators can do this. The *id* and *organization* of the User are fixed and cannot be changed.

### DELETE /wattdepot/{org-id}/user/{user-id}

Delete this representation of a user. Only administrators can do this. Deleting a user just removes them from the organization. No data is associated with an individual user, just their organization.

---------
## Sensor

See the [Sensor domain model description](../userguide/userguide.html#sensor) for details about this concept.

### GET /wattdepot/{org-id}/sensor/{sensor-id}

Retrieve a representation of this sensor as a JSON string. Members of the organization may do this.

### GET /wattdepot/{org-id}/sensors/

Retrieve a representation of all sensors associated with this group as a JSON string. Members of the organization may do this.

### PUT /wattdepot/{org-id}/sensor/

Create a new sensor. The request entity must be a valid JSON string representation of the Sensor. Members of the organization can do this.

### POST /wattdepot/{org-id}/sensor/{sensor-id}

Update this representation of a sensor. The request entity must be a valid JSON string representation of the updated Sensor. Members of the organization can do this. The *id* and *organization* of the Sensor are fixed and cannot be changed.

### DELETE /wattdepot/{org-id}/sensor/{sensor-id}

Delete this sensor. Members of the organization can do this. All measurements created by the sensor are deleted.

----------
## Sensor Group

See the [Sensor Group domain model description](../userguide/userguide.html#sensor-group) for details about this concept.

### GET /wattdepot/{org-id}/sensor-group/{sensor-group-id}

Retrieve a representation of this sensor group as a JSON string. Members of the organization may do this.

### PUT /wattdepot/{org-id}/sensor-group/

Create a new sensor group. The request entity must be a valid JSON string representation of the sensor group. Members of the organization can do this.

### POST /wattdepot/{org-id}/sensor-group/{sensor-group-id}

Update the representation of this sensor group. The request entity must be a valid JSON string representation of the updated sensor group. Members of the organization can do this. The *id* and *organization* of the sensor group are fixed and cannot be changed.

### DELETE /wattdepot/{org-id}/sensor-group/{sensor-group-id}

Delete this sensor group. Members of the organization may do this. Deleting a sensor group has no affect on other objects in the WattDepot server.

-----------
## Sensor Model

See the [Sensor domain model description](../userguide/userguide.html#sensor) for details about sensor models, which are part of the Sensor description.

Note that Sensor Models are always defined in the *public* group.

*(Are these calls restricted to the admin user?)*

### GET /wattdepot/public/sensor-model/{sensor-model-id}

Retrieve a representation of a sensor model.

### GET /wattdepot/public/sensor-models/

Retrieve a representation of all sensor models.

### PUT /wattdepot/public/sensor-model/

Create a new sensor model.

### POST /wattdepot/public/sensor-model/{sensor-model-id}

Update this representation of a sensor model.

### DELETE /wattdepot/public/sensor-model/{sensor-model-id}

Delete this representation of a sensor model.


-----------
## Collector

See the [Collector domain model description](../userguide/userguide.html#collector) for details about this concept.

*(Do we want to call this "collector-process-definition", or just "collector"? Which is less confusing?)*

### GET /wattdepot/{org-id}/collector-process-definition/{collector-process-definition-id}

Retrieve the representation of this Collector process definition instance as a JSON string. Members of the organization may do this.

### GET /wattdepot/{org-id}/collector-process-definitions/

Retrieve a list of representations of all Collector process definition instances associated with this group as a JSON string. Members of the organization may do this.

### PUT /wattdepot/{org-id}/collector-process-definition/

Create a new representation of a collector's process definition. The request entity must be a valid JSON string representation of the collector's process definition. Members of the organization can do this.

### POST /wattdepot/{org-id}/collector-process-definition/{collector-process-definition-id}

Update a pre-existing collector's process definition. The request entity must be a valid JSON string representation of the updated collector's process definition. Members of the organization can do this.

### DELETE /wattdepot/{org-id}/collector-process-definition/{collector-process-definition-id}

Delete this process definition representation. Members of the organization can do this.


-------------
## Depository

See the [Depository domain model description](../userguide/userguide.html#depository) for details about this concept.

### GET /wattdepot/{org-id}/depository/{depository-id}

Retrieve a representation of this depository as a JSON string. Members of the organization may do this.

### GET /wattdepot/{org-id}/depositories/

Retrieve a list of representations of all depositories associated with this group as a JSON string. Members of the organization may do this.

### PUT /wattdepot/{org-id}/depository/

Create a new depository.  The request entity must be a valid JSON string representation of the depository. Members of the organization can do this.

### DELETE /wattdepot/{org-id}/depository/{depository-id}

Delete this repository. Members of the organization can do this. All measurements in this depository are deleted.

--------------
## Measurement

See the [Measurement domain model description](../userguide/userguide.html#measurement) for details about this concept.

### GET /wattdepot/{org-id}/depository/{depository-id}/measurement/{measurement-id}

Retrieve a representation of a single measurement in the depository as a JSON string. Members of the organization may do this.

*(Question: shouldn't measurement-id be a timestamp?)* That doesn't provide enough information to get a single measurement.

### GET /wattdepot/{org-id}/depository/{depository-id}/measurements/?sensor={sensor-id}&start={start-timestamp}&end={end-timestamp}

Retrieve a representation of the measurements in the depository for the given sensor from start to end. Can return an empty list. If the {sensor-id} is a sensor group id, it will return all the measurements made by the sensors in the group. 

### GET /wattdepot/{org-id}/depository/{depository-id}/measurements/gviz/?sensor={sensor-id}&start={start-timestamp}&end={end-timestamp}

Retrieve a representation (in Google Visualization format) for the measurements in the depository between start and end with the given sensor.

*(Provide link to gviz format?)*

### GET /wattdepot/{org-id}/depository/{depository-id}/sensors/

Retrieve a representation of all the sensors that have stored measurements in this depository as a JSON string. Members of the organization may do this.

### PUT /wattdepot/{org-id}/depository/{depository-id}/measurement/

Store a new measurement in this depository. The request entity must be a valid JSON string representation of the measurement. Members of the organization can do this.

### DELETE /wattdepot/{org-id}/depository/{depository-id}/measurement/{measurement-id}

Delete this measurement. Members of the organization can do this.


----------
## Interpolated Value

See the [Interpolated Value domain model description](../userguide/userguide.html#interpolated-value) for details about this concept.

### GET /wattdepot/{org-id}/depository/{depository-id}/value/?sensor={sensor-id}&start={start-timestamp}&end={end-timestamp}&timestamp={timestamp}&gap={gapSeconds}

Calculate and return the interpolated value for the given sensor or sensor group. If {sensor-id} is a sensor group id then the values are summed for all the sensors in the group. This is a complicated call there are several different valid calls.

* If timestamp is supplied then returns the interpolated value at the given time.
* If start and end are supplied then returns the difference between the interpolated value at end and the interpolated value at start.
* If gap is supplied, then the call checks to see that the measurements used to interpolate the value(s) are less than gap seconds apart. If they are not then an HTTP Status of 417 Client Error is return with the text explaining what the problem is.

### GET /wattdepot/{org-id}/depository/{depository-id}/value/gviz/?sensor={sensor-id}&start={start-timestamp}&end={end-timestamp}&timestamp={timestamp}&gap={gapSeconds}

Retrieve the measured value in Google Visualization format. This is a complicated call there are several different valid calls.

* If timestamp is supplied then returns the interpolated value at the given time.
* If start and end are supplied then returns the difference between the interpolated value at end and the interpolated value at start.
* If gap is supplied, then the call checks to see that the measurements used to interpolate the value(s) are less than gap seconds apart. If they are not then an HTTP Status of 417 Client Error is return with the text explaining what the problem is.

### GET /wattdepot/{org-id}/depository/{depository-id}/values/?sensor={sensor-id}&start={start-timestamp}&end={end-timestamp}&interval={interval-minutes}&value-type={"point" | "difference"}

Calculate and return a list of interpolated values for the given sensor or sensor group from {start-timestamp} to {end-timestamp} at a sample period of {interval-minutes}. The value-type field indicates which type of Interpolated Value to return, point or difference. If {sensor-id} is a sensor group id then the values are summed for all the sensors in the group.



---------------
## Measurement Type

See the [Measurement Type domain model description](../userguide/userguide.html#measurement-type) for details about this concept.

### GET /wattdepot/public/measurement-type/{measurement-type-id}

Retrieve a representation of this measurement type.

### GET /wattdepot/public/measurement-types/

Retrieve a representation of all defined measurement types.

### PUT /wattdepot/public/measurement-type/

Store a new measurement type.

*(Is this even possible? I thought measurement types were implemented in code?)*

### POST /wattdepot/public/measurement-type/{measurement-type-id}

*(Is this even possible? I thought measurement types were implemented in code?)*

### DELETE /wattdepot/public/measurement-type/{measurement-type-id}

*(Is this even possible? I thought measurement types were implemented in code?)*


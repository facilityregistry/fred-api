API Specifications v1.1
=======================

Specification Notes
-------------------

- This document defines an API which uses `JSON <http://www.json.org>`_ for exchanging data.
- All dates should follow `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_.
- All field/properties should follow the camelCasing convention.
- Use UTF-8 encoding.

Core Properties
---------------

Each facility must contain the following core properties.

Name
~~~~

Name of the facility.

::

  name: "Kakamega HC"


System Id
~~~~~~~~~

The internal system uid is denoted with "id".  The id most be universally unique.

::

  id: '0X9OCW3JMV98EYOVN32SGN4II'

Note: the API does not providing a specific format for IDs.  That is left up to the implementation.

URL
~~~

URL link to the unique ID API resource for the facility

::

  url: "http://facilityregistry.org/api/v1/facilities/0X9OCW3JMV98EYOVN32SGN4II.json"


External Facility Identifiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One of the primary functions of the facility registry is facilitate a mapping of the different IDs
used by different agencies to represent a particular facility.

Each external identifier consists of the following components:

- **agency**: agency who created the code.  ex) ministry of health, UNICEF, etc.
- **context**: context/external system in which the agency is using the ID.  eg) HMIS, DHIS2, HR
- **id**: unique identifier

.. code-block:: javascript

  identifiers : [
        {agency: "MOH", context: "DHIS", id: "123"},
        {agency: "UNICEF", context: "mtrac", id: "53adf"},
        { .... }
  ] 


Geolocation
~~~~~~~~~~~

Geolocation represented by longitude and latitude coordinates in that order.

::

  coordinates: [lng, lat]

Active
~~~~~~

Active = true/false indicates whether the facility is active or not.

::

  active: true

CreatedAt
~~~~~~~~~~

`ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ timestamp, including timezone, of when the facility was created.
::
  createdAt: "2011-11-16T14:26:15Z"

UpdatedAt
~~~~~~~~~~
`ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ timestamp, including timezone, of when the facility was last updated.
::
  updatedAt: "2011-11-16T14:26:15Z"

Extended Properties
~~~~~~~~~~~~~~~~~~~

Extended properties are implementation specific properties in the **properties** block.  

The property types that are supported are:

* String – A series of textual characters
* Integer – A whole number
* Decimal
* Boolean – A true or false value
* Date: `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ format. eg) 2012-12-16T18:22:20Z
* Lists - A list of one of the following data types
  * Simple data types such as the code mnemonic of selected value(s) (example: “apple”,”orange”)
  * Implementation specific complex data

**Sample properties**

.. code-block:: javascript

  "properties": {
      "numBeds": 55,
      "services": ["XR","OBG","TR"],
      "equipment": [
          {
              "id": 542,
              "name": "Microscope"
          },
          {
              "id": 942,
              "name": "Vaccine Fridge"
          }
      ],
      "manager": "Mrs. Liz"
      "hasMaternity": true,
  },


**Property field specification expectations**

- For each property field, the implementer specifies a **stable** code that should not be changed once defined.  The implementation should warn the user if they attempt to modify the code. 
- The property field code should consist only of letters and number and not any special characters, spaces or punctuation to allow them to represent a good xml element.  The API does not specify whether to define properties using *camelCasing* or *lower_case*, however, we encourage the implementation to be consistent in their formating.
- Each property field should be unique
- Specific properties for attachments and images are not supported in this version.  It is possible to use a text string to represent a file path but that is implementation specific
- Properties should follow the camelCasing naming convention

.. Note::
 
  Future releases will support: 

  - linking to an external data dictionary that defines the property schema for the facility
  - linking to external entities and references to other facilities

REST API
--------

Versioning
~~~~~~~~~~

All FRED API specifications published by the FRED team are assigned a unique version number in the format MAJOR.MINOR.REVISION. These version numbers follow a `aemantic versioning <http://semver.org/>`_ pattern whereby:

1.REVISION is incremented for revisions to a MINOR version. These changes represent nonfunctional changes to the API.
2.MINOR version numbers are incremented when new functionality is introduced which is backwards compatible with existing functionality in the MAJOR version. MINOR versions numbers are semantically compatible with previous MINOR versions.
3.MAJOR version numbers are incremented when new functionality is introduced which is semantically incompatible with previous versions. 

::

  /api/v1/facilities.json

All prior versions still supported by the code should be exposed by its own URL.

Authentication
--------------

Will be supported initially by `HTTP Basic Authentication <http://en.wikipedia.org/wiki/Basic_access_authentication/>`_.

In the future, support for `HTTP Digest Authentication <http://en.wikipedia.org/wiki/Digest_access_authentication/>`_ in addition to OAuth 2.0 will be considered.

HTTP Responses
~~~~~~~~~~~~~~

- Success: HTTP 200
- Invalid: HTTP 422
- Unauthorized:  HTTP 401
- Missing: HTTP 404
- Forbidden: HTTP 403
- Method not Allowed: HTTP 405 
- Conflict: HTTP 409


Code Name Opt Scope Trigger
- 200 OK RAllIndicates that the specified action was successfully completed. A 200 response indicates that the registry did successfully perform the operation and the response contains the final result of the action.
- 401 UnauthorizedRAllRaised when the client attempts to perform an operation against a resource which requires authorization. This error code indicates a challenge for client credentials.
- 403 ForbiddenRAllIndicates that the client does not have the necessary permission to perform the specified operation against the requested resource.
- 404 Not FoundRGETIndicates that a resource was not found or is not available.
- 405 Not AllowedRAllIndicates that the requested operation is not allowed on the current resource (for example: DELETE on a collection)
- 409 ConflictRPOSTIndicates that the facility registry has detected a conflict in the operation and has refused to perform the operation.
- 410 GoneOGETIndicates that a resource did exist but has been permanently removed. 
- 415 Unsupported Media Type O POST, PUTIndicates that the content supplied in the request is not supported by the facility registry. 
- 42 2InvalidRPOST, PUT Indicates that the request is not well-formed, is missing data, or is semantically invalid and could not be processed by the facility registry.
- 500 Internal Server ErrorRAllIndicates that the server encountered an error while attempting to execute the desired action.


Error Messages
~~~~~~~~~~~~~~

Returns HTTP Response 401 or 404 along with a human readable error message.

Optional Verbose Error messages


*JSON*

.. code-block:: javascript

  { 
    “message”: “human readable error message”,
    “moreInfo” : “http://api.facilityregistry.org/errors/12345"
  }

Adding / Updating Facilities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  /facilities.json

**POST**:  Creates facility. **SUCCESS**: HTTP 200 Returns URL to the generated facility.     

If a duplicate is detected (up to the implementation) a **409** is returned


.. Note::
 
   Need to define JSON input status

**GET**: See below

**PUT**: Error, not supported

**DELETE**: Error, not supported

::

  /facilities/<id>.json

**POST**:: Error, not supported

**GET**: See below

**DELETE**:  Delete facility.  **SUCCESS**: Returns: HTTP 200 and the ID of deleted facility

When the facility registry receives a request to obsolete a facility, the facility registry SHALL validate that the facility exists. If the requested target of deletion does not exist, the facility registry SHALL respond with an HTTP 404 error.

If the facility resource exists, the facility registry SHALL delete the facility resource such that the record is no longer discoverable to consumers. The process by which the facility registry marks the facility as deleted is not specified in this document, and is left to implementers to determine the most appropriate method.

Once the record is deleted, the facility registry SHALL return an HTTP 200 response with the URL of the deleted facility.

**PUT**: Update facility if exists, if not error.  Success: HTTP 200, JSON collection

Individual Facility Lookup
~~~~~~~~~~~~~~~~~~~~~~~~~~
::

  GET /facilities/<id>.json

Returns facility in JSON

Get Facilities
~~~~~~~~~~~~~~~
::

  GET /facilities.json

Returns the list of facilities in json.

**Sorting**

::
  
  /facilities.json?sortAsc=beds&sortDesc=nurses

Sorting precedence from left to right. 

**Pagination**

::

  /facilities.json?limit=25&offset=50

* Limit: the amount of records to return in a result. Default: Offset=25
* Offset: the offset of the search result. Default: Offset=0

**allProperties**

allProperties is a boolean field (default true) that defines that all the core properties plus the user defined properties in the properties block should be returned.   

::

  /facilities.json?allProperties=true

This would return all the properties (core + specified)

**Defining Partial Response with fields**

::

  /facilities.json?fields=name,id,properties:numBeds

This would return just the specified properties of name, id and numBeds (in the properties sub-object) in a partial response. This is very helpful in optimizing performance in bandwidth constrained settings. All properties in the facility registry are accessible by this method including the core properties and those in the property sub-object.

Filtering Facilities
~~~~~~~~~~~~~~~~~~~~

::

  /facilities.json?propertyName=filterValue


Query parameters MUST be passed as one value per parameter. Query parameters MUST map to core properties with the same name. Query parameter values MUST be URL encoded when sent to the facility registry service.

Implementers MAY choose to extend the available query parameters made available to consumers. When extended query parameters are implemented, they MUST be implemented such that:
* The name of the query parameter MUST exactly match the name of a property the parameter filters, and
* Repetitions of the same named parameter MUST be considered an OR operation. For example, if a facility registry supports filtering on creation date then a filter for all facilities created in January or February of 2012 would be represented as: “?createdAt=2012-01&createdAt=2012-02”, and
* Implementers MUST declare which extended query parameters they expose. 

**Filter by Active status**

Filter facilities that are active or not.  Supported parameters = true, false

::

  /facilities.json?active=true/false

**Filter by Updated Since**

Returns facilities updated since a particular data expressed in the `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ format.

::

  /facilities.json?updatedSince=2011-11-16T00:00:00Z



Sample JSON Output (UTF-8 encoded)
~~~~~~~~~~~~~~~~~~

.. literalinclude:: facilities.json
   :language: javascript
   :linenos:


Recommended Practices
---------------------

While it is not required, we suggest implementations support gzip, etags and cache headers which can help reduce uncessary data transfer which is helpful in low-bandwidth environments.

Cache headers if implemented could be especially useful, as a common use case seems to be maintaining a mirror of facility information.






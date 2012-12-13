API Specifications v1.0
=======================

Initial proposed draft specification

Specification Notes
-------------------

- The initial API implementation  will support only JSON.  Once the API stabilizes, support for an XML endpoint is planned.
- All dates/timestamps should be in `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ and include a timezone (default UTC)
- All field/properties should follow the camelCasing convention.

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

Internal system identifier denoted with "id"

::

  id: '0X9OCW3JMV98EYOVN32SGN4II'

Must:

- The system will generate a unique system id when generating a facility
- The system will not return the same system id twice when creating two facilities

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

Implementation Specific Properties
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Implementation specific custom properties are to be included in the **properties** block.  

The property types that are supported are:

- Text
- Integer
- Decimal
- Boolean (true/false)
- Date: `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ format. eg) 2012-12-25
- Select (select one or select many)

Select is represented by an array containing the selected options.

::

  services: ["XR","OBG","TR"],

**Sample properties**

.. code-block:: javascript

  "properties": {
      "numBeds": 55,
      "services": ["XR","OBG","TR"],
      "manager": "Mrs. Liz"
      "hasMaternity": true,
  },


**Property field specification expectations**

- For each property field, the implementer specifies a **stable** code that should not be changed once defined.  The implementation should warn the user when 
- The property field code should consist only of letters and number and not any special characters, spaces or punctuation to allow them to represent a good xml element.  The API does not specify whether to define properties using *camelCasing* or *lower_case*, however, we encourage the implementation to be consistent in their formating.
- Each property field should be unique
- Specific properties for attachments and images are not supported in this version.  It is possible to use a text string to represent a file path but that is implementation specific
- Properties should follow the camelCasing naming convention

.. Note::
 
  Future releases will support: 

  - linking to an external data dictionary that defines the property schema for the facility
  - linking to external entities and references to other facilities

API
---

Versioning
~~~~~~~~~~

`Semantic versioning <http://semver.org/>`_.  (X.Y.Z) where X is the major version, Y the minor and Z the patch version.  Minor version Y must be incremented if a new backwards compatible functionality is introduced to the API.  A major version X must be incremented if any backwards incompatible changes are introduced to the public API.

The major version must be exposed in the URL.  Note: the URL pattern may vary by implementation.

::


  /api/v1/facilities.json

All prior versions still supported by the code should be exposed by its own URL.

Authentication
--------------

Will be supported initially by `HTTP Basic Authentication <http://en.wikipedia.org/wiki/Basic_access_authentication/>`_.

In the future, support for `HTTP Digest Authentication <http://en.wikipedia.org/wiki/Digest_access_authentication/>`_ in addition to OAuth 2.0 will be considered.

HTTP Responses
~~~~~~~~~~~~~~

- Success: HTTP 200, XML/JSON representation of object (when applicable)
- Invalid: HTTP 422, XML/JSON representation of errors
- Unauthorized:  HTTP 401
- Missing: HTTP 404
- Forbidden: HTTP 403
- Method not Allowed: HTTP 405 
- Conflict: HTTP 409

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

List Facilities
~~~~~~~~~~~~~~~
::

  GET /facilities.json

Returns the list of facilities in json.

**allProperties**

allProperties is a boolean field (default true) that defines that all the core properties plus the user defined properties in the properties block should be returned.   

::

  /facilities.json?allProperties=true

This would return all the properties (core + specified)

**Defining Partial Response with fields**

::

  /facilities.json?fields=name,id,properties:numBeds

This would return just the specified properties of name, id and numBeds (in the properties sub-object) in a partial response. This is very helpful in optimizing performance in bandwidth constrained settings. All properties in the facility registry are accessible by this method including the core properties and those in the property sub-object.

Filter by Active status
~~~~~~~~~~~~~~~~~~~~~~~

Filter facilities that are active or not.  Supported parameters = true, false

::

  /facilities.json?active=true/false

Filter by Updated Since
~~~~~~~~~~~~~~~~~~~~~~~

Returns facilities updated since a particular data expressed in the `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601>`_ format.

::

  /facilities.json?updatedSince=2011-11-16T00:00:00Z



Sample JSON Output
~~~~~~~~~~~~~~~~~~

.. literalinclude:: facilities.json
   :language: javascript
   :linenos:


Recommended Practices
---------------------

While it is not required, we suggest implementations support gzip, etags and cache headers which can help reduce uncessary data transfer which is helpful in low-bandwidth environments.

Cache headers if implemented could be especially useful, as a common use case seems to be maintaining a mirror of facility information.






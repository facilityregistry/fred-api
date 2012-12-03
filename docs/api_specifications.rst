API Specifications v1.0
=======================

Initial proposed draft specification

Specification Notes
-------------------

The initial API implementation will support only JSON.  Once the API stabilizes, support for an XML endpoint is planned.

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

  id: 0X9OCW3JMV98EYOVN32SGN4II

Must:

- The system will generate a unique system id when generating a facility
- The system will not return the same system id twice when creating two facilities

Note: the API does not providing a specific format for IDs.  That is left up to the implementation.

URL
~~~

URL link to the unique ID API resource for the facility

::

  url: "http://facilityregistry.org/api/v1/facilities/0X9OCW3JMV98EYOVN32SGN4II"


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

Geolocation represented by latitude and longitude cooridinates in decimal degrees.

::

  coordinates: [-1.6917, 29.5250]


Created At
~~~~~~~~~~

`ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601/>`_ timestamp of when the facility was created.
::
  created_at: "2011-11-16T14:26:15Z"

Updated At
~~~~~~~~~~
`ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601/>`_ timestamp of when the facility was last updated.
::
  updated_at: "2011-11-16T14:26:15Z"


Active
~~~~~~

Active = True/False indicates whether the facility is active or not.

::

  active: "True"

Implementation Specific Properties
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


API
---

Versioning
~~~~~~~~~~

`Semantic versioning <http://semver.org/>`_.  (X.Y.Z) where X is the major version, Y the minor and Z the patch version.  Minor version Y must be incremented if a new backwards compatible functionality is introduced to the API.  A major version X must be incremented if any backwards incompatible changes are introduced to the public API.

The major version must be exposed in the URL.  Note: the URL pattern may vary by implementation.

::


  /api/v1/facilities

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
    “more_info” : “http://api.facilityregistry.org/errors/12345}
  }

Adding / Updating Facilities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  /facilities

**POST**:  Creates facility. **SUCCESS**: HTTP 200 Returns URL to the generated facility.     

If a duplicate is dedicated (up to the implementation) a **409** is returned


.. Note::
 
   Need to define JSON input status

**GET**: See below

**PUT**: Error, not supported

**DELETE**: Error, not supported

::

  /facilities/<id>

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

**Properties**

::

  /facilities.json?properties=all

Whether to return all facility properties or just the core properties in the result set.  Default just returns core.

**Meta Summary Data** *(optional)*

Results return a meta block of summary resultset data to make client application development easier.  This is an *optional* feature to the core API.

:: 

   meta: {
        limit: 2,
        next: null,
        offset: 0,
        previous: null,
        total_count: 29
    },



Filter by Active status
~~~~~~~~~~~~~~~~~~~~~~~

Filter facilities that are active or not.  Supported parameters = True, False

::

  /facilities.json?active=True/False

Filter by Updated Since
~~~~~~~~~~~~~~~~~~~~~~~

Returns facilities updated since a particular data expressed in the `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601/>`_ format.

::

  /facilities.json?updated_since=2012-03-23



Sample JSON Output
~~~~~~~~~~~~~~~~~~

.. literalinclude:: facilities.json
   :language: javascript
   :linenos:


Recommended Practices
---------------------

While it is not required, we suggest implementations support gzip, etags and cache headers which can help reduce uncessary data transfer which is helpful in low-bandwidth environments.

Cache headers if implemented could be especially useful, as a common use case seems to be maintaining a mirror of facility information.






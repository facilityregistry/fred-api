API Specifications v0.9
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

  name: "Ruhiira HC"

System ID
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

  <url>http://facilityregistry.org/api/v1/facilities/0X9OCW3JMV98EYOVN32SGN4II</url>

External Facility Identifiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One of the primary functions of the facility registry is facilitate a mapping of the different IDs
used by different agencies to represent a particular facility.

Each external identifier consists of the following components:

- agency: agency who created the code.  ex) ministry of health, UNICEF, etc.
- context: context/external system in which the agency is using the ID.  eg) HMIS, DHIS2, HR

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

Active = yes/no indicates whether the facility is active or not.

::

  active: yes/no

Authentication
--------------

Will be supported initially by `HTTP Basic Authentication <http://en.wikipedia.org/wiki/Basic_access_authentication/>`_.

In the future, support for `HTTP Digest Authentication <http://en.wikipedia.org/wiki/Digest_access_authentication/>`_ in addition to OAuth 2.0 will be considered.

API
-------------

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




Versioning
~~~~~~~~~~

`Semantic versioning <http://semver.org/>`_.  (X.Y.Z) where X is the major version, Y the minor and Z the patch version.  Minor version Y must be incremented if a new backwards compatible functionality is introduced to the API.  A major version X must be incremented if any backwards incompatible changes are introduced to the public API.

The major version must be exposed in the URL.  Note: the URL pattern may vary by implementation.

::
  /api/v1/facilities

All prior versions still supported by the code should be exposed by its own URL.

Adding / Updating Facilities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  /facilities

**POST**:  Creates facility. **SUCCESS**: HTTP 200 Returns URL to the generated facility.     

.. Note::
 
   Need to define JSON/XML input status

**GET**: See below

**PUT**: Error, not supported

**DELETE**: Error, not supported

::

  /facilities/<id>

**POST**:: Error, not supported

**GET**: See below

**DELETE**:  Delete facility.  **SUCCESS**: Returns: HTTP 200 and the ID of deleted facility

**PUT**: Update facility if exists, if not error.  Success: HTTP 200, XML/JSON collection

Individual Facility Lookup
~~~~~~~~~~~~~~~~~~~~~~~~~~
::

  GET /facilities/<id>.json/xml

Returns facility in JSON or XML.

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


Filtering Facilities
~~~~~~~~~~~~~~~~~~~~
::

  /facilities.json?property1=value&property2=value
  
Properties apply to all core and user defined facility properties

Counting
~~~~~~~~
::

  /facilities/count.json

Returns the count of all facilities

::
  
    /facilities/count.json?beds=10

Returns counts of facilities where the number of beds are equal to 10, with beds=10 representing the querystring.

Pagination
~~~~~~~~~~

::
 
  /api/features.json?limit=25&offset=50

- **limit**: the amount of records to return in a result
- **offset**: the offset of the search result.  Facilitates pagination
- **paging=false**: turns paginiation off

Text Search
~~~~~~~~~~~
::

  /facilities.json?q=search_value

Text search 

.. Note::
 
   Support for this will vary by implementation.  
   
   Implementing service should specify clearly in their documentation what fields support text search.

   The text search:
   
   - Searches in values of properties
   - Searches in both codes and values of Coded Value style properties (lists, tags, hierarchies, etc)

   A site with a coded value “CHP”->”Community Health Post” would be a hit on a query on the word “post”
   A site with a coded value of  “0203”->”Mygenge” would be a hit on queries “203” and “genge”


Sorting
~~~~~~~
::

  /facilities.json?sortAsc=beds&sortDesc=nurses

Sorts the results by property.  

.. Note::
 
   - Each field type needs to define what ascending/descending means.
   - Sorting precedence is left to right (first by beds then by nurses in the example above) - closest to the “?”


Filter by Updated Since
~~~~~~~~~~~~~~~~~~~~~~~
::

  /facilities.json?updated_since=2012-03-23

Returns facilities updated since a particular data expressed in the `ISO 8601 <http://en.wikipedia.org/wiki/ISO_8601/>`_ format.

Sample JSON Output
~~~~~~~~~~~~~~~~~~

.. literalinclude:: facilities.json
   :language: javascript
   :linenos:



Sample XML Output
~~~~~~~~~~~~~~~~~

.. literalinclude:: facilities.xml
   :language: xml
   :linenos:








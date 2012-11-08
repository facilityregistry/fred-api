API Specifications v0.9
=======================

Initial proposed draft specification

Core Properties
---------------

**Global Unique Identifier (GUID)**


A URL call back to the facility’s global unique identifier (GUID) in the facility registry.

*XML*
::
  <guid>http://facilities.moh.gov.rw/api/1.0/facilities/50181.xml</guid>

*JSON:*
::    
  "guid”: “http://facilities.moh.gov.rw/api/1.0/facilities/50181.json

Must:
- The system will generate system identifiers when creating resources
- The system will not return the same system identifier twice when creating two resources

Discussed but left to specific implementations:
- be nicely url encoded
- be a GUID
- have numeric spacing

**External Facility Identifiers/Codes**

The services provides the ability to map to external IDs used by different agencies.

Each identifier consists of the following components:

id = ID code 
agency = agency who created the code.  ex) ministry of health, UNICEF, etc.
context = context/external system in which the agency is using the ID.  eg) HMIS, DHIS2, HR

*XML*

.. code-block:: xml

  <FOSAID  identifier=”yes” agency=”moh” context =”fosa”>1234</FOSAID>

*JSON*

.. code-block:: javascript

  {
    “FOSAID”: 
    “id”: 1234,
    “identifier”: “yes”,
    “agency”: “moh”,
    “context: “fosa”
  }


**Name**

Name of the facility.

**Geolocation**

Each facility can have it’s location represented by an optional GPS point represented in decimal degrees.

*XML*
::
  <geopoint:latitude>-1.69172</geopoint:latitude> 
  <geopoint:longitude>29.52505</geopoint:longitude> 

**Created At**

Timestamp of when the facility was created.

Use ISO 8601 format.
::
  2011-11-16T14:26:15Z

**Updated At**

Timestamp of when the facility was last updated.



JSON Facilities Representation
------------------------------

.. literalinclude:: facilities.json
   :language: javascript
   :linenos:


XML Facilities Representation
-----------------------------

.. literalinclude:: facilities.xml
   :language: xml
   :linenos:




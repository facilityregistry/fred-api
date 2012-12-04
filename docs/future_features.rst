Future API Features
===================

Proposed features for future API releases.

Version 1.1
-----------

Future TBD
----------

Core Properties
~~~~~~~~~~~~~~~

Add support for open_date and closed_date?

**Meta Summary Data** *(optional)*

Results return a meta block of summary resultset data to make client application development easier.  This is an *optional* feature to the core API.

:: 
`
   meta: {
        limit: 2,
        next: null,
        offset: 0,
        previous: null,
        total_count: 29
    },


Pagination
~~~~~~~~~~

::
 
  /api/features.json?limit=25&offset=50

- **limit**: the amount of records to return in a result
- **offset**: the offset of the search result.  Facilitates pagination
- **paging=false**: turns paginiation off


Sorting
~~~~~~~
::

  /facilities.json?sortAsc=beds&sortDesc=nurses

Sorts the results by property.  

.. Note::
 
   - Each field type needs to define what ascending/descending means.
   - Sorting precedence is left to right (first by beds then by nurses in the example above) - closest to the “?”



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


Sample XML Output
~~~~~~~~~~~~~~~~~

.. literalinclude:: facilities.xml
   :language: xml
   :linenos:






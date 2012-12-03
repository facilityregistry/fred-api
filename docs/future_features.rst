Future API Features
===================

Proposed features for future API releases.

Version 1.1
-----------

Future TBD
----------

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









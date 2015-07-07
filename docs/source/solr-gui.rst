Solr GUI and Query
------------------------------------------------------------------------------

Access Solr Gui
***************

- Go to: http://localhost:8983/solr/#/
- Select Core "collection1"
- Go to: "Schema Browser"
- Select "Title"
- Click: "Load Term Info"
- Click on term "nachrichten"

Solr Query
**********

Solr Query Parameters:

Query "q"::

    Title:"nachrichten"
    *:"nachrichten"

Filter Query "fq"::

    is_folderish:true

Sorting "sort"::

    "Date asc"
    "Date desc"

Filter List "fl"::

    Title,Type

This parameter can be used to specify a set of fields to return, limiting the amount of information in the response.

Response Writer "wt"::

  "json"

A Response Writer generates the formatted response of a search.

Solr Query via URL
******************

Copy query from Solr GUI, e.g.::

    http://localhost:8983/solr/collection1/select?q=Title%3A%22termine%22&wt=json&indent=true


Advanced Solr Query Syntax
**************************

Simple Query::

    "fieldname:value"

Operators::

    "Title:Foo AND Description:Bar"

"AND", "OR", "+", "-"

Range Queries::

    "[* TO NOW]"

Boost Terms:

    "people^4"

Fuzzy Search::

    "house0.6"

Proximity Search::

    "'apache solr'2"


Questions
*********

- What do we get by using Solr instead of the Plone search?
- Why don't we query Solr directly in Plone?
- What does collective.solr do?

Solr GUI and Query Syntax
----------------------------------

In the next part we will take a closer look the the search GUI of Solr
and its query syntax.

Access Solr Gui
***************

Solr is a REST based wrapper around the Java lucene index. It comes with
its own web GUI. It it possible to access all of the SOLR API via REST and
most of this functionality is exposed via its web GUI. To test it out, do the
following. :

 - Go to: http://localhost:8983/solr/#/
 - Select Core "collection1"
 - Go to: "Schema Browser"
 - Select "fullname"
 - Click: "Load Term Info"
 - Click on term "<fullname>"

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

Faceting
**************************

TBD


Search GUIs
**************************

 - c.solr ootb

 - eea.facetednavigation

 - custom

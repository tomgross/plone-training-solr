collective.solr JSON API
------------------------------------------------------------------------------

Checkout json-api branch of collective.solr.

buildout.cfg::

    [buildout]
    ...
    extensions = mr.developer
    auto-checkout = collective.solr

    ...

    [sources]
    collective.solr = git https://github.com/collective/collective.solr.git pushurl=git@github.com:collective/collective.solr.git branch=json-api

Run buildout::

  $ bin/buildout

Start Plone and Solr::

  $ bin/instance fg
  $ bin/solr-instance fg


JSON Search API
***************

URL::

  http://localhost:8080/Plone/@@search?format=json&SearchableText=Plone

Javascript::

  GET http://localhost:8080/Plone/@@search?SearchableText=Plone
  Accept: application/json

Response::

    {
        "data":
    [
            {
                "description": "",
                "id": "front-page",
                "portal_type": "Document",
                "title": "Willkommen bei Plone",
                "url": "http://localhost:8080/Plone/front-page"
            }
        ],
        "suggestions": [ ]
    }


JSON Suggest API
****************

URL:

    http://localhost:8080/Plone/@@search?format=json&SearchableText=Plane

Javascript::

  GET http://localhost:8080/Plone/@@search?SearchableText=Plane
  Accept: application/json

Response::

    {
        "data": [ ],
        "suggestions":
        {
            "plane":
            {
                "endOffset": 87,
                "numFound": 1,
                "startOffset": 82,
                "suggestion":
                    [
                        "plone"
                    ]
                }
            }
        }
    }


JSON Autocomplete API
*********************

URL::

  http://localhost:8080/Plone/@@solr-autocomplete?term=Pl

Response::

[
    {
        "value": "Willkommen bei Plone",
        "label": "Willkommen bei Plone"
    }
]





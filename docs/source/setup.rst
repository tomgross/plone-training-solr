Set up Plone and Solr
------------------------------------------------------------------------------

Buildout
********

Bootstrap project::

  $ mkdir plone-training-solr
  $ cd plone-training-solr
  $ wget https://bootstrap.pypa.io/bootstrap-buildout.py
  $ wget https://raw.githubusercontent.com/collective/collective.solr/json-api/solr.cfg


Create Buildout (buildout.cfg)::

    [buildout]
    extends =
        http://dist.plone.org/release/4.3.6/versions.cfg
        solr.cfg
    parts += instance

    [instance]
    recipe = plone.recipe.zope2instance
    http-address = 8080
    user = admin:admin
    eggs =
        Plone
        collective.solr

    [versions]
    zope.interface = 4.0.5
    zc.buildout = 2.3.1
    setuptools = 8.0.4

Run buildout::

  $ python2.7 bootstrap-buildout.py
  $ bin/buildout

Start Plone::

  $ bin/instance fg

Start Solr::

  $ bin/solr-instance fg

Solr Buildout
*************

Buildout parts::

    [buildout]
    parts +=
        solr-download
        solr-instance

Base Solr Settings::

    [settings]
    solr-host = 127.0.0.1
    solr-port = 8983
    solr-min-ram = 128M
    solr-max-ram = 256M

Solr Download::

    [solr-download]
    recipe = hexagonit.recipe.download
    strip-top-level-dir = true
    url = https://archive.apache.org/dist/lucene/solr/4.10.4/solr-4.10.4.tgz
    md5sum = 8ae107a760b3fc1ec7358a303886ca06

Solr Instance::

    [solr-instance]
    recipe = collective.recipe.solrinstance
    solr-location = ${solr-download:location}
    host = ${settings:solr-host}
    port = ${settings:solr-port}
    basepath = /solr
    # autoCommitMaxTime = 900000
    max-num-results = 500
    section-name = SOLR
    unique-key = UID
    logdir = ${buildout:directory}/var/solr
    default-search-field = default
    default-operator = and
    unique-key = UID
    java_opts =
      -Dcom.sun.management.jmxremote
      -Djava.rmi.server.hostname=127.0.0.1
      -Dcom.sun.management.jmxremote.port=8984
      -Dcom.sun.management.jmxremote.ssl=false
      -Dcom.sun.management.jmxremote.authenticate=false
      -server
      -Xms${settings:solr-min-ram}
      -Xmx${settings:solr-max-ram}

Index::

    index =
        name:allowedRolesAndUsers   type:string stored:false multivalued:true
        name:created                type:date stored:true
        name:Creator                type:string stored:true
        name:Date                   type:date stored:true
        name:default                type:text indexed:true stored:false multivalued:true
        name:Description            type:text copyfield:default stored:true
        name:description            type:text copyfield:default stored:true
        name:effective              type:date stored:true
        name:exclude_from_nav       type:boolean indexed:false stored:true
        name:expires                type:date stored:true
        name:getIcon                type:string indexed:false stored:true
        name:getId                  type:string indexed:false stored:true
        name:getRemoteUrl           type:string indexed:false stored:true
        name:is_folderish           type:boolean stored:true
        name:Language               type:string stored:true
        name:modified               type:date stored:true
        name:object_provides        type:string stored:false multivalued:true
        name:path_depth             type:integer indexed:true stored:false
        name:path_parents           type:string indexed:true stored:false multivalued:true
        name:path_string            type:string indexed:false stored:true
        name:portal_type            type:string stored:true
        name:review_state           type:string stored:true
        name:SearchableText         type:text copyfield:default stored:false
        name:searchwords            type:string stored:false multivalued:true
        name:showinsearch           type:boolean stored:false
        name:Subject                type:string copyfield:default stored:true multivalued:true
        name:Title                  type:text copyfield:default stored:true
        name:Type                   type:string stored:true
        name:UID                    type:string stored:true required:true


- name: Name of the field
- type: Type of the field (e.g. "string", "text")
- indexed: searchable
- stored: returned as metadata
- copyfield: copy content to another field, e.g. copy title, description, subject and SearchableText to default.

https://wiki.apache.org/solr/SchemaXml#Common_field_options

Plone and Solr
**************

Activate Solr in Plone::

- Create Plone instance with collective.solr installed
- Go to: "Configuration" -> "Solr Settings"
- Check: "Active", click "Save"
- Go to: http://localhost:8080/Plone/@@solr-maintenance/reindex
- Search for "Plone"

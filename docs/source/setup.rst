Set up Plone and Solr
=====================

For using Solr with Plone you need two things:

 1) A running Solr server
 2) A integration product (like collective.solr) for delegation of indexing
    and searching to the Solr server. In this training we will focus on
    collective.solr for this purpose.

Bootstrap project::

  $ mkdir plone-training-solr
  $ cd plone-training-solr
  $ curl -O https://bootstrap.pypa.io/bootstrap-buildout.py
  $ curl -O https://raw.githubusercontent.com/collective/collective.solr/master/solr.cfg
  $ curl -o plone5.cfg https://raw.githubusercontent.com/collective/minimalplone5/master/buildout.cfg


a buildout (*buildout.cfg*) which installs both requirements::

    [buildout]
    extends =
        plone5.cfg
        solr.cfg

    [instance]
    eggs +=
        collective.solr

Run buildout::

  $ python2.7 bootstrap-buildout.py
  $ bin/buildout

Start Plone in foreground mode to see that everything is ok::

  $ bin/instance fg

Start Solr in another terminal in foreground mode ::

  $ bin/solr-instance fg

Solr Buildout
*************

We assume you are more less familiar with the Plone buildout but let's
analyze the solr buildout configurtion a bit.

First we have two buildout parts::

    [buildout]
    parts +=
        solr-download
        solr-instance

As the name suggests *solr-download* gets the full Solr package from
the official download server and unpacks it.
The part *solr-instance* is for configuring Solr. Let's continue with the
details.

The base Solr settings specify the host (usually localhost or 0.0.0.0), the
port (8983 is the standard port of Solr) and two Java parameters for specifying 
lower and upper memory limit. More is usually better. If you want a rough idea
on how much memory you should use follow the guidelines found in this article:
https://lucidworks.com/blog/2011/09/14/estimating-memory-and-storage-for-lucenesolr/ ::

    [settings]
    solr-host = 127.0.0.1
    solr-port = 8983
    solr-min-ram = 128M
    solr-max-ram = 256M

There is nothing fancy in the Solr download part. It takes an URL to the Solr
binary and a md5 sum for verification. 

.. note At time of writing the latest working version of Solr was 4.10.x

It looks like this in *solr.cfg*::

    [solr-download]
    recipe = hexagonit.recipe.download
    strip-top-level-dir = true
    url = https://archive.apache.org/dist/lucene/solr/4.10.4/solr-4.10.4.tgz
    md5sum = 8ae107a760b3fc1ec7358a303886ca06

The Solr instance part is more complicated. It provides a subset of many,
many configuration options of Solr and the possibility to define the
schema of the index::

    [solr-instance]
    recipe = collective.recipe.solrinstance
    solr-location = ${solr-download:location}
    host = ${settings:solr-host}
    port = ${settings:solr-port}
    basepath = /solr
    max-num-results = 500
    section-name = SOLR
    unique-key = UID
    logdir = ${buildout:directory}/var/solr
    default-search-field = default
    default-operator = and
    java_opts =
      -Dcom.sun.management.jmxremote
      -Djava.rmi.server.hostname=127.0.0.1
      -Dcom.sun.management.jmxremote.port=8984
      -Dcom.sun.management.jmxremote.ssl=false
      -Dcom.sun.management.jmxremote.authenticate=false
      -server
      -Xms${settings:solr-min-ram}
      -Xmx${settings:solr-max-ram}

Let's analyze them one by one ::

    solr-location = ${solr-download:location}

Specify the location of Solr, dowloaded with the previous part. ::

    host = ${settings:solr-host}
    port = ${settings:solr-port}
    basepath = /solr

Base configuration for running Solr referencing previously defined settings.
With this configuration it is possible to access Solr in a browser with the
following URL: http://localhost:8983/solr ::
The section-name defines the name which can be used to reflect custom address
and/or basepath settings in zope.conf.::

    section-name = SOLR

It follows the following pattern in *zope.conf* If you use standard settings
no changes in *zope.conf* are necessary. ::

    <product-config ${part:section-name}>
        address ${part:host}:${part:port}
        basepath ${part:basepath}
    </product-config>

.. note: Another easy way to use different hosts on dev, stage and production
   machines is to define a host alias in /etc/hosts

Like the Zope catalog the Solr index has a schema consisting of index and metadata fields.
You can think of index fields as something you can use for querying / searching
and metadata something you return as result list.
Solr defines its schema in a big XML schema.xml. There is a section in the
*collective.recipe.solrinstance* which gives you access to the most common
configuration options in a buildout way::

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

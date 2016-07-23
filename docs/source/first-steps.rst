First Steps
=====================

Indexing a new dexterity field
------------------------------

A common use case is to add an aditional field to the index.
We have two inform both sides (Solr and Plone) if we
need a new field in the index.

A simple use case is to pass through a raw dexterity field
to the index. First we add the field to the schema.
We do this TTW right now.

.. note:: In the production setup you will define your schema
   with an interface or a supermodel XML but this is beyond of
   this training. More information on dexterity schemas and
   fields can be found in the Plone documention: Link TBD

Let's add a field *email* to a page. We assume this is contact
email which can be used to contact the reponsible support person
for this page. And we want to make this field to be found in
fulltext search.

TBD Steps to add field
TBD Store schema.xml?
TBD Screenshot adding field to schema

Next thing we do is to extend the Solr fields definition in
our buildout.cfg

to the *fields* section of the *solr* part we add the
following line: ::

        name:email                type:string copyfield:SearchableText stored:true multivalued:false

After we have done that we need to rerun the buildout ::

 $ bin/buildout

and restart Solr and Plone ::

 $ bin/instance restart
 $ bin/solr-instance fg

TBD Point to indexing adapters



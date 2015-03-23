ElasticSearch
=============

ElasticSearch_ is a full-text search engine. In Spinal Stack it stores all the logs provided by the various components Spinal Stack deploys and configure.

Configuration
-------------

========================== =================================================== ============================
Configuration              Description                                         Default
========================== =================================================== ============================
elasticsearch              Enable HAProxy binding Internal network IP          true
elasticsearch_bind_options HAProxy bind options Internal network IP            HAProxy default bind options
elasticsearch_bind_ip      IP address on which ElasticSearch will be listening Internal network IP
elasticsearch_port         Port on which ElasticSearch will be listening       9200
========================== =================================================== ============================

puppet-openstack-cloud
----------------------

* cloud::database::nosql::elasticsearch
* cloud::logging::server (include cloud::database::nosql::elasticsearch)

Non-HA Setup
------------

In a non-HA setup, simply instantiate the 'cloud::logging::server' on the concerned node.

HA Setup
--------

In an HA setup, instantiate 'cloud::logging::server' on all the nodes needed, puppet-openstack-cloud will take care of putting the cluster behind HAProxy, and ElasticSearch itself will take care of creating the internal cluster.

.. _ElasticSearch: https://www.elasticsearch.org

Kibana
======

Kibana_ is a front-end to ElasticSearch. It enables to visualize all the Spinal Stack components logs.l

Configuration
-------------

================= ================================================================ =================================================
Configuration         Description                                                  Default
================= ================================================================ =================================================
kibana_bind_ip    IP on which Kibana will be listening on                          Internal network IP
kibana_servername Servername in which Kibana will be listening on                  VIP public full qualified domain name
kibana_port       Port on which Kibana will be listening on                        8300
kibana_manage_ws  Boolean to describe if the apache vhost should be managed        true
kibana_es_server  IP address or hostname of the ElasticSearch server to connect to VIP internal network IP
kibana_es_index   Index on which logs are stored on the ElasticSearch server       fluentd
================= ================================================================ =================================================

.. _Kibana: http://www.elasticsearch.org/overview/kibana/

Logging Stack Components
========================

Before tackling how to configure logging in Spinal Stack, the following is the list of components used to create the logging stack. Every component is open-source and freely available.

============= ======================== =========== =================== ============================================
name          role                     technology  license             homepage
============= ======================== =========== =================== ============================================
rsyslog       Logs exporter (optional) C           GNU GPLv3           http://www.rsyslog.com
fluentd       Data collector           Ruby / C++  Apache license v2.0 http://www.fluentd.org
elasticsearch Logs storage             Java        Apache license v2.0 http://www.elasticsearch.org
kibana3       Logs browser             JavaScript  Apache License V2.0 http://www.elasticsearch.org/overview/kibana
============= ======================== =========== =================== ============================================


Rsyslog
-------

Rsyslog provides a way to configure logging per host. It allows to export log and specify facility level per program.

In Spinal Stack, rsyslog is implemented in case it has to be plugged on a customer logging infrastracture. The ideal
setup remains to configure a fluentd agent to export specifly formated logs.

fluentd
-------

Fluentd (aka td-agent) is a log collector. It *has* to be installed in the log server and can be installed on every other node.
It works by specifying inputs, modifier and outputs.

In Spinal Stack, on the log server, fluentd listens for inputs on either a tcp or upd port and then insert them into ElasticSearch.

elasticsearch
-------------

Elasticsearch is a distributed - very scalable - search server. It provides the ability to have schema-free JSON documents.

In Spinal Stack, it is the component that will store all the log sent to the log server, by default it is installed in the install-server.
It can be easily scaled, simply by adding a new ElasticSearch instance to the same cluser name.

kibana3
-------

Kibana is the visual interface to the ElasticSearch cluster. It is developped by the ElasticSearch team itself, it hence provides a good integration with ElasticSearch.
Kibana is a set a javascript files ran behind a web server.

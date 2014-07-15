Configuration
=============

SpinalStack provides two classes related to logging. logging/agent.pp and logging/server.pp. Please read below for more details.

Puppet
------

logging/agent.pp
****************

The purpose of this puppet class is to be able to fully configure either fluentd and/or rsyslog on the agent nodes.

The class itself takes only 4 parameters :

* syslog_enable (boolean) : Should rsyslog be configured in the agent.
* sources (hash) : An hash describing the source of the logs.
* matches (hash) : An hash describing the matches for specific tagged logs.
* plugins (hash) : An hash describing the list of plugins that should be installed on the instance.

As for the rest of SpinalStack, the whole configuration takes place in the according hiera's yaml configuration file.

logging/server.pp
*****************

One important thing to understand about this class, is that a logging server is also a logging agent, with ElasticSearch and Kibana installed along side.

The configuration mentionned above applies here, plus the operator should configure ElasticSearch and Kibana from hiera.



Components Configuration
------------------------

Following is the list of the puppet modules used for SpinalStack logging infrastructure :

==================== ===============================================================
name                 homepage
==================== ===============================================================
puppet-rsyslog       https://github.com/enovance/puppet-rsyslog
puppet-fluentd       https://github.com/enovance/puppet-fluentd
puppet-elasticsearch https://github.com/enovance/puppet-elasticsearch
kibana3              https://github.com/enovance/kibana3
==================== ===============================================================

Each and every of those modules are highly configurable. Listed below are the most important parameters for a proper integration.
If those parameters is not enough for your needs, please refer to the implementation of the actual module.

puppet-rsyslog
**************

====================================== ===================== ==============================================
name                                   default               purpose
====================================== ===================== ==============================================
rsyslog::client::log_remote            true                  Should the log be sent to remote server
rsyslog::client::remote_type           tcp                   Protocol to use. Possible value 'tcp' or 'udp'
rsyslog::client::remote_forward_format RSYSLOG_ForwardFormat Rsyslog format
rsyslog::client::port                  514                   Remote port to send data to
rsyslog::client::server                log                   Server name to send data to
====================================== ===================== ==============================================

puppet-elasticsearch
********************

====================================== ===================== ===============================================================================================
name                                   default               purpose
====================================== ===================== ===============================================================================================
elasticsearch::init_template           undef                 Name of the initd file. Possible value 'elasticsearch.RedHat.erb' or 'elasticsearch.Debian.erb'
====================================== ===================== ===============================================================================================

kibana3
*******

============================ ============================== ===============================================================================================
name                         default                        purpose
============================ ============================== ===============================================================================================
kibana3::config_es_port      9200                           Port the ElasticSearch cluster is listening on
kibana3::config_es_protocol  http                           Protocol to contact the Elasticsearch cluster. Possible value 'http' or 'https'
kibana3::config_es_server    '"_+window.location.hostname"' IP address or servername of the ElasticSearch cluster to contact
kibana3::config_kibana_index kibana-int                     ElasticSearch index to retrieve logs from
kibana3::manage_ws           true                           Should the module manage the apache webserver
kibana3::ws_servername       kibana3                        Apache vhost name
kibana3::ws_port             80                             Port apache is listening on
============================ ============================== ===============================================================================================

Hiera
-----

agent
*****

Scenario 1: The agent export all its log to the log server via rsyslog
######################################################################

.. code-block:: none

  ---
  cloud::logging::agent::syslog_enable: true
  rsyslog::client::log_remote: true
  rsyslog::client::remote_type: update
  rsyslog::client::port: 5140
  rsyslog::client::server: logserver.example.com
  rsyslog::client::remote_forward_format: RSYSLOG_TraditionalForwardFormat


Scenario 2: The agent export its log using fluentd, rsyslog isn't configured. Logs are formatted on the agent side
###################################################################################################################

.. code-block:: none

  ---
  cloud::logging::agent::syslog_enable:false
  cloud::logging::agent::sources:
    apache:
      configfile: apache
      format: apache2
      type: tail
      tag: log.apache
      config:
        path: /var/log/apache2/access.log
        pos_file: /var/tmp/fluentd.pos
  cloud::logging::agent::matches:
    forward:
      configfile: forward
      pattern: "**"
      type: forward
      servers:
      -
        host: logserver.example.com
        port: 24224

server
******

Scenario 1: The log server receives its log from rsyslog and stores them in elasticseach
########################################################################################

.. code-block:: none

  ---
  elasticsearch::init_template: elasticsearch.RedHat.erb
  kibana3::manage_ws: false
  kibana3::config_es_server: 127.0.0.1
  kibana3::config_kibana_index: fluentd
  kibana3::ws_servername: logserver.example.com
  cloud::logging::agent::sources:
    syslog:
      configfile: syslog
      type: syslog
      tag: log.syslog
      config:
        port: 5140
        bind: 0.0.0.0
        with_priority:
  cloud::logging::agent::matches:
    elasticsearch
      configfile: elasticsearch
      pattern: "**"
      type: elasticsearch
      config:
        logstash_format: true
        index_name: fluend
        type_name: fluentd
        port: 9200
        host: 127.0.0.1

Scenario 2: The log server receives its log from a fluentd forward and stores them in elasticsearch
###################################################################################################

.. code-block:: none

  ---
  elasticsearch::init_template: elasticsearch.RedHat.erb
  kibana3::manage_ws: false
  kibana3::config_es_server: 127.0.0.1
  kibana3::config_kibana_index: fluentd
  kibana3::ws_servername: logserver.example.com
  cloud::logging::agent::sources:
    forward
      configfile: forward
      type: forward
      tag: log.syslog
      config:
        port: 24224
  cloud::logging::agent::matches:
    elasticsearch
      configfile: elasticsearch
      pattern: "**"
      type: elasticsearch
      config:
        logstash_format: true
        index_name: fluend
        type_name: fluentd
        port: 9200
        host: 127.0.0.1

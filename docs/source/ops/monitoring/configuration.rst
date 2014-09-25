Configuration
=============

Spinal Stack provides two classes related to monitoring. monitoring/agent.pp and monitoring/server.pp. Please read below for more details.

Puppet
------

monitoring/agent/sensu.pp
*************************

The purpose of this puppet class is to be able to fully configure sensu on the agent side.

The class itself takes no parameter it only installs the sensu packages.

As for the rest of Spinal Stack, the whole configuration takes place in the according hiera's yaml configuration file.

monitoring/server/sensu.pp
**************************

The purpose of this puppet class is to be able to fully configure the sensu server.

In order to be fully operational a sensu server needs to be properly connected to Redis and RabbitMQ, which is taken care of by this class.
Also, a sensu server can be more powerfull by adding plugins and handlers, which is also taken care of by this class.

This class takes the following parameters :

* checks (hash): A hash of checks to install
* handlers (hash): A hash of handlers to install
* plugins (hash): A hash of plugins to install
* rabbitmq_user (string): The RabbitMQ user to connect to
* rabbitmq_password (string): The RabbitMQ password
* rabbitmq_vhost (string); The RabbitMQ virtual host to connect to


Components Configuration
------------------------

Following is the list of the puppet modules used for Spinal Stack logging infrastructure :

=================== ===============================================
Name                Homepage
=================== ===============================================
sensu-puppet        https://github.com/enovance/sensu-puppet
yelp-uchiwa         https://github.com/enovance/yelp-uchiwa
puppet-redis        https://github.com/enovance/puppet-redis
puppetlabs-rabbitmq https://github.com/enovance/puppetlabs-rabbitmq
=================== ===============================================

Each and every of those modules are highly configurable. Listed below are the most important parameters for a proper integration.
If those parameters is not enough for your needs, please refer to the implementation of the actual module.

sensu-puppet
************

=============================== ========= =================================================================
name                            default   purpose
=============================== ========= =================================================================
sensu::server                   false     Include the sensu server
sensu::api                      false     Include the sensu api
sensu::rabbitmq_port            5672      RabbitMQ port to be used by sensu
sensu::rabbitmq_host            localhost Host running RabbitMQ for sensu
sensu::rabbitmq_user            sensu     Username to connect to RabbitMQ with sensu
sensu::rabbitmq_password        ''        Password to connect to RabbitMQ with sensu
sensu::rabbitmq_vhost           sensu     Virtual host to connect to RabbitMQ with sensu
sensu::rabbitmq_ssl             false     Use SSL transport to connect to RabbitMQ
sensu::rabbitmq_ssl_private_key undef     Private key to be used by sensy to connect to RabbitMQ
sensu::rabbitmq_cert_chain      undef     Private SSL cert chain to be used by sensy to connect to RabbitMQ
sensu::redis_host               localhost Hostname of Redis to be used by sensu
sensu::redis_port               6379      Redis port to be used by sensu
sensu::api_bind                 0.0.0.0   IP to bind the api service to
sensu::api_host                 localhost Hostname of the sensu api serice
sensu::api_port                 4567      Port of the sensu api service
sensu::api_user                 undef     User of the sensu api service
sensu::api_password             undef     Password of the sensu api service
sensu::subscriptions            undef     Default subscriptions used by the client
=============================== ========= =================================================================

yelp-uchiwa
***********

==================== ======= ==============================================================
name                 default purpose
==================== ======= ==============================================================
uchiwa::install_repo true    Should the package manage the repos
uchiwa::user         ''      Username to access the dashboard. Leave empty for none.
uchiwa::pass         ''      Password to access the dashboard. Leave empty for none.
uchiwa::host         0.0.0.0 IP address / hostname to bind the monitoring dashboard to
uchiwa::port         3000    Port to bind the monitoring dashboard to
uchiwa::stats        10      Determines the retention, in minutes, of graphics data
uchiwa::refresh      1000    Determines the interval to pull the Sensu API, in milliseconds
==================== ======= ==============================================================

puppet-redis
************

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

puppetlabs-rabbitmq
*******************

This class is managed by cloud::messaging, please refer to it for it's configuration.

Hiera
-----

agent
*****

Since the class cloud::monitoring::agent::sensu simply install the sensu packages no configuration is required hiera wise.

server
******

This is an example of the configuration of the sensu monitoring server
######################################################################

.. code-block:: none

  ---
  sensu::server: true
  sensu::api: true
  sensu::rabbitmq_password: password
  sensu::rabbitmq_host: brk01
  sensu::rabbitmq_vhost: /sensu
  uchiwa::user: admin
  uchiwa::password: password
  uchiwa::host: "%{::ipaddress}"
  uchiwa::install_repo: false
  cloud::monitoring::server::sensu::checks:
    check_ntp:
      command: /path/to/check/check_ntp.sh
    check_https:
      command: /path/to/check/check_http.sh
  cloud::monitoring::server::sensu::rabbitmq_user: sensu
  cloud::monitoring::server::sensu::rabbitmq_password: password
  cloud::monitoring::server::sensu::rabbitmq_vhost: '/sensu'

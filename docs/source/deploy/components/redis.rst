Redis
=====

Redis_ is an advanced key-value cache and store. In Spinal Stack it is used as :

  * a backend for Ceilometer
  * a backend for Sensu API

Configuration
-------------

============================= ================================================================================================================================ ============================
Configuration                 Description                                                                                                                      Default
============================= ================================================================================================================================ ============================
redis                         Enable HAProxy binding                                                                                                           true
redis_bind_ip                 IP address on which Redis will be listening                                                                                      Internal Network IP
redis_port                    Port on which Redis will listening                                                                                               6379
redis_bind_options            HAProxy bind options                                                                                                             HAProxy default bind options
redis_haproxy_port            Port on which HAProxy will bind to for Redis traffic                                                                             6379
sentinel_port                 Port on which sentinel will listening                                                                                            26739
sentinel_down_after           Time in ms an instance should not be reachable for a Sentinel starting to think it is down                                       1000
sentinel_failover_timeout     The time needed to restart a failover after a previous failover was already tried against the same master by a given Sentinel    1000
sentinel_notification_script  Script called for any sentinel event that is generated in the WARNING level (for instance -sdown, -odown, and so forth)          /bin/redis-notifications.sh
redis_master_name             Hostname of the Redis master                                                                                                     -
redis_master_ip               IP address of the Redis master                                                                                                   redis_master_name
sentinel_haproxy_monitor_ip   IP address of the HAProxy Redis is ran behind                                                                                    Public Virtual IP
sentinel_haproxy_monitor_port Port of the HAProxy API Redis is ran behind                                                                                      10300
============================= ================================================================================================================================ ============================

.. warning::
   The redis_master_name parameter is mandatory. Without it the deployment of Redis in a HA setup will fail.

puppet-openstack-cloud
----------------------

* cloud::database::nosql::redis::server
* cloud::database::nosql::redis::sentinel

Non-HA Setup
------------

In a non-HA setup, only the following parameters are necessary to configure: redis, redis_bind_ip, redis_port, redis_bind_options, redis_haproxy_port.

To deploy Redis in a non-HA setup, include the 'database::nosql::redis::server' on the concerned node in your scenario and adjust the aforementioned parameters in the environment file.


HA Setup
--------

In an HA setup, all the aforementioned parameters will have an impact on your setup.

To deploy Redis in HA setup, include both 'database::nosql::redis::server' and 'database::nosql::redis::sentinel' on the concerned nodes in your scenario.

How does it work ?
^^^^^^^^^^^^^^^^^^

.. figure:: ./img/haredis.png
   :align: center
   :alt: HA Redis architecture

   HA Redis architecture

Redis is deployed in an active/passive setup. Redis stream will always land on the same Redis server, the master, through HAProxy.
On the node where Redis runs, there is another process called Sentinel that is also running, this process is in charge of electing a master in case the master fails.
If the master fails, the sentinel will detect this change and notify HAProxy to route the Redis stream to the new elected Redis master.

.. _Redis: http://www.redis.io

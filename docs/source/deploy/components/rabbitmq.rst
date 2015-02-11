RabbitMQ
========

RabbitMQ is a message broker software. It enables the various part of Openstack to communicate together.

Configuration
-------------

======================== ======================================================== =================================================================
Configuration            Description                                              Default
======================== ======================================================== =================================================================
rabbit_names             An array of rabbit servers hostnames                     An array of the controller hostnames
rabbit_hosts             An array of rabbit servers ip addresses                  An array of the controller node ip with the port ':5672' suffixed
rabbit_password          RabbitMQ password                                        rabbitpassword
rabbit_cluster_node_type RabbitMQ cluster node type                               disc
rabbit_port              Port on which RabbitMQ will listen on                    5672
rabbit_ip                Ip address of RabbitMQ interface                         IP of the node it is one
rabbit                   Boolean to described if RabbitMQ should be load balanced false
rabbit_bind_options      An array of HAProxy bind options                         Global HAProxy binding options array
erlang_cookie            Erlang cookie to use                                     No default, the parameter is required
======================== ======================================================== =================================================================

Upgrade from I.1.3.0 to J.1.0.0
-------------------------------

This is a special note when upgrading from I.1.3.0 to J.1.0.0.
Due to the new puppetlabs-rabbitmq implementation, a new parameter is now required in the environment file: erlang_cookie.

If you are already running Spinal Stack I.1.3.0 and you want to upgrade to J.1.0.0, you will have to look at the current content of /var/lib/rabbitmq/.erlang.cookie and copy it to erlang_cookie parameter, so your upgrade will work correctly.
If you miss the parameter, config-tools scripts (configure.sh) will fail to run.
If you set a cookie in your env, that is different from what you had in I.1.3.0, the upgrade will fail and you'll see an error message in the upgrade Jenkins job.
These failures are here to prevent any issue during the upgrade and allow you to maintain your cluster up and running.

.. _RabbitMQ: http://www.rabbitmq.com/

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

.. _RabbitMQ: http://www.rabbitmq.com/

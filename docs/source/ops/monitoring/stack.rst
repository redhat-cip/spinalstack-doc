Monitoring Stack Components
===========================

Before tackling how to configure monitoring in Spinal Stack, the following is the list of components used to create the monitoring stack. Every component is open-source and freely available.

========= ==================== =========== ====================== =================================================
Name      Role                 Technology  License                Homepage
========= ==================== =========== ====================== =================================================
RabbitMQ  Transport layer      Erlang      Mozilla Public License http://www.rabbitmq.com/
redis     Data store           C           BSD                    http://www.redis.io/
Sensu     Monitoting router    Ruby        MIT                    http://www.sensuapp.org/
Uchiwa    Dashboard for sensu  NodeJS      MIT                    http://sensuapp.org/docs/latest/dashboards_uchiwa
========= ==================== =========== ====================== =================================================


RabbitMQ
--------

RabbitMQ is a robust and scalable AMQP broker - among other protocols.

In Spinal Stack, it allows Sensu server to publish a set of check to specific exchanges, within the sensu vhost, and
receive the results published by the clients in return.

Redis
-----

Redis is an advanced key-value cache and store

In Spinal Stack, it allows Sensu server to store persistent data with the need of a full-blown RDBMS.

Sensu
-----

Sensu is often described as the “monitoring router”. Essentially, Sensu takes the results of “check” scripts run across many systems, and if certain conditions are met; passes their information to one or more “handlers”.

In Spinal Stack, it is the core monitoring components. Some checks are common to every nodes (ie. system checks), other are standalone checks and test nodes role specific feature.

Uchiwa
------

Uchiwa is the dashboard that let the administrator interact with Sensu.

In Spinal Stack, it is the component that will be the entry point for every monitoring related task, be it down-timing an alert, checking the health of the various nodes, etc...

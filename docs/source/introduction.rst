.. _introduction:

Introduction
============

Spinal Stack is the solution to install OpenStack in production at eNovance.


Project features
----------------

1. Bare-metal provisioner: hardware discovery, validation and configuration
2. Configure OpenStack services to be highly available and scalable
3. Flexible environments to adapt to various needs
4. Post-deployment checks and runset of tests to validate the deployed OpenStack
5. Orchestration of upgrades without downtime
6. View logs in real-time through the UI
7. Support for Red Hat
8. Based 1OO% on upstream


OpenStack features
------------------

Spinal Stack is released with all OpenStack core projects.

===================== ========== ==================== ===================================
service               component  backends/drivers     notes
===================== ========== ==================== ===================================
load-balancer         HAproxy    -                    SSL termination support
clustering            Pacemaker  Corosync             -
cache                 Memcached  -                    used by Horizon & service group API
dashboard             Horizon    -                    SSL support
database              MySQL      Galera               MariaDB packages
compute               Nova       local + RBD + NFS    RBD only on Debian
identity              Keystone   MySQL                -
image                 Glance     local + RBD + NFS    -
network               Neutron    OVS/GRE + Cisco N1KV -
orchestration         Heat       -                    -
telemetry             Ceilometer MongoDB              -
block storage         Cinder     RBD + NetAPP + iSCSI -
object storage        Swift      -                    -
logging               fluentd    -                    with kibana3 & elasticsearch
monitoring            sensu      -                    with uchiwa dashboard
database as a service Trove      -                    experimental now
message queuing       RabbitMQ   -                    HA queues (cluster mode)
===================== ========== ==================== ===================================

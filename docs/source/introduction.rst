.. _introduction:

Introduction
============

Spinal Stack is the solution to install OpenStack in production at eNovance.


Project features
----------------

1. Bare-metal provisionner: hardware discovery, validation and configuration
2. Configure OpenStack services to be high available and scalable
3. Flexible environments to adapt at the needs
4. Post-deployment checks and running a set of tests for validating deployed OpenStack
5. Orchestration of upgrades without downtime
6. View logs in real-time through UI
7. Support for Red Hat and Debian
8. Based on 1OO% upstream


OpenStack features
------------------

Spinal Stack is released with all OpenStack core projects.

===================== ========== ================ ===================================
service               component  backends/drivers notes
===================== ========== ================ ===================================
load-balancer         HAproxy    -                SSL termination support
clustering            Pacemaker  Corosync         manage ceilometer-agent-central
cache                 Memcached  -                used by Horizon & service group API
dashboard             Horizon    -                SSL support
database              MySQL      Galera           MariaDB packages
compute               Nova       local + RBD      RBD only on Debian
identity              Keystone   MySQL            -
image                 Glance     local + RBD      -
network               Neutron    OVS + GRE        -
orchestration         Heat       -                -
telemetry             Ceilometer MongoDB          -
block storage         Cinder     RBD + NetAPP     -
object storage        Swift      -                -
logging               fluentd    -                with kibana3 & elasticsearch
database as a service Trove      -                experimental now
message queuing       RabbitMQ   -                HA queues (cluster mode)
===================== ========== ================ ===================================

Workflow
========

Overview
--------

An image should be put here

Details
-------

1. On the nodes

On each and every node, a sensu agent has to be enabled.
A sensu agent has two roles :

  * Subscribe the node to a profile of checks
  * Return the status of standalone checks

2. On the monitoring server

On the monitoring server, sensu, sensu-api and redis are set up. This is where the profile of checks are described and the returned values of checks stored.

3. Monitoring dashboard

In the simplest case scenario, Uchiwa the Community Sensu Dashboard is install on the same host as the sensu server. However one is free to set it up wherever
one needs and change the configuration appropriately.



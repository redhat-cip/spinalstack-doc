Workflow
========

Overview
--------

An image should be put here

Details
-------

1. On the nodes

On each and every node, either rsyslog or fluentd agent should be configured to export logs to the log server.
Based on the chosen option (rsyslog/fluentd), log formatting/pruning is applied on the agent nodes.

2. On the log server

On the log servers, fluentd is configured to listen for incoming logs. Based on the chosen options on the agents logging configuration, it could be UDP (rsyslog), TCP (fluentd/forward), HTTP (fluentd/http).
If logs are not formatted on the agents, they can be formatted on the log server using fluentd matches.
Once logs are well formatter they are then sent to an ElasticSearch cluster to be stored.

3. Logs retrieval

To view, query and analyze the logs, the user must log onto Kibana. There s/he will be provided with all the power of Lucene, a full-featured text search library, to query the logs.

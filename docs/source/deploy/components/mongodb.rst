MongoDB
========

MongoDB is a document oriented database. In Spinal Stack it is used as a backend for ceilometer.

Configuration
-------------

===================== =================================================== =================================================
Configuration         Description                                         Default
===================== =================================================== =================================================
mongo_nodes           An array of the MongoDB cluster nodes               An array with the controller private ip addresses
mongo_nojournal       Boolean to describe if journaling is disabled       false
mongo_replset_members An array of MongoDB instance part of the replicaset An array with the machine hostname
replicatset_enabled   Boolean to described if replicaset is enabled       true
mongodb_version       The version of MongoDB to install                   2.4.0
===================== =================================================== =================================================

.. _MongoDB: https://www.mongodb.org/

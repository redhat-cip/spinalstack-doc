Memcached
=========

Memcached is an in-memory key-value store for small chunks of arbitrary data (strings, objects) from results of database calls, API calls, or page rendering.
In Spinal Stack, and in Openstack in general it is used by Keystone.

Configuration
-------------

====================== ==================================== ==================================================================
Configuration          Description                          Default
====================== ==================================== ==================================================================
memcache_servers       An array of memcached servers        An array of the controller node ip with the port ':11211' suffixed
====================== ==================================== ==================================================================

.. _Memcached: http://memcached.org/

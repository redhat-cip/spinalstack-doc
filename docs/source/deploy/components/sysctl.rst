sysctl
======

When deploying Spinal Stack a user might have the need to sysctl rules.
This is possible by configuring the sysctl_rules variable in your environment.

Configuration
-------------

============= ======================== ====================
Configuration Description              Default
============= ======================== ====================
sysctl_rules  An hash of sysctl rules  Refer to the example
============= ======================== ====================

By default sysctl_rules is set to empty.

This is an example of usage:

.. code-block:: none

  ---
  sysctl_rules:
    'net.ipv4.ip_forward':
      value: '1'
    'net.ipv6.conf.all.forwarding':
      value: '1'

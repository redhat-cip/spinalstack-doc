Limits
======

When deploying Spinal Stack a user might have the need to specify security limits (with pam_limits) rules.
This is possible by configuring the limit_rules variable in your environment.

Configuration
-------------

============= ======================== ====================
Configuration Description              Default
============= ======================== ====================
limit_rules   An hash of limits rules  Refer to the example
============= ======================== ====================

By default limit_rules is set to empty.
An deployer can add limits rules for MySQL (useful in production) like this:

.. code-block:: none

  ---
  limit_rules:
    'mysql_nofile':
      ensure: 'present'
      user: 'mysql'
      limit_type: 'nofile'
      both: '16384'

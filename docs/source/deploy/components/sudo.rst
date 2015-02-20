Sudo
====

When deploying Spinal Stack a user might have the need to specify sudoers rules for users.
This is possible by configuring the sudo_configs variable in your environment.

Configuration
-------------

============= ======================== ====================
Configuration Description              Default
============= ======================== ====================
sudo_configs  An hash of sudoers rules Refer to the example
============= ======================== ====================

By default sudo_configs is set with the following content (for sensu proper operations)

.. code-block:: none

  ---
  'sensu':
    'content' : "Defaults:sensu !requiretty\nDefaults:sensu secure_path = /usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin\nsensu ALL = NOPASSWD:ALL"

An administrator can add more sudoers rules or override the sensu sudoer rule like this

.. code-block:: none

  ---
  sudo_configs:
    'user1':
      content: 'Defaults:user1 !requiretty'
    'user2':
      content: 'Defaults:user2 !requiretty'
    'sensu':
      content: 'Default:sensu requiretty'

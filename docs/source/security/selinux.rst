SElinux
=======

Spinal Stack is able to run SElinux in 'enforced' mode on RHEL7.

Overview
--------

Security-Enhanced Linux (SELinux) is a Linux kernel security module that provides a mechanism for supporting access control security policies, including United States Department of Defense–style mandatory access controls (MAC).
Spinal Stack is able to enable SElinux in 'enforced' mode in a flexible way where you can configure your own booleans and modules.

Configuration
-------------

Enforce SElinux
###############

To tell Spinal Stack to enable SElinux, apply the following configuration in your environment file :

.. code-block:: none

  selinux_mode: enforcing
  selinux_directory: /usr/share/selinux/custom/

Adding your own configuration
#############################

In advanced deployments, you will need to tweak SElinux policy, and Spinal Stack is able to configure custom policies. You can use the Hiera per host or per profile to specify the extra booleans and modules like this:

.. code-block:: none

  ---
  cloud::selinux_booleans:
    - global_ssp
    - haproxy_connect_any
    - rsync_full_access
    - swift_can_network
    - nis_enabled
    - domain_kernel_load_modules
    - virt_use_execmem
    - httpd_can_network_connect
  cloud::selinux_modules:
    - systemctl
    - nova-cert
    - nova-consoleauth
    - nova-scheduler
    - keepalived_haproxy
    - keepalived_init
    - mysql_rsync
    - swiftobject-sps


Note that this is a first implementation of SElinux in Spinal Stack. This feature remains experimental for now and is not supported on install-server nodes.

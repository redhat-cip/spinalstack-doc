Firewall
========

Spinal Stack is able to configure IPtables rules on the nodes.

Overview
--------

The firewalling implementation is very flexible and disabled by default.
If you enable it without specific paramters, all the rules will be created depending of the services that are actually running. Example, if you have a Nova API node, this node will automatically have Nova API firewall rules (8774 tcp by default).

Configuration
-------------

Activate firewalling
####################

To tell Spinal Stack to enable firewalling, apply the following configuration in your environment file :

.. code-block:: none

  manage_firewall: true

Personalize firewalling
#######################

In advanced deployments, you will need to tweak IPtables rules, and Spinal Stack is able to configure custom rules. You can use the Hiera per host or per profile to specify the extra rules like this:

.. code-block:: none

  ---
  cloud::firewall_rules:
    '300 allow custom application 1':
      port: 999
      proto: udp
      action: accept
    '301 allow custom application 2':
      port: 8081
      proto: tcp
      action: accept


Note that these rules will be applied during the deployment.
If you need to create rules before the deployment, you can use "cloud::firewall_pre_extras".
Or if you need to create rules after the deployment, you can use "cloud::firewall_post_extras".

Also, if you want to purge IPtables rules on all the nodes before applying Spinal Stack rules (and eventually your custom rules), you can set purge_firewall_rules to "true" in your environment file.

Limit OpenStack API requests
############################

Some OpenStack projects does not have ratelimit middleware so you may want to use IPtables to limit the requests on the endpoints.

Example with Keystone:

.. code-block:: none
zsh:1: command not found: q
  ---
  cloud::identity::firewall_settings:
    '301 limit keystone API requests':
        rate_limiting: 50/sec

With this parameter, Keystone API will get 50 requests per second maximum.


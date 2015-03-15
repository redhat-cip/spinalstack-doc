Neutron
=======


L3 Agent HA
-----------

=========================================== ================================ =======
Configuration                               Description                      Default
=========================================== ================================ =======
neutron_l3_ha_enabled                       Enable/disable HA with VRRP_     false
neutron_l3_ha_vrrp_auth_type                VRRP auth type                   PASS
neutron_ha_vrrp_auth_password               VRRP auth password               -
neutron_l3_allow_automatic_l3agent_failover Enable/disable auto rescheduling false
neutron_l3_agent_mode                       Working agent for the agent      legacy
enable_distributed_routing                  Enable/disable DVR_              false
=========================================== ================================ =======

.. warning::
    In OpenStack Juno, DVR & VRRP can't work together. That means you can't enable neutron_l3_ha_enabled and enable_distributed_routing in the same time. If you try to do it, Puppet will fail to avoid any configuration error.

.. warning::
    In OpenStack Juno, VRRP can't work with l2population mechanism driver. Ensure neutron_mechanism_drivers parameter does not contain l2population in the array. Please read this blogpost_ for more informations about HA compatiblity.

.. note::
    neutron_l3_allow_automatic_l3agent_failover allows to re-schedule virtual routers if a Neutron L3 agent goes down.
    If neutron_l3_ha_enabled is enabled, this is not useful to enable neutron_l3_allow_automatic_l3agent_failover.

.. _VRRP: https://wiki.openstack.org/wiki/Neutron/L3_High_Availability_VRRP

.. _blogpost: http://assafmuller.com/2014/12/30/juno-advanced-routing-compatibility/

.. _DVR: https://wiki.openstack.org/wiki/Neutron/DVR

More documentation about `L3 configuration`_ on official manual.

.. _`L3 configuration`: http://docs.openstack.org/juno/config-reference/content/networking-options-l3_agent.html

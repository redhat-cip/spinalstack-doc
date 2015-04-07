Introduction
============

The deployment of Spinal Stack is done by 3 steps:

- **bootstrap**: manage lifecycle of servers (hardware + Operating System + packages)
- **configuration**: configure all services to make Spinal Stack working
- **sanity**: ensure Spinal Stack is deployed and working as expected by running advanced
  functionnal testing


Requirements
------------

Before running a deployment, there are some requirements to understand the way Spinal Stack works do be deployed.

====================== ==================================== =========================
step                   project(s)                            external documentation
====================== ==================================== =========================
bootstrap              eDeploy, PXEmgr                      eDeploy_, PXEmngr_
configuration, upgrade config-tools, Puppet, Hiera, Ansible Puppet_, Hiera_, Ansible_
sanity                 Tempest                              Tempest_
====================== ==================================== =========================

.. _eDeploy: https://github.com/enovance/edeploy/blob/master/docs/eDeployUserGuide.rst
.. _PXEmngr: https://github.com/enovance/pxemngr/blob/master/README.rst
.. _Puppet: http://docs.puppetlabs.com/
.. _Hiera: http://docs.puppetlabs.com/hiera/1/
.. _Ansible: http://docs.ansible.com/
.. _Tempest: http://docs.openstack.org/developer/tempest/


The minimal requirements to install Spinal Stack are:

=============== ================= =====================================
Deployment type Number of Servers Operating Systems
=============== ================= =====================================
PoC, dev        4                 RHEL 7.0
Production      > 10              RHEL 7.0
=============== ================= =====================================

.. note::
    Spinal Stack can be installed on bare-metal server or in a virtualized environment.

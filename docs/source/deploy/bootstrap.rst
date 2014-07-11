Bootstrap
=========

Spinal Stack is able to be bootstrapped on physical servers or virtual machines.
In this guide, we will see the physical servers use case.

The bootstrap steps are performed by the installation server node, which will have in charge the deployment of all other nodes.

eDeploy_ is the tool to provision and update systems (physical or virtual) using trees of files instead of packages or VM images.

.. _eDeploy: https://github.com/enovance/edeploy

The installation server node is build by using install-server_ eDeploy role.

.. _install-server: https://github.com/enovance/edeploy-roles/blob/master/install-server.install

All other nodes (load-balancers, controllers, compute nodes, etc) are build by using openstack-full_ eDeploy role.

.. _openstack-full: https://github.com/enovance/edeploy-roles/blob/master/openstack-full.install

Before starting the deployment, you need to get the last build of these roles.


Build eDeploy roles
-------------------

To build eDeploy roles by yourself on your eDeploy server, you can run::

    $ export ROLES="openstack-full install-server"
    $ export VIRTUALIZED=/srv/edeploy/tools/virt.conf
    $ /srv/edeploy/tools/jenkins-build.sh <src dir> <build dir> <archive dir> [<make params>]

When making continuous integration, this step can be done by Jenkins (upstream) and distributed eDeploy roles on the installation servers (downstream).


Deploy the install-server role
------------------------------

This role contains all these components:
- eDeploy
- PXEmngr
- Puppet Master: running Passenger with Apache2 and PuppetDB on 81 port.
- Puppet Dashboard: monitor Puppet nodes using this URL: http://<install-server>:82
- Kibana3: View logs in real-time through UI using this URL: http://<install-server>:8300
- Jenkins: manage lifecyle of our infrastructure (configuration, sanity and upgrade) using this URL: http://<install-server>:8282

The first step, is to bootstrap the install-server by using eDeploy itself.
There is several ways to do it and this manual_ explains them in details.

.. _manual: https://github.com/enovance/edeploy/blob/master/docs/eDeployUserGuide.rst#id31

In the case you don't have an eDeploy server to bootstrap the install-server node, you can use the `local installation with an USB key`_ method.

.. _`local installation with an USB key`: https://github.com/enovance/edeploy/blob/master/docs/eDeployUserGuide.rst#id35

You have to follow eDeploy manual to finish the installation of eDeploy service on the install-server node.
Once we have eDeploy working, we can continue to prepare the configuration.

.. note::
    There is no need to install Puppet, Ansible or any service. They are already present in install-server role.


Deploy the openstack-full roles
-------------------------------

Once we have the install-server node ready, we can deploy all our infrastructure by using eDeploy over the network.
To start the deployment, boot the targeted server by using the proper boot device regarded the kind of deployment you choose (PXE versus USB).

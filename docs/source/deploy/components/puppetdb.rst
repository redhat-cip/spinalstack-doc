PuppetDB
========

PuppetDB_ collects data generated by Puppet. It allows to have a global overview of every node of the Spinal Stack deployment and their resource in a centralized place.

Requirements
------------

In Spinal Stack, PuppetDB is configured the following way :

  * The PuppetDB daemon runs on the install-server node with SSL disabled. Hence, it runs on ``127.0.0.1:8080``
  * Nodes still reach PuppetDB using SSL. SSL is terminated at the webserver level via a vhost listening on port ``8081`` that also serves as a proxy to ``127.0.0.1:8080``

Configuration of PuppetDB happens before step 1 since it needs to be ready for the really first run. Hence the SSL certificate needs to be present a deployment time.

How to push the PuppetDB certificate during the deployment:

  * On baremetal or virtualized deployment: push the file in your environment in ``etc/puppet/ssl/puppetdb.pem`` and it will automatically copied on the install-server.
    Spinal Stack will take care of the permissions during the bootstrap.
  * On Inception deployment, running Heat, you can provide ``puppetdb_pem`` parameter in your environment, containing the RAW data of your certificate.
    During the stack creation with Heat, cloud-init will take care to create the file on the install-server instance.

.. warning::
    To start correctly, the PuppetDB certificate has to be in place at ``/etc/puppet/ssl/puppetdb.pem`` otherwise PuppetDB won't start
    and the whole deployment will fail. If the file does not exist during the deployment, the ``configure.sh`` script will fail to avoid
    useless debug during the deployment.

.. note::
    The ``puppetdb.pem`` is a *standard* webserver SSL certificate (see security guide's :doc:`../../security/ssl` section for more details)


Configuration
-------------

====================== ==================================== =========================
Configuration          Description                          Default
====================== ==================================== =========================
``puppetdb_server``    The PuppetDB server URL              None
``puppetdb_pem``       The PuppetDB certificate             None
====================== ==================================== =========================

.. _PuppetDB: https://docs.puppetlabs.com/puppetdb/latest/

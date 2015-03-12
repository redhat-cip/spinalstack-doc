Upgrade guide
=============

Spinal Stack aims to provide continuous integration and continuous delivery.
Upgrades are fully automated and ensure the minimum of service downtime.

They only work from n to n+1 releases and it's not possible to skip one release.

The upgrade is orchestred by 3 steps:

- Generate the new configuration on the install-server. Example with J.1.0.0 (release that we want) on 3 nodes::

    $ git clone git@github.com:enovance/config-tools.git
    $ cd config-tools
    $ ./provision.sh -i J.1.1.0 git@my-env-git-repo:spinalstack-env/env-joe.yaml version=RH7.0-J.1.1.0 stable=J.1.0.0

- Run `upgrade` Jenkins job to perform Spinal Stack upgrade. It will automatically:

  - Prepare OpenStack nodes to be upgraded by running the `ansible playbooks`_ with `before_config` tag.
  - Run Puppet on all the nodes and re-validate all the steps.
  - Run some actions on OpenStack nodes by running the `ansible playbooks`_ with `after_config` tag.

- Run `sanity` Jenkins job to ensure that we still have Spinal Stack working as expected.

If you want to know how upgrades work under the hood, look `how upgrade are automatized` page.

.. _`ansible playbooks`: https://github.com/enovance/edeploy-roles/blob/master/upgrade

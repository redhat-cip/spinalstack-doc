Sanity guide
============

After deploying Spinal Stack, it's very important to validate that our deployment is working as expected and all features are correctly working.

Jenkins will have in charge to run the `sanity` job which will perform two actions:

- Start OpenStack resources (instances, volumes, networks, etc). Before creatind the resources, it checks if they already exist. If it's the case, it considers that Sanity already built before. This is useful when running `sanity` after the `upgrade` process. Currently, it's done by a set of bash scripts, the time Javelin2 (sub-project within Tempest) is being ready.
- Run 1600 functionnal tests in Tempest (API + CLI)

.. warning::
    This guide will be written soon, while it's still work in progress to move the work in Spinal Stack.

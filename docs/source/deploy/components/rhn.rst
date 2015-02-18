RHN
===

When deploying Spinal Stack on Red Hat, you need to configure your RHN subscription.

This is a parameter to set in your environment file:


Configuration
-------------

================ =============== =======
Configuration    Description     Default
================ =============== =======
rhn_registration RHN Credentials -
================ =============== =======

.. code-block:: none

  ---
  rhn_registration:
    username: joe
    password: secrete

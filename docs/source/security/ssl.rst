SSL
===

Currently, Spinal Stack can terminate SSL only on the loadbalancer (HAProxy) (Since version I-1.1.0). It is in the current plan to provide also the possibility to terminate SSL on each node using an SSL Proxy.

Overview
--------

Put an image here, with rows showing which part is https ans which part is not

Configuration
-------------

Certificates
############

Vendor Certificate
******************

Generate the PEM file
:::::::::::::::::::::

Before moving forward with the SSL configuration, the operator needs to have a valid pem file.

There are two cases possible in order to generate a proper pem file :

* The operator has a SSL chain file : The correct pem file is the concatenation, in this exact order of : chain file + private key + certificate authority

* The operator has no SSL chain file : The correct pem file is the concatenation, in this exact order of : private key + certificate authority

Self-Signed Certificate
***********************

There are some limitations with a self-signed certificate. Most of openstack-client commands should use '''--insecure'''. Whithin the spinalstack deployment you must trust your certification authority on all the servers (openstack and install-server).

Generate CA and certificates
::::::::::::::::::::::::::::

.. code:: bash

   mkdir self-signed-cert && cd self-signed-cert
   # change theses values to match your requirements
   # country, 2 letters
   country="my_country"
   location="my_town"
   organisation="my_organisation"
   domain="my_domain.com"

   # generate the CA
   openssl req -days 3650 -out ca.pem -new -x509 -subj /C=$country/L=$location/O=$organisation

   # generate a private key for your servers
   openssl genrsa -out star_$domain.key 2048

   # create csr
   openssl req -key star_$domain.key -new -out star_$domain.req -subj /C=$country/L=$location/O=$organisation/CN=*.$domain
   echo '00' > file.srl

   # generate the crt file
   openssl x509 -req -in star_$domain.req -CA ca.pem -CAkey privkey.pem -CAserial file.srl -out star_$domain.crt

   # generate the pem file for haproxy
   cat star_$domain.key star_$domain.crt > star_$domain.pem

   # you can rename star_my_domain.com.* star_my_domain_com.* if needed

Trust the CA
::::::::::::

You have to add the ca on install-server and all the others servers, for Redhat systems:

.. code:: bash

  cp ca.pem /etc/pki/ca-trust/source/anchors/
  update-ca-trust

Configure HAProxy
#################

The Spinal Stack loadbalancer class, allows the operator to enable SSL for the public facing API endpoints. It does work the same way for internal's and admin's one.

To enable SSL for a specific service, the loadbalancer.pp parameter named SERVICENAME_bind_options should contain ['ssl', 'crt', '/path/to/pem'].

So for example if one wants to set the nova api, to only accept SSL, I would set the following in the matching hiera file :


.. code-block:: none

  ---
  cloud::loadbalancer::nova_bind_options:
    - ssl
    - crt
    - /patch/to/pem


Configure Endpoints
###################

Configuring the HAProy is just the first part of an SSL configuration. Keystone should also be aware to use https when talking to a specific interface. Hence, when registring the various services, the 'https' protocol should be specified for the various SSL aware services.

To tell Spinal Stack that nova should be contacted via 'https' on its publicUrl, apply the following configuration in your hiera file :

.. code-block:: none

  ---
  cloud::identity::ks_nova_public_proto: https


To tell Spinal Stack that nova should be contacted via 'https' on its internalUrl, apply the following configuration in your hiera file :

.. code-block:: none

  ---
  cloud::identity::ks_nova_internal_proto: https


To tell Spinal Stack that nova should be contacted via 'https' on its adminUrl, apply the following configuration in your hiera file :

.. code-block:: none

  ---
  cloud::identity::ks_nova_admin_proto: https

.. note::
    Do not enable SSL for Nova Metadata API and Neutron Metadata Agent. This feature is not supported yet.

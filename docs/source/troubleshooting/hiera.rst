Hiera
=====

Hiera is a key/value lookup tools, fully integrated with Puppet, that retrieves datas from a preconfigured backend.

In Spinal Stack, hiera holds a key role. The Spinal Stack puppet module should *never* be edited, the whole deployment is data driven, hence hiera driven.

For those reason a good understanding of hiera and how to troubleshoot it is a required skill for sucessful deployments.

Principles
----------

The following is the declaration of our messaging class

.. code-block:: none

  class cloud::messaging(
    $cluster_node_type = 'disc',
    $rabbit_names      = $::hostname,
    $rabbit_password   = 'rabbitpassword'
  ){

As one can see it has default value for each parameters. With this current state they are three ways to specify a value to the cloud::messaging class. Hiera being the way Spinal Stack works.

1. Let the default

   One can let the default value and simply include the cloud::messaging class to use it

2. Specify specific valude

   One can specify specific values in another manifest, the call would look like the following

.. code-block:: none

  class {'cloud::messaging' :
    cluster_node_type = 'foo',
    rabbit_names      = 'rabbit.example.com',
    rabbit_password   = 'secret',
  }

3. Use Hiera

   One can use hiera to specify those values in a configured backend (yaml file, json file, mysql). Spinal Stack relies on a yaml backend at the moment. (More in Configuration file)

   The yaml hiera file would look like the following

.. code-block:: yaml

  ---
  cloud::messaging::cluster_node_type: foo
  cloud::messaging::rabbit_names: rabbit.example.com
  cloud::messaging::rabbit_password: secret

Configuration file
------------------

The configuration files is located at /etc/puppte/hiera.yaml and the default provided by Spinal Stack is the following

.. code-block:: yaml

  ---
  :backends:
    - yaml
  :yaml:
    :datadir: /etc/puppet/data
  :hierarchy:
    - "%{::type}/%{::fqdn}"
    - "%{::type}/common"
    - common

Feel free to change it to meet your needs, here the explanation of the default hiera.yaml.

  * backend: Specify the backend one wants to use (can be json, mysql, etc...)
  * yaml: Specify backend specifics, here for the yaml backend. Simply specify the directory where to find the files
  * hierarchy: Indicates, in prefered order, where to find the datas. The actual file name on the filesystem should end with .yaml.

Admiting that the fact type is 'controller' and the fact fqdn is 'controller01.example.com', this will be the order in which data will be looked for :

  * /etc/puppet/data/controller/controller01.example.com.yaml
  * /etc/puppet/data/controller/common.yaml
  * /etc/puppet/data/common.yaml

Debug
-----

From time to time one might find hiera, not popullating the variable as one would have expect, then it's time to get one's hands dirty.

Log onto the puppet master server, and start looking what hiera actually returns to puppet, using the following commands.

Find the value hiera returns to puppet
######################################

.. code-block:: none

  hiera MYVARIABLE -c /etc/puppet/hiera.yaml

The latter will show one what hiera returned to puppet.

*Note*: On the previous messaging class exmaple. If one wants to find the rabbit_names value, don't use `hiera rabbit_names -c /etc/puppet/hiera.yaml`, but `hiera cloud::messaging::rabbit_names -c /etc/puppet/hiera.yaml`. cloud::messaging::rabbit_names is your actual parameter name.


Find which files are opened by hiera to look for informations
#############################################################

.. code-block:: none

  hiera MYVARIABLE -c /etc/puppet/hiera.yaml --debug

The latter will print a trace of all the files hiera has been looking into to retrieve data.

Specify facts in your query
###########################

.. code-block:: none

  hiera MYVARIABLE -c /etc/puppet/hiera.yaml ::type=controller ::fqdn=lb001.example.com

The latter tells hiera to look for MYVARIABLE, with the fact type set to controller and the fact fqdn set to lb001.example.com


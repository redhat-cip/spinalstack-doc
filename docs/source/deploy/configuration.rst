Configuration guide
===================

After bootstrapping the servers, we need to generate the configuration.

To allow more flexibility and to easier define the parameters of Spinal Stack, config-tools_ has in charge to generate and run the configuration remotely.
Config Tools are a set of tools to use puppet to configure a set of nodes with complex configuration using a step by step approach. Each step is validated by serverspec tests before going to the next step. If the tests of a step fail, Puppet is called again on all the nodes.

.. _config-tools: https://github.com/enovance/config-tools

YAML is the format that Spinal Stack is using to generate the configuration.

To configure Spinal Stack correctly, there are two notions to understand:

**Infrastructure** (common accross multiple deployments)
    describe how Spinal Stack will be configured. It will describe the different kind of nodes,
    which services are running on them and some default parameters in the deployment.
    The infrastructure YAML files can be use by multiple deployment and should never been locked to some environment
    informations (IP address or passwords).
**Environment** (deployment specific)
    describe the details of our deployment: IP address, domain name, passwords, SSH keys, etc.
    This file should not be shared with any other deployment and has to be stored safely.


Infrastructure YAML files
-------------------------

Currently, there are two examples of infrastructure YAML file which work for 2 scenarios:
The `infrastructure YAML repository`_ is flexible enough to support multiple scenarios.

.. _`infrastructure YAML repository`: https://github.com/enovance/openstack-yaml-infra

In "scenario" directory, you will find differents scenarios that contain a "infra.yaml" file which will fit (or not) with your deployment.
If none of these scenarios fits with your deployment, you have to create your own, respecting this YAML architecture:

    * `name`: Name of the profile (ex: install-server)
    * `arity`: Define a rule for the number of servers expected in the deployment (ex: 1+n means
      at least one server with this profile)
    * `edeploy`: eDeploy role to use for the deployment. Usually, we use install-server role
      for installation server node and openstack-full role for all other nodes.
    * `steps`/`roles`: contains the different steps of the deployments by defining for each step the Puppet classes
      expected to be applied on the nodes. It's optionnal to define the steps for each profile.
    * `serverspec_config`: parameters to make `Spinal Stack serverspec tests`_ working on the profiles.
    * `ordered_profiles`: describe the order to run Puppet for each step. For example, if we want to run puppet
      on compute nodes before the controller.
    * `serverspec`: `Spinal Stack Puppet classes`_ / `Spinal Stack serverspec tests`_ names assotiation

.. _`Spinal Stack serverspec tests`: https://github.com/enovance/openstack-serverspec
.. _`Spinal Stack Puppet classes`: https://github.com/enovance/puppet-openstack-cloud


This directory is common to all scenarios:

**data**
    `data` directory contains Hiera files. Spinal Stack is structured in 3 Hiera levels:

    * `FQDN` (Full qualified domain name): parameters and classes for some hosts only. (ex: set VRRP priority on
      a load-balancer node in particular)
    * `type`: defines some classes and parameters for each profile that we created in the `infra` YAML file
    * `common`: common classes & parameters that will be applied on all the nodes of the deployment.

.. note::
    They are listed from the highest priority to the lowest.

**data/fqdn**
    Later, we will describe another YAML file which is the `environment`. In this file, we will see how
    Hiera will use at this level. FQDN hiera level is useful when we want to set specific parameters for an
    host (like the storage devices for Swift and Ceph, etc).

**data/type**
    This file contains all the parameters that we pass to the different Puppet classes.
    `config-tools` will generate the different profiles with the corresponding Puppet classes for each profile.
    If we want to override the paramater of a class in particular, we can do this at the FQDN Hiera level.
    If there is too much modifications to do at this level, it's maybe because the deployment does not fit with
    this scenario and you may have to create a new infrastructure environment.

**data/common**
    YAML file which will contain all parameters consummed by `data/type` to configure the Puppet classes.
    The actual data could be pulled from the `environment file` that we will write later (ex:
    config.mysql_root_password) or from common itself (ex: galera_ip: "%{hiera('vip_public_ip')}") by using
    Hiera syntax.

**heat**
    OpenStack Heat (Orchestration) template that could be used to run the infrastructure in OpenStack.



Environment YAML file
---------------------

The `environment` that we just talked in `infrastructure` is something specific for each deployment and has to be generated each time you deploy Spinal Stack.

There is an example of `environment` file here_.

.. _here: https://github.com/enovance/openstack-yaml-infra/blob/master/example.yml

Let's create this file (in the example it will be named joe.yml) and describe its structure:
* `infra`: infrastructure name (ex: 3nodes)

For each host:

* `hostname`: hostname of the host
* `profile`: profile name used in infra.yaml in the `infrastructure` YAML file
* `ip`: IP used to reach the servers from internal network.

.. note::
    The servers have to be reachable from the node where we will execute the configuration process.

* `config`: contains FQDN Hiera data level parameters.

After hosts description:

* `config`: contains confidentials & deployment specific informations, like IP addresses, subnets, passwords, SSH keys, etc.

This file should be stored on a private versionned git repository since it contains confidential informations.

This is an example of a 3 nodes deployment:
From the installation server, the configuration is generated by first creating this YAML file::

    **env-joe.yaml**

    module:
      git@github.com:enovance/puppet-openstack-cloud
    ansible:
      git@github.com:enovance/edeploy-roles
    serverspec:
      git@github.com:enovance/openstack-serverspec.git
    environment:
      joe
    infrastructure:
      git@github.com:enovance/openstack-yaml-infra.git
    scenario:
      ref-arch
    jenkins:
      git@github.com:enovance/jjb-openstack.git

.. note::

    If scenario is empty, the "ref-arch" will be configured by default.

.. note::
    Before continuing to the next steps, you have to ensure these following requirements:

    - installation server has Internet access
    - all nodes have the user defined in the environment YAML file (joe.yml) with sudo permissions
      (usually configured by cloud-init)
    - installation server can SSH all nodes with the user defined in the environment YAML file (joe.yml)


We are now ready to generate the Spinal Stack configuration.

Generate the configuration
--------------------------

From the installation server, run::

    $ git clone git@github.com:enovance/config-tools.git
    $ cd config-tools
    $ ./provision.sh I.1.1.0 git@my-env-git-repo:spinalstack-env/env-joe.yaml version=D7-I.1.1.0

Run the configuration
---------------------

From the installation server, connect to the Jenkins server by using this URL: http://install-server:8282 and run the job `puppet`.

Depending of the infrastructure size, the configuration can take 30 minutes or more.


During the configuration
------------------------

1. Puppet master is being prepared by **config-tools** `provision.sh` script. It will take care of Puppet
   configuration, Hiera data dynamic management (according to the steps) and the install-server will be
   puppetized itself.
2. Then the deployment of OpenStack nodes is starting:

   - Step 1: Memcached, MySQL, MongoDB, RabbitMQ,i logging agent, Ceph monitor and Ceph Key management
   - Step 2: HAproxy, Ceph OSD, Horizon, Swift storage nodes
   - Step 3: Keystone users, tenants, services and endpoints; Create Ceph storage pools
   - Step 4: OpenStack API, schedulers and compute services
   - Step 5: OpenStack Networking services

For each step, Puppet is run on the nodes where the step is needed. Puppet is run until serverspec tests pass with a limit of 5 times. If after 5 times of Puppet run, the serverspec tests still fail, Jenkins job will fail and provide an output to let the deployer know which tests fail.
The concept of steps make easy the debugging when something is wrong during the deployment process.

.. warning::
    serverspec tests do not certify OpenStack is up and running. They just validate that Puppet did what we expected from the configuration point of view.

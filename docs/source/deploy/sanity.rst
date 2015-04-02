Sanity guide
============

Spinal Stack presents a validation suite that can be run after each deployment to ensure that every feature is working correctly. It consists of a series of tests based on tools developed by the upstream OpenStack QA project. This test suite is referred to as "Sanity tests" and can be launched from Jenkins under the name 'Sanity'. As of the J.1.1.0 version, this job uses 2 tools:

- Javelin
- Tempest

Javelin
-------

Javelin is a tool that allows the creation, check and destruction of some OpenStack resources. We use it to validate that existing resources are able to survive a version upgrades. Here's how it works:

1. Before running, Javelin will compare the current version of Spinal Stack with the latest version tested. The current version is provided by the tool `edeploy` and the latest version tested is fetched from disk. This will determine if Javelin is running before or after an upgrade process.

2. If Javelin is running before an upgrade (or if it's a first run after the initial deployment), it will simply create a bunch of resources.

3. If Javelin is running after an upgrade (in other words, if the current version of Spinal Stack is superior to the latest version tested), Javelin will check for the existence of the OpenStack resources. If it cannot find them, Javelin considers that the resources didn't survive the upgrade and will report an error.

4. After every run, Javelin will update the latest tested version on disk with the current version of Spinal Stack.

The latest version tested by Javelin can be found on the install server, in the file: `/opt/tempest-scripts/javelin-latest-run`

The resources manipulated by Javelin are described in a Yaml file managed by the Sanity suite. As of the J.1.1.0 release, here are the resources:

.. highlight:: yaml
   # This is a yaml description for the most basic definitions
   # of what should exist across the resource boundary. Perhaps
   # one day this will grow into a Heat resource template, but as
   # Heat isn't a known working element in the upgrades, we do
   # this much simpler thing for now.
   tenants:
   - javelin
   - discuss
   users:
   - name: javelin
     pass: gungnir
     tenant: javelin
   - name: javelin2
     pass: gungnir2
     tenant: discuss
   secgroups:
   - name: angon
     owner: javelin
     description: angon
     rules:
     - 'icmp -1 -1 0.0.0.0/0'
     - 'tcp 22 22 0.0.0.0/0'
   - name: baobab
     owner: javelin
     description: baobab
     rules:
     - 'tcp 80 80 0.0.0.0/0'
   # resources that we want to create
   images:
   - name: javelin_cirros
     owner: javelin
     imgdir: images
     file: cirros-0.3.2-x86_64-blank.img
     format: ami
     aki: cirros-0.3.2-x86_64-vmlinuz
     ari: cirros-0.3.2-x86_64-initrd
   volumes:
   - name: assegai
     server: peltast
     owner: javelin
     gb: 1
     device: /dev/vdb
   - name: pifpouf
     server: hoplite
     owner: javelin
     gb: 2
     device: /dev/vdb
   networks:
   - name: world1
     owner: javelin
   - name: world2
     owner: javelin
   subnets:
   - name: subnet1
     range: 10.1.0.0/24
     network: world1
     owner: javelin
   - name: subnet2
     range: 192.168.1.0/24
     network: world2
     owner: javelin
   routers:
   - name: connector
     owner: javelin
     gateway: true
     subnet:
     - subnet1
     - subnet2
   servers:
   - name: peltast
     owner: javelin
     flavor: m1.small
     image: javelin_cirros
     networks:
     - world1
     secgroups:
     - angon
     - baobab
   - name: hoplite
     owner: javelin
     flavor: m1.medium
     image: javelin_cirros
     networks:
     - world2
     secgroups:
     - angon
   objects:
   - container: jc1
     name: javelin1
     owner: javelin
     file: /etc/hosts
   telemetry: true



Tempest
-------

Tempest is the suite of functional tests that is used in the gate to validate every API call against new developments. We use it to validate that the deployment went well. When Sanity is launched, it will take care of:

- Generating a tempest.conf relevant to the Spinal Stack deployment
- Create the users, tenants, roles and images that will be used in the tests
- Launch over 1600 functional tests, testing API and CLI
- Report the tests result in the Jenkins interface

A couple of things to note about Tempest:

- Tempest will create a bunch of resources during its tests. These resources will be cleaned up after Sanity, even in the case of tests failure.
- Sanity enables a feature of Tempest called "tenant isolation", which means that every resource created by a test will be only visible inside its own tenant. This allows parallel execution of the tests while preventing potential race conditions.

More info
^^^^^^^^^

For more information on Tempest, you should read:

- the Spinal Stack Tempest configuration file, located at: `/usr/share/openstack-tempest-<version>/etc/tempest.conf`, where <version> stands for the OpenStack version in use ('icehouse' or 'juno' for instance).
- the `[upstream Tempest documentation] <http://docs.openstack.org/developer/tempest/>`_

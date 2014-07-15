bug
===

**I have an issue to deploy my physical / virtual nodes by using edeploy**

You may have to report the bug in eDeploy github.


**I face an issue with packages within the eDeploy role (wrong version or missing package)**

File a bug on eDeploy roles github.


**Configuration fails to be generated**

File a bug on config-tools github.


**Infrastructure YAML files have wrong informations**

File a bug on the YAML infra github that you use in your deployment.


**Some services are not well configured or do not run correctly**

It will require some debug to determine if the issue comes from the infra/env YAML files or from upstream Puppet modules
directly.
After some investigation in Puppet, you can determine if:

- a wrong value has been setted in a configuration file. There is a chance the issue comes from infra/env YAML.
- a service is up but has unregular behavior. It could be in the upstream puppet modules.

All OpenStack Puppet modules use Launchpad to file bugs. Other modules usually use github.


**The Puppet catalog does not compile**

It's highly possible that the issue comes from puppet-openstack-cloud module, which is the high-level module to configure Spinal Stack.

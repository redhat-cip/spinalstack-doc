Frequently Asked Questions
==========================


How re-deploy a full infrastructure ? Do we need to re-deploy the install server or not ?
-----------------------------------------------------------------------------------------

In case you need to re-deploy an entire infrastructure it's recommended to re-install the install server. You will at least need to remove puppetDB data.


How can I add a new node in a Spinal Stack deployement ?
--------------------------------------------------------

In theory you just have to eDeploy the node and add it in your environment yaml. Then run puppet steps (all configure.sh steps).

In theory, you should have to update your environment YAML file containing node definition, and restart the jenkins jobs (edeploy and puppet).
In the reality, you have to take care at not rebooting existing nodes.
There still a lack of documentation about that and a warning about eDeploy job. We are not sure the job will/can reboot only one node actually (hosts.conf).
We really encourage you to test, document and open issue on this subject !


How upgrade an existing Spinal Stack infrastructure ?
-----------------------------------------------------

You can find a piece of documentation here : http://spinalstack.enovance.com/en/latest/deploy/upgrade.html

There is a Jenkins' job named `Upgrade` to take care of this task.


I'm deploying a stable Spinal Stack version where a bug has been fixed in current dev version. How can I proceed ?
------------------------------------------------------------------------------------------------------------------

The main idea is : don't stay on old Spinal Stack version. Upgrade as fast as you can. So this case ideally do not appear.

  * If it's a urgent security patch like security update, you can re-activate package manager and do it manually or using ansible.
  * In other cases, you can back-port manually Spinal Stack fix but be careful and take care of a developer advice. Keep in mind you can break future upgrade.

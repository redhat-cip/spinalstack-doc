Frequently Asked Questions
==========================


How re-deploy a full infrastructure ? Do we need to re-deploy the install server or not ?
-----------------------------------------------------------------------------------------

In case you need to redeploy an entire infrastructure it's recommended to reinstall the install server. But feel free to make pull requests for clean/reset an install-server feature. You will at least need to remove puppetDB data.


How can I add a new node in a SpinalStack deployement ?
-------------------------------------------------------

In theory you just have to eDeploy the node and add it in your environment yaml. Then run puppet steps (all configure.sh steps).
There still a leak of documentation about that and a warning about eDeploy job. We are not sure the job will/can reboot only one node actually (hosts.conf).
We really encourage you to test, document and open issue on this subject !


How upgrade an existing spinalstack infrastructure ?
----------------------------------------------------

You can find a piece of documentation here : http://spinalstack.enovance.com/en/latest/deploy/upgrade.html

There is a Jenkins' job named `Upgrade` to take care of this task.


I'm on SpinalStack I.1.1.0. I have a bug fixed on I.1.2.0. How can I back-port this patch ?
-------------------------------------------------------------------------------------------

The main idea is : don't stay on old SpinalStack version. Upgrade as fast as you can. So this case ideally do not appear.

  * If it's a urgent security patch like security update, you can re-activate package manager and do it manually or using ansible.
  * In other cases, you can back-port manually SpinalStack fix but be careful and take care of a developer advice. Keep in mind you can break future upgrade.
  * If you need to quickly fix things like a puppet class (can't wait a merge of pull request). It's not actually recommended but this case need to be discuss => Add new feature in SpinalStack config-tools (include all patches in patches directory or not ?)

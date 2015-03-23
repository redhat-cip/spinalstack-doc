Sensu
=====

Sensu_ is a modern monitoring system made for the cloud. It is the monitoring system installed by default with Spinal Stack.

Configuration
-------------

============================= =========================================================== ============================
Configuration                 Description                                                 Default
============================= =========================================================== ============================
sensu_api                     Enable HAProxy binding for the Sensu API                    true
sensu_api_ip                  IP address on which Sensu API will be listening             Internal Network IP
sensu_api_port                Port on which Sensu API will be listening                   4568
sensu_api_bind_options        HAProxy bind options for the Sensu API                      HAProxy default bind options
sensu_dashboard               Enable HAProxy binding for the Sensu Dashboard              true
sensu_dashboard_ip            IP address on which the Sensu Dashboard will be listening   Internal Network IP
sensu_dashboard_port          Port on which the Sensy Dashboard will be listening         3000
sensu_dashboard_bind_options  HAProxy bind options for the Sensu Dashboard                HAProxy default bind options
sensu_install_repo            Should puppet manage the sensu repo                         false
sensu_uchiwa_install_repo     Should puppet manage the uchiwa repo                        false
sensu_rabbitmq_user           Sensu Rabbitmq user                                         sensu
sensu_rabbitmq_password       Sensu Rabbitmq user's password                              rabbitpassword
sensu_rabbitmq_vhost          Sensu Rabbitmq vhost                                        /sensu
sensu_redis_host              Redis host Sensu will connect to                            Public Virtual IP
sensu_redis_port              Redis port Sensu will connect to                            6379
sensu_dashboard_user          Sensu Dashboard user name                                   admin
sensu_dasbhaord_password      Sensu Dashboard password                                    password
sensu_plugins                 Sensu list of plugins                                       {}
sensu_handlers                Sensu list of handlers                                      {}
sensu_checks                  Sensu list of checks                                        {}
============================= =========================================================== ============================


puppet-openstack-cloud
----------------------

* cloud::monitoring::server::sensu
* cloud::monitoring::agent::sensu


Import Plugins
--------------

If the check you want is not already installed on the machines, you can rely on the 'sensu_plugins' parameter to retrieve them in the environment file::

    sensu_plugins:
      'https://raw.githubusercontent.com/sensu/sensu-community-plugins/master/plugins/system/check-mem.sh':
       type: url

As we previously noted, the plugins will be dropped in '/etc/sensu/plugins'. You can apply the exact same pattern to retrieve handlers not present on the system::

    sensu_plugins:
      'https://raw.githubusercontent.com/sensu/sensu-community-plugins/master/handlers/notification/pagerduty.rb':
        type: url
        install_path: /etc/sensu/handlers/

Enable Checks
-------------

Checks are normally enabled per profile, but if you want some checks to be enabled for each and every node, the setting can be located in the environment file::

    sensu_checks:
      'check-mem' :
        command: '/etc/sensu/plugins/check-mem.sh -w 150 -c 100'
        subscribers: base
        standalone: false

Enable Handlers
---------------

Each handler will require its own configuration, to enable a handler edit the 'sensu_handlers' in the environment file::

    sensu_handlers:
      'handler_irc':
        command: '/etc/sensu/handlers/irc.rb'
        type: 'pipe'
        config:
          irc_server: 'irc://user:password@irc.freenode.net:6667/#mycompany'
          irc_ssl: false

.. _Sensu: https://www.sensuapp.org

Modifying Clearwater Settings
=============================

This page discusses how to change settings on a Clearwater system. Most
settings can be changed on an existing deployment (e.g. security keys
and details of external servers), but some are so integral to the system
(e.g. the SIP home domain) that the best way to change it is to recreate
the Clearwater deployment entirely.

Modifying Settings in /etc/clearwater/shared\_config
----------------------------------------------------

*This will have a service impact of up to five minutes.*

Settings in ``/etc/clearwater/shared_config`` can be safely changed
without entirely recreating the system. The one exception to this is the
``home_domain``; if you want to change this go to the "Starting from
scratch" section instead.

To change one of these settings, if you are using Clearwater's
`automatic configuration
sharing <Automatic_Clustering_Config_Sharing.html>`__ functionality:

-  Edit ``/etc/clearwater/shared_config`` on *one* node and change to
   the new value.
-  Run ``cw-upload_shared_config`` to upload the new config to etcd. The
   changes to the shared configuration are logged to ``/var/log/syslog``
   and to the console. Each node in your deployment picks up the changed
   shared configuration and safely restarts itself to use it.
-  You can check which nodes are using the new shared config by running
   ``cw-check_restart_queue_state``. If this command shows that there's
   been an error (i.e. a node wasn't able to restart after picking up
   the new config), simply fix the ``/etc/clearwater/shared_config`` and
   run the ``cw-upload_shared_config`` script again.

If you are not using automatic clustering, do the following on *each*
node:

-  Edit ``/etc/clearwater/shared_config`` and change the setting to the
   new value.
-  Run ``sudo service clearwater-infrastructure restart`` to ensure that
   the configuration changes are applied consistently across the node.
-  Restart the individual component by running the appropriate command
   below. This will stop the service and allow monit to restart it.

   -  Sprout - ``sudo service sprout quiesce``
   -  Bono - ``sudo service bono quiesce``
   -  Homestead -
      ``sudo service homestead stop && sudo service homestead-prov stop``
   -  Homer - ``sudo service homer stop``
   -  Ralf -``sudo service ralf stop``
   -  Ellis - ``sudo service ellis stop``
   -  Memento - ``sudo service memento stop``

Modifying Sprout JSON Configuration
-----------------------------------

*This configuration can be freely modified without impacting service.*

Some of the more complex sprout-specific configuration is stored in JSON
files

-  ``/etc/clearwater/s-cscf.json`` - contains information to allow the
   Sprout I-CSCF to select an appropriate S-CSCF to handle some
   requests.
-  ``/etc/clearwater/bgcf.json`` - contains routing rules for the Sprout
   BGCF.
-  ``/etc/clearwater/enum.json`` - contains ENUM rules when using
   file-based ENUM instead of an external ENUM server.

To change one of these files, if you are using Clearwater's `automatic
configuration sharing <Automatic_Clustering_Config_Sharing>`__
functionality:

-  Edit the file on *one* of your sprout nodes.
-  Run one of ``sudo cw-upload_{scscf|bgcf|enum}_json`` depending on
   which file you modified.
-  The change will be automatically propagated around the deployment and
   will start being used.

If you are not using automatic clustering do the following on *each*
node:

-  Make the necessary changes to the file.
-  Run ``sudo service sprout reload`` to make sprout pick up the
   changes.

Modifying DNS Config
--------------------

*This configuration can be freely modified without impacting service*

It's possible to add static overrides to DNS to a clearwater node. This
is done by adding entries to ``/etc/clearwater/dns_config``. Currently,
only CNAME records are supported and this is only used by Chronos.

To change this file, if you are using Clearwater's `automatic
configuration sharing <Automatic_Clustering_Config_Sharing>`__
functionality:

-  Edit the file on one of your nodes in each site.
-  Run
   ``sudo /usr/share/clearwater/clearwater-config-manager/scripts/upload_dns_config``.
-  The change will be automatically propagated around the site and will
   start being used.

If you are not using automatic clustering do the following on each
Vellum node:

-  Make the necessary changes to the file.
-  Run ``sudo service chronos reload-dns`` to make Chronos pick up the
   changes.

Starting from scratch
---------------------

*This will have a service impact of up to half an hour.*

If other settings (such as the Clearwater home domain) are being
changed, we recommend that users delete their old deployment and create
a new one from scratch, either `with
Chef <Creating_a_deployment_with_Chef.html>`__ or
`manually <Manual_Install.html>`__. This ensures that the new settings are
consistently applied.
